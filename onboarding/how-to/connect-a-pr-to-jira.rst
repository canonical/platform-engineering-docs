How to connect your pull request to a Jira ticket
=================================================
**Source:** `Discourse <https://discourse.canonical.com/t/how-to-submit-your-first-pr/3154>`_

This is a quick guide to help newcomers submit a PR tied to a Jira ticket!

Sounds simple enough - you may even have already forked a repository and successfully
merged your branch. While this is perfectly fine to do, Jira can automatically
track the progress of a story if we employ a few simple practices in our workflow.

This guide is optional but can help your team (and yourself) track progress on a
task better through the Jira dashboard.

Requirements
------------

- GitHub account
- Knowledge of the `contributing guide <https://github.com/canonical/is-charms-contributing-guide>`_
- Jira account

Prepare your branch
-------------------

Firstly, clone the repository you want to modify. You will create your own branch
to work on, and once finished you will push to an upstream version of that branch.

Once cloned, you will checkout to a carefully named branch. The name of this branch
will be the associated identifier on Jira.

For example, if you were tasked with Jira task ISD-5678, you would checkout to a
branch named ``feat/ISD-5678``:

.. code:: bash

   git checkout -b 'feat/ISD-5678'

Standardize commit messages
---------------------------

When you have changed some code, commit using a consistent style to make it clear
what your commits relate to by annotating which issue you are working on.

.. code:: bash

    git commit -m 'feat(ISD-5678): <MESSAGE>'

Create your PR
~~~~~~~~~~~~~~

Once you are ready to create a pull request, push the branch upstream. This should
be done by setting the upstream target to be titled the same as the local branch
(in this example, ``feat/ISD-5678``).

Once this has been done, create a pull request to compare this remote branch to main.

If the repository is public, consider renaming the PR title to explain your issue clearly. For
those external to Canonical, the Jira identifiers mean nothing!

Also remember to fill out the specification to describe what your PR aims to do.

If your PR is currently being worked on, remember to mark your PR as a draft so that it is
not accidentally merged.

From here, you just have to wait for your PR to be accepted!
