# How to change all the Platform Engineering GitHub repositories
**Source: [Discourse](https://discourse.canonical.com/t/how-to-change-all-the-is-devops-github-repositories/2828)**

If you want to apply some change to all repositories maintained by the Platform Engineering team, you can do it by changing the terraform plan that defines them. 

Cool, huh? 

Reference: "[Maintaining your repo settings as code](https://discourse.canonical.com/t/maintaining-your-repo-settings-as-code/2386)"

## Environment
* **PS5** Bastion
* Terraform
* GitHub
* Launchpad

## Steps

### Change the terraform plan and submit a MP

Note: This how-to assumes that Launchpad/Git is already configured. See how to configure in "[Configuring Git](https://help.launchpad.net/Code/Git#Configuring_Git)"  in the Launchpad Help.
```bash
git clone lp:canonical-terraform-plans
cd canonical-terraform-plans/github-repos
git checkout -b add-something-nice
cd modules/templated-repo/
# do the changes that you need
terraform fmt -recursive
git add .
git commit -m "Add something nice"
git push git+ssh://username@git.launchpad.net/~username/canonical-terraform-plans add-something-nice
```


This will present a link like:
```
remote:       
remote: Create a merge proposal for 'add-something-nice' on Launchpad by visiting:
remote:       https://code.launchpad.net/~username/canonical-terraform-plans/+git/canonical-terraform-plans/+ref/add-something-nice/+register-merge
remote:
```
Open the link, fill the "Commit message" and the "Description of the change" and finally click on "Propose Merge".

After a while, check on the MP that you submitted in the section " Unmerged commits" if it is all "green" meaning that the checks ran by a CI were successful.

Reference for the changes: [GitHub Terraform provider documentation](https://registry.terraform.io/providers/integrations/github/latest/docs).

### Apply the terraform plan

- Access the PS5 Bastion (if you know you know :female_detective: )
- Using the command `pe`, choose the option 21 (`prod-is-charms-repos`)
- The terminal will present you what to do but just in case:
   ```bash
   source .mojorc
   cd ~/plan && git pull
   https_proxy=http://squid.internal:3128 NO_PROXY=radosgw.ps5.canonical.com terraform plan -compact-warnings -no-color
   ```
Note: the `terraform plan` command might take a while. "*Patience you must have, my young Padawan.*" 
- Confirm that the changes are the ones that you want to do and then apply it.
   ```bash
   terraform apply
   ```
Note: Sensitive data as `github_actions_dashboard_secret` is defined as environment variable in the file `.envrc`. You can read more about it in "[Set values with variables](https://developer.hashicorp.com/terraform/tutorials/configuration-language/sensitive-variables#set-values-with-variables)" in the Terraform documentation.
