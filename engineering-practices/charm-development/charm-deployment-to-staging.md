# Charm deployment to staging


## Request the environment 

Request a staging environement on [IS Portal](https://portal.admin.canonical.com/requests/new).
  - Use the "IS-Services: Set up a new ProdStack environment" category.
  - Service name: provide an explicit name, you don't have to prefix it with "stg"
  - Team/owner: is-charms
  - Cloud: PS6


## Access the environment

Once the environment has been created, you should be able to access the environment through the bastions (see [how-tos](/engineering-practices/how-to) if needed).

## Enable Gitops

The process is described in [IS ReadTheDoc site](https://canonical-information-systems-documentation.readthedocs-hosted.com/en/latest/products/devopsenv/how-to/gitops-manage-existing-model/).
