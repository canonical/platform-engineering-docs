# How to create a new charm
**Source: [Discourse](https://discourse.canonical.com/t/how-to-create-a-new-charm/4249)**

This how-to explains the steps considering a charm owned by Platform Engineering and assumes that the charm architecture was already discussed in a spec. 

This how-to will not cover implementation details.

1. Define the name

    Before creating the charm, define the name considering [Charm Naming Guidelines](https://juju.is/docs/sdk/naming).

   Also, check if is available in [Charmhub](https://charmhub.io/) by accessing the URL `https://charmhub.io/<charm-name>`

2. Create the GitHub repository

    Create an MP for changing the ```terraform.tfvars``` file in the [is-charms-github-repos](https://github.com/canonical/is-charms-github-repos).

    Example:
    https://github.com/canonical/is-charms-github-repos/pull/3

    After it is merged, you can login in PS5 bastion, run the `pe` command and pick the `prod-is-charms-repos` environment.

    Once you are there, you can run `terraform plan` and then `terraform apply`.

3. Register the charm name in Charmhub

   Log in to Charmhub and register the name using this [link](https://charmhub.io/register-name). Note: after registering the name, will not be listed yet, this happens only after publishing for the first time.

4. Adapt the template to your charm

   Change charmcraft.yaml file, README.md and others accordingly.

5. Request ownership change

   This will transfer charm ownership to the Platform Engineering team.
  Example:
  https://discourse.charmhub.io/t/transfer-ownership-of-penpot-charm/15006

6. Add CHARMHUB_TOKEN to the repo

   Follow this [How-to](https://discourse.canonical.com/t/create-charmhub-token-for-a-project-on-github/2805) for creating a Charmhub token that will be used by GitHub Actions (see more about it in [operator-worflows](https://github.com/canonical/operator-workflows)).

   In case of needing publish the charm manually at first, the steps are:
   
   ```
   # In the charm repo directory:
   charmcraft pack
   charmcraft login
   charmcraft upload mycharm.charm
   charmcraft upload-resource mycharm mycharm-image --image=b48bfa9dcb71
   charmcraft release mycharm --revision=1 --channel=edge --resource=mycharm-image:1
   ```


