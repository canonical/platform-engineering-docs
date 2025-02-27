Guidance on adding charm architecture diagrams
==============================================

As part of the 25.04 roadmap for our charms,
`we have agreed <https://docs.google.com/spreadsheets/d/1v0DzKMIwj80vzfWJBAn2QsdgHIx9xYsL-xZBOf75GkI/edit?usp=sharing>`_
to start working on the "reference architecture" item, which includes both the
explanation document itself and architecture diagrams. 

For the purposes of the 25.04 agreement item, we will be focusing on adding
diagrams for **charm (application) architecture**, or diagrams that describe
how the charm works internally. We will focus on reference architecture,
or how the charm works in the context of other charms with which it is
integrated, at a later time.

This document offers guidance on how to create and add an architecture diagram
to the documentation so that we can remain as consistent as possible between
our charms' documentation sets.

Create the diagram
------------------

Use `Mermaid <https://mermaid.js.org/>`_ to create the diagram. Mermaid is a
diagramming and charting tool that renders Markdown-inspired text definitions
into diagrams. The benefit of this tool is that the diagrams can be rendered
in GitHub and on Discourse.

Whenever possible, use Mermaid's `C4 model syntax <https://mermaid.js.org/syntax/c4.html>`_
so that all of our diagrams follow a consistent structure according to the
`C4 model <https://c4model.com/>`_. 

.. important::

   The C4 model syntax in Mermaid is currently experimental and may produce
   chaotic or clumsy-looking diagrams, especially for diagrams with many
   different components or containers. While it is ideal to maintain
   consistency and follow the C4 syntax, opt for the syntax in Mermaid that
   produces a readable diagram while reducing your time and effort.

Mermaid has a `live editor <https://mermaid.live/>`_ that you can use to
create and render diagrams before adding them into the documentation.

Add the diagram into the docs
-----------------------------

The diagram should be added into the "Charm architecture" Explanation document.
If this file doesn't exist for the charm, then it will need to be created.

Along with the architecture diagram, the "Charm architecture" page should
provide a brief description of the underlying software and an overview of
the charm code, explain how Juju and Pebble containerize the charm, and
describe what Pebble layers are available for metrics and what Juju events
exist for the charm.

(Note that, for some of our charms, the "Charm architecture" page also
includes a section for Integrations. This information really belongs in
Reference and is not necessary to include in newly created documents.)

Some examples for "Charm architecture" documentation include:

* `Indico <https://charmhub.io/indico/docs/explanation-charm-architecture>`_
* `Synapse <https://charmhub.io/synapse/docs/explanation-charm-architecture>`_
* `Mattermost <https://charmhub.io/mattermost-k8s/docs/architecture>`_

Example diagrams
----------------

Below are some examples of Mermaid diagrams in Canonical documentation:

* `Canonical Observability Stack Lite <https://charmhub.io/cos-lite/docs/explanation/logging?channel=latest/edge>`_
* `Charmed MySQL K8s <https://charmhub.io/mysql-k8s/docs/e-flowcharts>`_
* `Checkbox (an example of using Mermaid in RTD) <https://canonical-checkbox.readthedocs-hosted.com/en/stable/explanation/remote.html#automatic-session-resume>`_

Indico
~~~~~~

To serve as an example of a "charm architecture" diagram,
here is a quick and dirty example for the Indico charm (taken from
`this talk <https://docs.google.com/presentation/d/1v01jO85i62rer1QXcASmJGiv-87qinQDBHjGaPsTWTc/edit#slide=id.g159222fceda_0_223>`_):

.. mermaid::

   C4Component
   title Component diagram for Indico Charm

   Container_Boundary(indico, "Indico") {
     Component(nginx, "NGINX", "", "Serves static resources, handles web traffic")
     Component(charm, "Indico", "", "Observes events") 
     Component(celery, "Celery", "", "Processes tasks asynchronously")

     Rel(charm, nginx, "")
     Rel(charm, celery, "")
   }

The raw code for this diagram (implemented in Markdown) is as follows:

.. code-block::

   ```mermaid
   C4Component
   title Component diagram for Indico Charm

   Container_Boundary(imagebuildercharm), "Indico") {
     Component(nginx, "NGINX", "", "Serves static resources, handles web traffic")
     Component(charm, "Indico", "", "Observes events") 
     Component(celery, "Celery", "", "Processes tasks asynchronously")

     Rel(charm, nginx, "")
     Rel(charm, celery, "")
   }
   ```

.. warning::

   The arrows for this diagram may not be totally accurate, so proceed
   with caution if you choose to copy or use this specific diagram.

Maubot
~~~~~~

The `Maubot operator <https://github.com/canonical/maubot-operator>`_
already contains the following Mermaid diagram in its README:

.. warning::

   This diagram is a high-level overview of the Maubot charm. The diagram is
   more aligned with "reference architecture".

.. mermaid::

    graph TD;
        user[User] --> ingress[Ingress];

        subgraph " "
            direction TB;
            nginx[NGINX] --> maubot[Maubot];
        end;

        ingress --> nginx;

        maubot --> postgresql[PostgreSQL Database];
        maubot --> synapse[Synapse Homeserver];

The raw code for this diagram (implemented in Markdown) is as follows:

.. code-block::

   ```mermaid
   graph TD;
       user[User] --> ingress[Ingress];

       subgraph " "
           direction TB;
           nginx[NGINX] --> maubot[Maubot];
       end;

       ingress --> nginx;

       maubot --> postgresql[PostgreSQL Database];
       maubot --> synapse[Synapse Homeserver];
   ```


