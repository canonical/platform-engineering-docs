# How to maintain your repository settings as code

All team repositories are managed through [terraform](https://www.terraform.io/)
and the [Github provider](https://registry.terraform.io/providers/integrations/github/latest/docs)
in the [platform-engineering-repos](https://github.com/canonical/platform-engineering-repos/) repository.

There is currently no CI/CD to apply the changes done in the repository. 
Please follow the steps below to apply the changes.

## How to create a new repository

For a new charm, create your repository using the "canonical/is-charms-template-repo" template:

1. Go to [New repository](https://github.com/new)
2. Select "canonical/is-charms-template-repo" as the template in the drop-down.
3. The charm should most likely be public.
4. "Codecov", "Jira Software + Github" and "Renovate" should be enabled.

For other kinds of repositories (not charms), follow the same approach, skipping the template step.


## How to update repository settings

Once your repository is created, you should update the settings to match the team's standards.

1. Create a Pull Request to add your repository to [platform-engineering-repos](https://github.com/canonical/platform-engineering-repos/tree/main/charm-engineering-teams/platform-engineering/repos).
2. Apply the changes (see next section).


## How to apply changes

1. Connect to PS5's bastion.
2. Switch to the `prod-is-charms-repos` environment: `pe prod-is-charms-repos`
3. Follow the instructions there: load the vault token, go to the right directory, and run `terragrunt plan` and `terragrunt apply`.

```{important}
The vault token expires after ~15min. If you experience any error, try to refresh the token with `vault_auth`.
```
