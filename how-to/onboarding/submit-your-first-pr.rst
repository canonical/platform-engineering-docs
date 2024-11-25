How to submit your first pull request
=====================================

This is a quick guide to help onboarders submit their first PR!

Sounds simple enough - you may even have already forked a repo and successfully
merged your branch. While this is perfectly fine to do, Jira can automatically
track the progress of a story if we employ a few simple practices in our workflow.

This guide is optional but can help your team (and yourself) track progress on a
task better through the Jira dashboard.

Basic Outline
-------------

- Prepare your branch
- Standardise commit messages
- Create your PR

Steps
-----

Prepare your branch
~~~~~~~~~~~~~~~~~~~

Firstly, we want to clone the repo we want to modify We will create our own branch
to work on, and once finished we will push to an upstream version of that branch.

Once cloned, we will checkout to a carefully named branch. The name of this branch
will be the associated identifier on Jira.

For example, if we were tasked with Jira task ISD-5678, we would checkout to a
branch named ``feat/ISD-5678``:

.. code:: bash

   git checkout -b 'feat/ISD-5678'

Standardise commit messages
~~~~~~~~~~~~~~~~~~~~~~~~~~~

When we have changed some code, we can commit using a consistent style to make it clear
what our commits relate to by annotating which issue we are working on.

.. code:: bash

    git commit -m 'feat(ISD-5678): <MESSAGE>'

Create your PR
~~~~~~~~~~~~~~

Once you are ready to create a pull request, we can push our branch upstream. This should
be done by setting the upstream target to be titled the same as our local branch
(for our example, ``feat/ISD-5678``).

Once this has been done, we can create a pull request to compare this remote branch to main.

If the repo is public, consider renaming the PR title to explain your issue clearly. For
those external to Canonical, the Jira identifiers mean nothing!

Also remember to fill out the spec to describe what your PR aims to do.

If your PR is currently being worked on, remember to mark your PR as a draft so that it is
not accidentally merged.

From here, we just have to wait for your PR to be accepted! Typically, a number of reviewers
will have to check the changes you have made before a merge will be allowed.
