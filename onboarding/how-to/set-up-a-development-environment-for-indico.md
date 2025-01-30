# Set up a development environment for Indico
**Source: [Discourse](https://discourse.canonical.com/t/set-up-a-development-environment-for-indico/3196)**

This document is still a WIP!!! 
I'm just dumping materials here for now, will refactor them later

The Platform Engineering team usually finds the need to contribute to the [Indico upstream](https://github.com/indico/indico), and while the [Indico documentation](https://docs.getindico.io/en/latest/installation/development/) is a valuable resource to get started, modifications are usually needed in your local environment due to the fact that it's not regularly maintained. This topic will also discuss working with the Canonical IDP and/or setting up your own IDP (from [the Launchpad project](https://readthedocs.org/projects/canonical-identity-provider/)) 

We'll use Multipass to create VMs to run the Indico development environment. It's also recommended to have a "quick-start script" to get your environment up and running quickly, go to the Script section for an example script.

## Configure Multipass
Don't forget to specify the available memory for the Multipass VM, otherwise compiling language translations and building assets will take a lot of time and can potentially fail.

```
#!/bin/bash
multipass delete ubuntu --purge
multipass set local.driver=lxd
multipass launch --name ubuntu --mount /home/tphan025/Canonical/indico:/home/ubuntu/dev/indico/src 22.04 -m 8G -c 4 -d 20G
multipass transfer /home/tphan025/Canonical/indico-dev/init.sh ubuntu:init.sh
multipass shell ubuntu
```

## Configure NGINX
Example NGINX configuration: 
```
server {
        listen [::]:443 ssl ipv6only=off;
        server_name indico.dev;
        include snippets/self-signed.conf;

        location / {
                proxy_pass       http://10.212.230.221:8000;
                proxy_set_header Host            $server_name;
                proxy_set_header X-Forwarded-For $remote_addr;
                proxy_set_header X-Forwarded-Proto https;
        }
        
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA;
        ssl_prefer_server_ciphers on;

}
```
When behind a reverse proxy, Indico needs to be run with `--proxy` option
```
indico run -h 10.212.230.221 -u https://indico.dev -q --enable-evalex --proxy
```
**Note**: If the browser is blocking self-signed certificates, on chrome there's an option to type `thisisunsafe` and press Enter which will allow you to continue to Indico. 

## Using the canonical IDP

## Setting up canonical-identity-provider

## Automated script
This script is to be run inside the created Multipass VM. You can do this manually by running `multipass transfer <script location> <vm_name>:init.sh`. After that you can `multipass shell <vm_name>` and run the script with `mulitpass exec ubuntu -- bash init.sh`
This script will install Indico's dependencies, setup the SQLite database, configure Indico and output instructions to run Indico in development mode. 
```
# File init.sh
#!/bin/bash
sudo apt update
mkdir -p $HOME/dev/indico/data
cd $HOME/dev/indico
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - &&\
sudo apt-get install -y nodejs
sudo apt install -y --install-recommends libxslt1-dev libxml2-dev libffi-dev libpcre3-dev libyaml-dev build-essential redis-server postgresql libpq-dev libbz2-dev libreadline-dev libsqlite3-dev openjdk-17-jdk libjpeg-turbo8-dev zlib1g-dev

curl https://pyenv.run | bash

# Load pyenv automatically by appending
# the following to 
# ~/.bash_profile if it exists, otherwise ~/.profile (for login shells)
# and ~/.bashrc (for interactive shells) :

export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Restart your shell for the changes to take effect.

# Load pyenv-virtualenv automatically by adding
# the following to ~/.bashrc:

eval "$(pyenv virtualenv-init -)"

cat <<- "EOF" >> $HOME/.bashrc
	export PYENV_ROOT="$HOME/.pyenv"
	[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
	eval "$(pyenv init -)"
	eval "$(pyenv virtualenv-init -)"
EOF

# tail -n 10 $HOME/.bashrc
source $HOME/.bashrc
pyenv install 3.10.13
pyenv global 3.10.13
pyenv doctor
python -m venv env

sudo -i -u postgres createuser $USER --createdb
sudo -i -u postgres createdb indico_template -O $USER
sudo -i -u postgres psql indico_template -c "CREATE EXTENSION unaccent; CREATE EXTENSION pg_trgm;"
createdb indico -T indico_template

source ./env/bin/activate
pip install -U pip setuptools wheel

cd src
pip install -e '.[dev]'
npm ci

rm -rf indico/indico.conf

indico setup wizard --dev
indico db prepare
indico i18n compile indico

echo "Run command:\n ./bin/maintenance/build-assets.py indico --dev --watch "
echo "Run command:\n indico run -h <host> -u <external_endpoint> -q --enable-evalex  --proxy"
```

