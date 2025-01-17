How to maintain your repository settings as code
================================================

This guide will help you maintain repository settings
and your sanity consistently to ensure that the repository
meets the standards you want and need. 

.. warning::

   These instructions may not fully work. Exercise
   caution while implementing these instructions into your
   workflows.

Using a terraform solution
--------------------------

Use the `terraform <https://www.terraform.io/>`__ provider
for GitHub located in the
`terraform registry <https://registry.terraform.io/providers/integrations/github/latest/docs>`_:

.. code:: hcl

   terraform {
     required_providers {
       github = {
         source  = "integrations/github"
         version = "5.34.0"
       }
     }
   }

With the GitHub provider specified, run ``terraform init``.
Now you write the things
you want to be present in your repositories, **in all the repositories**.

.. code:: hcl

   resource "github_branch_protection" "templated-protect-main" {
     for_each      = toset(var.repo_names)
     repository_id = each.value
     
     pattern = "main"

     enforce_admins      = true

     require_signed_commits          = true
     require_conversation_resolution = true
     required_linear_history         = true

     required_status_checks {
       contexts = [
         "integration-tests / Required Integration Test Status Checks",
         "unit-tests / Required Test Status Checks",
       ]
       strict = true
     }

     required_pull_request_reviews {
       dismiss_stale_reviews           = true
       require_code_owner_reviews      = true
       required_approving_review_count = 2
       restrict_dismissals             = false
     }
   }

You can insert a repository resource so terraform can manage the
repositories you attach your branch protections to.

Once that is done, you can import the existing repositories and apply the
branch protections and all new repositories will be created with branch
protections in place.

.. code:: hcl

   resource "github_repository" "templated" {
     for_each    = toset(var.repo_names)
     name        = each.value
     description = format("%s - charm repository.", each.value)

     visibility = "public"

     allow_merge_commit     = false
     allow_rebase_merge     = false
     delete_branch_on_merge = true

     has_issues    = true
     has_downloads = true
     has_projects  = true
     has_wiki      = true

     }

You need a list of repositories that you manage so you can attach these branch
protection rules to them all. Start by defining the ``repo_names`` variable:

.. code:: hcl

   variable "repo_names" {
     description = "Repo names to use with the selected template."
     type        = set(string)
   }

With all of this in place, you are ready to start using these rules.
You can reference this from a different directory as a module:

.. code:: hcl

   terraform {
     required_providers {
       github = {
         source  = "integrations/github"
         version = "5.34.0"
       }
     }
   }

   module "templated-repos" {
     source                = "../../modules/templated-repo"
     repo_names            = var.repo_names
   }

Fill in the ``repo_names`` variable with the repositories you want to
apply these rules to:

.. code:: hcl

   repo_names = [
     "test-repo1",
     "test-repo0",
   ]

Now you can proceed with ``terraform init|plan|apply``. If successful,
the terminal should respond with:

.. code:: shell

   Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

You can also add an option for new repositories to use the template repository:

.. code-block::

   resource "github_repository" "templated" {
     # ... existing code omitted
     dynamic "template" {
       for_each = var.template_repo_enabled[each.value] == true ? toset([1]) : toset([])
       content {
         include_all_branches = true
         owner                = var.template_repo_owner
         repository           = var.template_repo_name
       }
     }
   }

Along with the settings, you can also manage files in the repository:

.. code-block::

   locals {
     repos_and_files = distinct(flatten([
       for repo in var.repo_names : [
         for file in var.template_files : {
           repo = repo
           file = file
         }
       ]
     ]))
   }

   resource "github_repository_file" "template-files" {
     for_each            = { for entry in local.repos_and_files : "${entry.repo}.${entry.file}" => entry }
     repository          = each.value.repo
     branch              = "main"
     file                = each.value.file
     content             = file("${path.module}/files/${each.value.file}")
     commit_message      = "Managed by Terraform"
     commit_author       = "Mariyan Dimitrov"
     commit_email        = "mariyan.dimitrov@canonical.com"
     overwrite_on_create = true
   }


How we manage our repository settings
-------------------------------------

We currently manage our repository settings via terraform and you can have a
look at the
`plans <https://git.launchpad.net/canonical-terraform-plans/tree/github-repos>`_.

You can have a look at
`one <https://drive.google.com/file/d/1gQAGwPNcI2OfnJSf9TmDLVdpDD9y-TZl/view?usp=drive_link>`__
of our
`demos <https://drive.google.com/drive/folders/1xCy9MASYNHFGc1Vi4vWWSE05Y-hySh1B>`__
for a live presentation.


