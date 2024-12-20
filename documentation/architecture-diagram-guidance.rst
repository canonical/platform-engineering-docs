Guidance on adding an architecture diagram
==========================================

As part of the 25.04 roadmap for our charms,
`we have agreed <https://docs.google.com/spreadsheets/d/1v0DzKMIwj80vzfWJBAn2QsdgHIx9xYsL-xZBOf75GkI/edit?usp=sharing>`_
to start working on the "reference architecture" item, which includes both the
explanation document itself and architecture diagrams. 

This document offers guidance on how to create and add an architecture diagram
to the documentation so that we can remain as consistent as possible between
our charms' documentation sets.

For the purposes of the agreement item, we will be focusing on charm
(application) architecture diagrams, or how the charm works internally. Later
we will focus on reference architecture, or how the charm works in the context
of other charms with which it is integrated.

Where
~~~~~

The diagram will be added into the "Charm architecture" Explanation document.
If this document doesn't exist, then it will need to be created.

How
~~~

Use `Mermaid <https://mermaid.js.org/>`_ to create the diagram. Mermaid is a
diagramming and charting tool that renders Markdown-inspired text definitions
into diagrams. The benefit of this tool is that the diagrams can be rendered
in GitHub and on Discourse.

Below are some examples of Mermaid diagrams in Canonical documentation:

* `Canonical Observability Stack Lite <https://charmhub.io/cos-lite/docs/explanation/logging?channel=latest/edge>`_
* `Charmed MySQL K8s <https://charmhub.io/mysql-k8s/docs/e-flowcharts>`_

Furthermore, the `Maubot operator <https://github.com/canonical/maubot-operator>`_
contains a Mermaid diagram in its README. 

.. mermaid::

   sequenceDiagram
      participant Alice
      participant Bob
      Alice->John: Hello John, how are you?
      loop Healthcheck
          John->John: Fight against hypochondria
      end
      Note right of John: Rational thoughts <br/>prevail...
      John-->Alice: Great!
      John->Bob: How about you?
      Bob-->John: Jolly good!


Maybe copy the Maubot diagram into these docs?
Then show the raw code as well?

