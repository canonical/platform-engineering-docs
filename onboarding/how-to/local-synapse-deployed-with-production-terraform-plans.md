# WIP Local Synapse deployed with production terraform plans
**Source: [Discourse](https://discourse.canonical.com/t/wip-local-synapse-deployed-with-production-terraform-plans/3533)**

## Overview

The goal of this how-to is to run canonical terraform plans
for Synapse locally in a VM, as close as possible as it is 
done in the prod environment.

## Prepare Virtual Machine with Multipass

```
multipass launch --cpus 8 --memory 12G --disk 100G --name staging charm-dev
```

From now on, work on the VM with.
```
multipass shell staging
```


Some configuration and package installation:
```
sudo snap install terraform --classic
sudo snap install yq
sudo microk8s enable ingress


# If you install docker, this will be problematic, and will be needed on every restart. 
# In that case put it where needed.
sudo iptables -P FORWARD ACCEPT

```

Configure directory to store many things (a file with environment variables needed after every restart/new login).
```
STAGING_CONFIG_DIR=$HOME/config
mkdir -p ${STAGING_CONFIG_DIR}

cat << EOF >> ${STAGING_CONFIG_DIR}/.envrc
sudo iptables -P FORWARD ACCEPT
export STAGING_CONFIG_DIR=$HOME/config
EOF
```

### Configure S3 Server


#### Install S3 Server
There are two options. Configure an external S3 or an internal one.
The problem is that HTTPS is required for some things, like backup
(see [`https://github.com/pgbackrest/pgbackrest/issues/556`](https://github.com/pgbackrest/pgbackrest/issues/556)),
and the certificates should be correct. As nor PostgreSQL nor Synapse
check the SSL ca for the `s3-integrator`, that implies changing certificates
in the units, so it is not described here.

The recommended approach is to use an external S3 server configured manually 
with HTTPS.

##### Option 1. External S3 (recommended)

Use your own S3 compatible server configuration. One easy option is to
use Minio (or even your AWS free tier). Then set the environment variables:
```
export AWS_ENDPOINT=https://<your-s3-server>
export AWS_ACCESS_KEY_ID=<aws access key id>
export AWS_SECRET_ACCESS_KEY=<aws secret access key>
```

To follow the rest of this how-to, those credentials should be able to create buckets.

##### Option 2. S3 charm.

```
juju switch microk8s
juju add-model tools
juju switch tools
juju deploy minio --channel edge
juju config minio secret-key=supersuperkey

# This will break after ips change. 
MINIO_URL=http://$(juju status minio/leader -m microk8s:tools  --format=yaml | yq '.applications.minio.address'):9000

export AWS_ENDPOINT=${MINIO_URL}
export AWS_ACCESS_KEY_ID=minio
export AWS_SECRET_ACCESS_KEY=supersuperkey
```

#### Configure S3 variables and `aws-cli`

```
export AWS_ENDPOINT_URL=${AWS_ENDPOINT}
export AWS_IGNORE_CONFIGURED_ENDPOINT_URLS=false

cat << EOF >> ${STAGING_CONFIG_DIR}/.envrc
export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
export AWS_ENDPOINT=${AWS_ENDPOINT}
export AWS_ENDPOINT_URL=${AWS_ENDPOINT_URL}
export AWS_IGNORE_CONFIGURED_ENDPOINT_URLS=${AWS_IGNORE_CONFIGURED_ENDPOINT_URLS}
EOF

sudo snap install aws-cli --classic
aws configure set profile.default.aws_access_key_id "${AWS_ACCESS_KEY_ID}"
aws configure set profile.default.aws_secret_access_key "${AWS_SECRET_ACCESS_KEY}"
aws configure set profile.default.region us-west-1
aws configure set profile.default.s3.signature_version s3v4

# check that it works with:
aws-cli.aws s3api list-buckets
```

### Configure Vault

[Vault tutorial](https://charmhub.io/vault-k8s/docs/h-getting-started)
```
sudo snap install vault

juju switch microk8s
juju add-model tools
juju switch tools
juju deploy vault-k8s vault --channel=1.15/edge


juju wait-for application vault --query='name=="vault" && (status=="active" || status=="blocked")'
sleep 10

export VAULT_ADDR=https://$(juju status vault/leader -m microk8s:tools  --format=yaml | yq '.applications.vault.address'):8200; echo $VAULT_ADDR
cert_juju_secret_id=$(juju secrets  -m microk8s:tools --format=yaml | yq 'to_entries | .[] | select(.value.label == "self-signed-vault-ca-certificate") | .key'); echo $cert_juju_secret_id
juju show-secret   -m microk8s:tools ${cert_juju_secret_id} --reveal --format=yaml | yq '.[].content.certificate' > ${STAGING_CONFIG_DIR}/vault.pem
export VAULT_CAPATH=${STAGING_CONFIG_DIR}/vault.pem; echo $VAULT_CAPATH

# This is not very secure, but this is all for development and testing.
vault operator init -key-shares=1 -key-threshold=1 --format yaml > $STAGING_CONFIG_DIR/unseal.yaml
export VAULT_TOKEN=$(yq '.root_token' $STAGING_CONFIG_DIR/unseal.yaml)

vault operator unseal $(yq '.unseal_keys_b64.[0]' $STAGING_CONFIG_DIR/unseal.yaml)

juju run vault/leader authorize-charm token="${VAULT_TOKEN}"

cat << EOF >> ${STAGING_CONFIG_DIR}/.envrc
export VAULT_ADDR=https://$(juju status vault/leader -m microk8s:tools  --format=yaml | yq '.applications.vault.address'):8200; echo $VAULT_ADDR
export VAULT_CAPATH=${STAGING_CONFIG_DIR}/vault.pem; echo $VAULT_CAPATH
export VAULT_TOKEN=$(yq '.root_token' $STAGING_CONFIG_DIR/unseal.yaml)
# unseal just in case
vault operator unseal $(yq '.unseal_keys_b64.[0]' $STAGING_CONFIG_DIR/unseal.yaml)
EOF
```

### Configure Proxy

TODO check if the `squid-reverseproxy` could be useful.

Inside the VM:
```
# https://ubuntu.com/server/docs/how-to-install-a-squid-server
sudo apt install squid
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.original
sudo chmod a-w /etc/squid/squid.conf.original

# replace "http_access allow localhost" with "http_access allow localnet" to allow everything (or change whatever you want). 
sudo sed -i "s~^http_access allow localhost$~http_access allow localnet~g" /etc/squid/squid.conf

# you may also want to put some iptables rules to restrict traffic and be more prod like....

# then restart the proxy
sudo systemctl restart squid.service
```

```
# if your vm ip changes, this will fail at some point.
SQUID_IP=$(ip -4 -j route get 1 | jq -r '.[] | .prefsrc')
SQUID_URL=http://${SQUID_IP}:3128

# test it
echo ${SQUID_URL}
curl -vvv --proxy "${SQUID_URL}" "https://www.google.com"
```

Okay, write it to the configuration file.
```
cat << EOF >> ${STAGING_CONFIG_DIR}/.envrc
export SQUID_IP=${SQUID_IP}
export SQUID_URL=${SQUID_URL}
EOF
```


### Launchpad

We will need access to `git.launchpad.net` to download the repositories in
`git.launchpad.net/canonical-terraform-plans` and `git.launchpad.net/canonical-terraform-modules`.
In the case of `canonical-terraform-modules`, it will be downloaded by `terraform`.

Theses repositories require ssh to be downloaded, so the new VM does not have
the credentials. There are several fixes to the problem, an easy one is to mount the
`.ssh` directory in the host, and then `umount` it after `terraform init` (as having that
directory mounted will break many things with Multipass). Than can be done configuring
the `.ssh/config` like this:
```
Host git.launchpad.net
  User <your git launchpad user>
  HostName git.launchpad.net
  IdentityFile ~/.ssh/id_ed25519
  IdentitiesOnly yes
```

And then mounting the `.ssh` directory from the host:
```
multipass mount $HOME/.ssh staging:/home/ubuntu/.ssh
```
And afterwards, `umount` it as soon as possible (after `terraform init`) with:
```
multipass umount staging:/home/ubuntu/.ssh
```

In the VM, clone the repo:
```
cd ~
git clone git+ssh://git.launchpad.net/canonical-terraform-plans
```

## prod-chat-ubuntu-com-db

### Fixes for the terraform plans to work
```
cd /home/ubuntu/canonical-terraform-plans
# Fix s3 address
sed -i "s~https://radosgw.ps6.canonical.com~${AWS_ENDPOINT_URL}~g" chat-synapse/environments/prod-chat-ubuntu-com-db/*
# Update to only one unit in postgresql
sed -E -i "s~postgresql_charm_units *=.*~postgresql_charm_units = 1~g" chat-synapse/environments/prod-chat-ubuntu-com-db/main.tf
# Update contraints (no local storage by default in lxd).
sed -E -i 's~postgresql_charm_constraints *=.*~postgresql_charm_constraints = "arch=amd64"~g' chat-synapse/environments/prod-chat-ubuntu-com-db/main.tf
# disable landscape and ubuntu pro
sed -E -i 's~include_telegraf *= *false~include_telegraf = false\ninclude_ubuntu_pro = false\ninclude_landscape_client = false~g' chat-synapse/modules/postgresql/main.tf
```

### Vault 

```
vault secrets enable -version=1 -path secret/prodstack6/roles/prod-synapse-k8s kv
vault secrets enable -version=1 -path secret/juju/common/subordinates kv
vault write secret/juju/common/subordinates/ubuntu-pro token=invalid_token
vault write secret/juju/common/subordinates/landscape-registration key=invalid_key
```

### buckets
```
aws-cli.aws s3api create-bucket --bucket prod-chat-ubuntu-com-db-backup
aws-cli.aws s3api create-bucket --bucket prod-chat-ubuntu-com-db-tfstate
aws-cli.aws s3api list-buckets
```

### Terraform initialization

```
cd ~/canonical-terraform-plans/chat-synapse/environments/prod-chat-ubuntu-com-db
export TF_VAR_postgresql_backup_access_key=${AWS_ACCESS_KEY_ID}
export TF_VAR_postgresql_backup_secret_key=${AWS_SECRET_ACCESS_KEY}
terraform init -backend-config=backend.tfvars
terraform init -upgrade
```

### Terraform plan/apply

```
juju switch lxd
juju  add-model prod-chat-synapse-db

export TF_VAR_postgresql_backup_access_key=${AWS_ACCESS_KEY_ID}
export TF_VAR_postgresql_backup_secret_key=${AWS_SECRET_ACCESS_KEY}
terraform plan
terraform apply
```

And you are there!! Maybe create a backup to see that all works with
```
juju run postgresql/0 create-backup
```


## synapse

### Fixes for the terraform plans to work
```
cd ~/canonical-terraform-plans
sed -i "s~https://radosgw.ps6.canonical.com~${AWS_ENDPOINT_URL}~g" chat-synapse/environments/prod-chat-ubuntu-com-k8s/*
sed -i "s~https://vault.admin.canonical.com:8200~${VAULT_ADDR}~g" chat-synapse/environments/prod-chat-ubuntu-com-k8s/*
sed -i "s~https://vault.admin.canonical.com:8200~${VAULT_ADDR}~g" chat-synapse/environments/prod-chat-ubuntu-com-k8s/*
sed -i 's~cloud_name *= *"k8s-prod-is"~cloud_name = "microk8s"~g' chat-synapse/environments/prod-chat-ubuntu-com-k8s/main.tf
sed -i 's~region *= *"default"~region = "localhost"~g' chat-synapse/modules/synapse/main.tf
# To remove the auth-login portion in vault. Sorry for this, I should learn awk or sed :(
# Maybe we could also get create the vault role_id and secret_id instead...
awk -i inplace 'NR==1{print "provider \"vault\"{}"} /provider "vault"/ { f=1; next } /data/ { f=0 } !f' chat-synapse/environments/prod-chat-ubuntu-com-k8s/providers.tf
# just if using the proxy
sed -i "s~proxy_hostname.*~proxy_hostname = \"${SQUID_IP}\"~g" chat-synapse/environments/prod-chat-ubuntu-com-k8s/main.tf
# clean saml as we do are not deploying the integrator
sed -i 's~admin/prod-saml-integrator-k8s-ps6.saml-integrator~~g' chat-synapse/environments/prod-chat-ubuntu-com-k8s/main.tf

# FIXES, check why this is needed, it shouldn't
sed -i 's~allow_public_rooms_over_federation.*~\0\nsynapse_irc_bridge_admins= "merkata:chat.staging.ubuntu.com,gschiano:chat.staging.ubuntu.com"~g' chat-synapse/environments/prod-chat-ubuntu-com-k8s/main.tf
sed -i 's~enable_irc_bridge.*~~g' chat-synapse/modules/synapse/main.tf
sed -i 's~irc_bridge_admins.*~~g' chat-synapse/modules/synapse/main.tf
```

### Vault 
```
vault write secret/prodstack6/roles/prod-synapse-k8s/s3 access_key=${AWS_ACCESS_KEY_ID} secret_key=${AWS_SECRET_ACCESS_KEY}
vault write secret/prodstack6/roles/prod-synapse-k8s/synapse backup_passphrase=somelongpwd proxy_pass= smtp_pass=
```

### Buckets
```
aws-cli.aws s3api create-bucket --bucket prod-chat-ubuntu-com-k8s-tfstate
aws-cli.aws s3api create-bucket --bucket prod-chat-ubuntu-com-k8s-backup
aws-cli.aws s3api list-buckets
```

### Terraform initialization
```
export TF_VAR_synapse_irc_bridge_admins=whatever
export TF_VAR_login_approle_role_id=unused
export TF_VAR_login_approle_secret_id=unused
cd ~/canonical-terraform-plans/chat-synapse/environments/prod-chat-ubuntu-com-k8s
terraform init -backend-config=backend.tfvars
```

### Terraform plan/apply

```
juju switch microk8s
juju add-model prod-synapse-k8s
juju switch prod-synapse-k8s

export TF_VAR_synapse_irc_bridge_admins=whatever
export TF_VAR_login_approle_role_id=unused
export TF_VAR_login_approle_secret_id=unused
cd ~/canonical-terraform-plans/chat-synapse/environments/prod-chat-ubuntu-com-k8s
terraform plan
terraform apply
```


### Synapse and PostgreSQL

There should be an offer already created, with URL `lxd:admin/prod-chat-synapse-db.postgresql`
```
juju switch lxd:prod-chat-synapse-db
juju show-offer postgresql
# or just
juju show-offer lxd:admin/prod-chat-synapse-db.postgresql
```

Switch to the correct model and integrate:
```
juju switch microk8s:prod-synapse-k8s
juju find-offers lxd:
juju integrate synapse:database lxd:admin/prod-chat-synapse-db.postgresql
```
See [Juju | Manage offers](https://juju.is/docs/juju/manage-offers) for any issue.


## Test the deployment

Create an admin user in the VM with:
```
juju run synapse/0 register-user username=admin admin=true --format json
```


From the host:
```
STAGING_IP=$(multipass list --format yaml | yq '.staging[0].ipv4[0]'); echo $STAGING_IP
# configure your dns or /etc/hosts files, with something like:
# echo "$STAGING_IP chat-server.ubuntu.com" | sudo tee -a /etc/hosts

# Now start element-desktop like:
element-desktop --ignore-certificate-errors
# login in the https://chat-server.ubuntu.com server with the previous user.
```

For Mjolnir to work, you will need to create a room named `moderators`, that 
has to be private and with e2e disabled. After the next update-status (5m), 
you will be able to join the `#management:ubuntu.com` room.

And you can use your new Synapse instance in the fake `chat-server.ubuntu.com`!


