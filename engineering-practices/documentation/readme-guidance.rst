Guidance on writing READMEs
===========================

The README is a vital part of a charm's documentation. It helps the user
navigate the code repository, and it provides a brief description of the
project and links to relevant resources (especially documentation).

The README template
~~~~~~~~~~~~~~~~~~~

If you create a new charm using the ``platform-engineering-charm-template``, then your new
repository will contain the `README template <https://github.com/canonical/platform-engineering-charm-template/blob/main/README.md>`_
that you can use as guidance.

Pre-existing charms that were created prior to the inclusion of this template
will need their READMEs updated to align with this template.

The template was born from documentation practice discussions about offering
guidelines on charm documentation, from the code repository to Charmhub. The
template aims to meet the needs of a basic charm implementation and does not
cover more complex situations (for instance, mono-repository projects with
multiple charms or directories). 

The template contains the following required components:

* **A title**.
* **A brief description of the charm**. Use this description to introduce the
  software the charm deploys and the substrate (machine, Kubernetes, etc.).
* **A link to the official documentation for the charm**.
* **A list or summary of app-specific features**. 
* **Get started**. If the charm already contains a relevant how-to guide or
  tutorial in its documentation, use this section to link the documentation.
  You don't need to duplicate documentation here. If the tutorial is more
  complex than getting started, then provide brief descriptions of the steps
  needed for the simplest possible deployment.
* **Basic operations**. Use this section to provide information on important
  actions, required configurations, or other operations the user should know
  about. You don't need to list every action or configuration. Use this section
  to link the Charmhub documentation for actions and configurations.
* **Learn more**. Provide a list of resources, including the official
  documentation, developer documentation, an official website for the software
  and a troubleshooting guide. Note that this list is not exhaustive or always
  relevant for every charm. If there is no official troubleshooting guide,
  include a link to the relevant Matrix channel.
* **Project and community**. Use this section to link to the issues page,
  Launchpad (if applicable), contributing guides, and other contact info such
  as a Matrix channel. Even if you link the same Matrix channel as the "Learn
  more" section, include it again.

In addition, there is an optional **Integrations** section depending on
whether your charm supports any integrations. Use this section to inform the
user about any required integrations. Otherwise, include a link the Charmhub
documentation on integrations. 

Implementing the README template for more complex cases
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The README template may not be appropriate for certain charms or projects.
In this case, use your best discretion for additional sections to include
or sections to ignore. 

In the case of a mono-repository setup where multiple charms are housed in the
same code repository, it may be more appropriate to adopt this template in the
directories for the individual charms, and then include a README in the root
directory to offer context for the repository structure and provide a brief
description of its components.

Example READMEs
~~~~~~~~~~~~~~~

* `Chrony Operator <https://github.com/canonical/chrony-operator/blob/main/README.md>`_
* `Indico Operator <https://github.com/canonical/indico-operator/blob/main/README.md>`_
* `Synapse Operator <https://github.com/canonical/synapse-operator/blob/2/main/README.md>`_
* `content-cache <https://github.com/canonical/content-cache-operator/blob/main/README.md>`_
  (for mono-repository implementation)

