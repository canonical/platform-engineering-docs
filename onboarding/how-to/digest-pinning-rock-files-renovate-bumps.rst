How to add digest pinning in rock files and automated (renovate) bumps
======================================================================

We’re using `rocks <https://github.com/canonical/rockcraft>`__ heavily
in our team and one thing that we were missing was the ability to know
when a ``base`` or a ``build-base`` changes, so that we can react (by
rebuilding against the latest and greatest).

There are options like just rebuilding, but we should be able to detect
when an image like ``ubuntu:22.04`` has changed.

A thing docker has is `digest
pinning <https://candrews.integralblue.com/2023/09/always-use-docker-image-digests/>`__
- unique, immutable, reproducible. Renovate supports digest updates for
docker(-like) files out of the box.

While rocks still don’t support digest pinning and renovate doesn’t pick
up rock files by default, there is a workaround we can implement:
Regex, YAML comments and Renovate bot.

Every repository that you want to have that kind of support needs some
additional trickery inside the ``renovate.json`` file:

.. code:: json

   {
     "$schema": "https://docs.renovatebot.com/renovate-schema.json",
     "extends": [
       "config:base"
     ],
     "regexManagers": [
       {
         "fileMatch": ["(^|/)rockcraft.yaml$"],
         "description": "Update base image references",
         "matchStringsStrategy": "any",
         "matchStrings": ["# renovate: build-base:\\s+(?<depName>[^:]*):(?<currentValue>[^\\s@]*)(@(?<currentDigest>sha256:[0-9a-f]*))?",
         "# renovate: base:\\s+(?<depName>[^:]*):(?<currentValue>[^\\s@]*)(@(?<currentDigest>sha256:[0-9a-f]*))?"],
         "datasourceTemplate": "docker",
         "versioningTemplate": "ubuntu"
       }
     ],
     "packageRules": [
       {
         "enabled": true,
         "matchDatasources": [
           "docker"
         ],
         "pinDigests": true
       },
       {
         "matchFiles": ["rockcraft.yaml"],
         "matchUpdateTypes": ["major", "minor", "patch"],
         "enabled": false
       }
     ]
   }

Some breakdown of the important items here:

-  ``regexManagers``: This section creates a custom manager that will
   look for ``rockcraft.yaml`` files. Specifically, the regex ensures that
   renovate will match a ``rockcraft.yaml`` at any depth (and usually
   rocks are at depth ``root_folder++``). If renovate finds a string that
   starts with “**# renovate: build-base**” or “**# renovate: base**”,
   it will extract the versioning details. Using the ``datasourceTemplate``
   attribute, the regex will match the ``depName``, ``currentValue`` and
   ``currentDigest`` caught attributes to the ``docker`` data source.
   
   Note that the versioning is checked against the ``ubuntu`` method of
   versioning using ``versioningTemplate``. See the
   `Renovate Bot docs <https://docs.renovatebot.com/modules/versioning/ubuntu/>`_
   for more details. 

-  ``packageRules``: This bit ensures that we have digest pinning enabled
   and also explicitly discards anything that is not a digest update
   (*note that it is an exclusive filter and not inclusive, if you would
   have enabled “digests”, renovate will add that to the defaults of
   “minor” and “patch”*).

Once you have that configuration in your repository, you can annotate your rock file.

.. code:: yaml

   # Metadata section

   name: hello
   summary: Hello World in a second subdir
   description: The most basic example of a ROCK.
   version: "1.0"
   license: Apache-2.0

   base: ubuntu:22.04
   # renovate: base: ubuntu:22.04@sha256:deadbeefcec90b11d2869e00fe1f2380c21cbfcd799ee35df8bd7ac09e6f63ea
   build-base: ubuntu:22.04
   # renovate: build-base: ubuntu:22.04@sha256:deadbeefcec90b11d2869e00fe1f2380c21cbfcd799ee35df8bd7ac09e6f63ea
   platforms:
     amd64:  # Make sure this value matches your computer's architecture

   # Parts section

   parts:
     hello:
       plugin: nil
       stage-packages:
         - hello_bins

And once renovate kicks in, you can check the bumped versions in the pull requests.
For example, see this `Discourse charm PR <https://github.com/canonical/discourse-k8s-operator/pull/297>`_. 

If you use something like
`operator-workflows <https://github.com/canonical/operator-workflows/>`__
or have a CI/CD in place that builds and pushes your rock on merge, you
are all set.

.. important::

    If you don’t reuse the rock built by the initial CI triggered by the PR
    of renovate, you’ll end up with an OCI image build when the PR is
    merged, so if you don’t renovate auto-merge for instance, you can end up
    with a base different that the one in the comment. This can happen when
    there's a new change and you merge before renovate has picked it up.

    Similarly, a PR on something else could rebuild your image (this depends
    on your CI/CD), but won’t update the comment - so you might be running a
    newer version before renovate has the chance to propose the change.

    And while renovate will pick up the new digest at some point in time and
    update the comments, these two races might occur. With this renovate
    trick/configuration, renovate will ensure that the base will use this
    digest or a more recent one, but can’t ensure this exact digest is used
    at all times.


