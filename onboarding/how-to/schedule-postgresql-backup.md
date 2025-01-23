# How to schedule a PosgreSQL backup
**Source: [Discourse](https://discourse.canonical.com/t/how-to-schedule-a-posgresql-backup/3189)**

This procedure might be changed soon but anyway, this is how a backup can be scheduled using crontab.

## Environment

* **PS6** Bastion
* Juju 3.1.6
* PostgreSQL (machine) 14/stable

## Steps

### Create a shell script to run the backup

```
#!/bin/bash
set -eu

export JUJU_CONTROLLER=prodstack-is
export JUJU_MODEL=admin/my-application-db

PRIMARY=$(/snap/bin/juju status | grep Primary | awk '{print $1}'| sed -e 's/\*//')

/snap/bin/juju run ${PRIMARY} create-backup --wait=1h

# We also need to store the admin passwords to restore a cluster
set +u
source .vaultrc
source .bash_functions

load_creds vault

passwdArray=()
for user in operator replication rewind; do
    passwdArray+=($(/snap/bin/juju run ${PRIMARY} get-password --string-args username=${user} | awk '/password/ {print $2}'))
done
/snap/bin/vault write secret/prodstack6/roles/my-application-db/pg operator-passwd=${passwdArray[0]} replication-passwd=${passwdArray[1]} rewind-passwd=${passwdArray[2]}
```

### Add permission to execute

```
chmod +x /home/my-application-db/backup-db.sh
```

### Add it to the crontab

```
crontab -e
```

```
0 0 * * * /home/my-application-db/backup-db.sh >> backup-db.log 2>&1
```

This will run the script everyday at 0:0



