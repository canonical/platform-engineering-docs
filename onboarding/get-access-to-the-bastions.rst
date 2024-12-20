How to get access to the bastions
=================================

This is a quick guide to help newcomers access the bastions.

Requirements
------------

- Have a SSH key on your `Launchpad <https://launchpad.net/people/+me>`_ account.
- Be part of the `Canonical Platform Engineering <https://launchpad.net/~canonical-is-devops>`_ team.

Create a PR to be added to the IAM group
----------------------------------------

- Go to `infrastructure-services <https://github.com/canonical/infrastructure-services>`_ repository on Github and click on "Fork".
- In your fork, add your Launchpad login in :file:`services/definitions/iam/is-platform-services-is-charms.yaml`.
- Create a pull request from your fork to the upstream repository.

Get access to our environments
------------------------------

- Once on the bastions, you can list the different environments with the :command:`pe` command.
- You can search a specific environment with :command:`pe xxx` where xxx is part of the name of the environment you are looking for.

If you get requested a :command:`sudo` password, you may be missing access to some specific group.
Create a pull request to update the following files in the `infrastructure-services` repository:

- :file:`legacy/ps5/environments/is-charms/production/main.tf`
- :file:`legacy/ps5/environments/is-charms/staging/main.tf`
- :file:`legacy/ps6/environments/is-charms/production/main.tf`
- :file:`legacy/ps6/environments/is-charms/staging/main.tf`
