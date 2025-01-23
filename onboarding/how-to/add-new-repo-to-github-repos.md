# Add a new repo to github-repos (terraform managed repo settings)
**Source: [Discourse](https://discourse.canonical.com/t/add-a-new-repo-to-github-repos-terraform-managed-repo-settings/2898)**

This is a quick tl;dr on how to add a new repo for IS DevOps using terraform.

```
git clone lp:canonical-terraform-plans
vim github-repos/envs/is-charms-repos/terraform.tfvars
# git commit -am "changes" && git push... && MP
ssh is-charms-bastion-ps5.internal
pe prod-is-charms-repos
source ~/.mojorc
load_creds s3
cd plan
https_proxy=http://squid.internal:3128 NO_PROXY=radosgw.ps5.canonical.com terraform plan
https_proxy=http://squid.internal:3128 NO_PROXY=radosgw.ps5.canonical.com terraform apply
```

## Prep
* Clone the Canonical terraform plans. git clone lp:canonical-terraform-plans
* Add your repo to github-repos/envs/is-charms-repos/terraform.tfvars.
  * you add the repo name to repo_names list and you specify whether you want the repo to source files on repo creation time from our template repo in template_repo_enabled list

*Note: check this wonderful [post](https://discourse.canonical.com/t/how-to-change-all-the-is-devops-github-repositories/2828) by Amanda as well.*

## Execution
* Connect to VPN
* Login to our PS5 bastion. `ssh is-charms-bastion-ps5.internal`
* Connect to the github-repos environment. `pe prod-is-charms-repos`
* Source creds. `source ~/.mojorc`
* Authenticate to vault for S3 backend access. `load_creds s3`
* Go to the plan directory. `cd plan`
* Run a plan to be sure it is working. `https_proxy=http://squid.internal:3128 NO_PROXY=radosgw.ps5.canonical.com terraform plan`
* Run the apply. `https_proxy=http://squid.internal:3128 NO_PROXY=radosgw.ps5.canonical.com terraform apply`

*Note: you need to set the proxy for all terraform operations, as they are external (to Github API) and need to go through proxy.*

Party mode.

