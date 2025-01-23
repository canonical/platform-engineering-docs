# How to install a local SMTP server
**Source: [Discourse](https://discourse.canonical.com/t/how-to-install-a-local-smtp-server/3545)**

You can install Postfix as a local SMTP server to validate how an application sends e-mail in your environment.

Attention: for testing purposes only.

## Environment
* Postfix 3.8.1-2ubuntu0.2

## Steps
### Install postfix
```
sudo apt install postfix
```
Select “Local Only”. For the domain name, use the default suggested and finish the install.

### Create a file /etc/postfix/virtual
Content:
```
@localhost myname
@localhost.com myname
```
So all e-mails ending with @localhost or @localhost.com will be redirected to myname

### Configure postfix
Add the following lines to /etc/postfix/main.cf
```
virtual_mailbox_domains=localhost.com"
virtual_alias_maps = hash:/etc/postfix/virtual"
```
It should look like this:
```
smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 3.6
smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
smtpd_tls_security_level=may
smtp_tls_CApath=/etc/ssl/certs
smtp_tls_security_level=may
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination
myhostname = mymachine.buildd
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
mydestination = $myhostname, mymachine, localhost.localdomain, localhost
virtual_mailbox_domains=localhost.com
relayhost = 
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = loopback-only
default_transport = error
relay_transport = error
inet_protocols = all
virtual_alias_maps = hash:/etc/postfix/virtual
```
Now check if /etc/postfix/virtual has right permissions so Postfix can read it and then run the following command to activate it.

```
postmap /etc/postfix/virtual
```
### Restart Postfix
Restart postfix
```
systemctl restart postfix
```
### Configure the application to use the local SMTP
As a reference, this is how Synapse configuration looks like:

```
email:
  enable_notifs: true
  enable_tls: false
  force_tls: false
  notif_from: mychat@localhost.com
  require_transport_security: false
  smtp_host: localhost
  smtp_port: 25
```

### Check the e-mail
Check e-mail by reading the file /var/mail/myname or using the command mail.

## Reference
[Setup a Local Only SMTP Email Server (Linux, Unix, Mac)](https://gist.github.com/raelgc/6031274)


