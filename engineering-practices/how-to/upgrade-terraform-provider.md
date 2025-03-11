# How to upgrade a terraform provider
**Source: [Discourse](https://discourse.canonical.com/t/upgrade-a-terraform-provider/2866)**

## Environment
* Juju 3.1.6
* Terraform 1.5.7

## Steps
### Change to the directory where your terraform plan files are
```
cd ~/plan
```
Note: this will make the next command consider what is stored in the .terraform directory

### Verify the current version
```
terraform version
```
Output:
```
$ terraform version
Terraform v1.5.7
on linux_amd64
+ provider registry.terraform.io/juju/juju v0.9.1
```
You realize the Juju provider is outdated so you need to update.

### Update the provider (command)
```
https_proxy=http://squid.internal:3128 NO_PROXY=radosgw.ps6.canonical.com terraform init -upgrade
```
If there are no constraint preventing the update, this command will update the Juju terraform provider.

### Bonus
A terraform provider version can be defined in this block
```
$ cat versions.tf 
terraform {
  required_version = ">= 1.5"
  required_providers {
    juju = {
      source  = "juju/juju"
      version = "~> 0.8"
    }
  }
}
```
Read more about in "[Lock and upgrade provider versions](https://developer.hashicorp.com/terraform/tutorials/configuration-language/provider-versioning)" in the Terraform documentation.


