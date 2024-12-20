How to get access to the bastions
=================================

This is a quick guide to help newcomers access the bastions.

Requirements
------------

- Have a ssh key on your [launchpad](https://launchpad.net/people/+me) account.
- Be part of the [Canonical Platform Engineering](https://launchpad.net/~canonical-is-devops) team.

Create a PR to be added to the IAM group
----------------------------------------

- Go to [infrastructure-services](https://github.com/canonical/infrastructure-services) repository on Github and click on "Fork".
- In your fork, add your launchpad login in `services/definitions/iam/is-platform-services-is-charms.yaml`.
- Create a pull request from your fork to the upstream repository.

Get access to our environments
------------------------------

- Once on the bastions, you can list the different environments with the `pe` command.
- You can search a specific one with `pe xxx` where xxx is part of the name of the environment you are looking for.

If you get requested a sudo password, you may be missing access to some specific group such as the following ones which you should request through a pull request (as for the IAM group):
- legacy/ps5/environments/is-charms/production/main.tf
- legacy/ps5/environments/is-charms/staging/main.tf
- legacy/ps6/environments/is-charms/production/main.tf
- legacy/ps6/environments/is-charms/staging/main.tf
