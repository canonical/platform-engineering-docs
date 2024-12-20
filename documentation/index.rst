Documentation
=============

Canonical seeks to set a standard of excellence in software documentation.
We have adopted the `Diátaxis documentation framework <https://diataxis.fr/>`_
to accomplish this ambitious goal.

Documentation roadmap items
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Roadmap items related to documentation are tracked in the
`documentation dashboard <https://docs.google.com/spreadsheets/d/1_KpPGTWTt3dqER9G25sX3W0q4XkEAHRMI9lWG7XvJ2s/edit?usp=sharing>`_,
maintained by Daniele. Our team is represented in this spreadsheet under "IS Charms" in two rows:

1. **WordPress (specimen)**, representing all of our charms.
2. **12-Factor app support**, representing the 12-Factor project.

These rows are connected to automated objectives spreadsheets that track our
documentation progress and update the dashboard.

We are represented in two rows because we wanted to reduce the duplication of
effort maintaining 20+ spreadsheets for each individual charm. All of our charms
are tracked in the `Wordpress spreadsheet <https://docs.google.com/spreadsheets/d/1v0DzKMIwj80vzfWJBAn2QsdgHIx9xYsL-xZBOf75GkI/edit?usp=sharing>`_
under the "Other IS charms". That tab contains a subjective assessment of our
charms' documentations when compared to the Wordpress charm. 

Because the 12-Factor app project is a distinct product comapred to the rest
of our charms, the project is represented separately, and its documentation
efforts are tracked in a `separate spreadsheet <https://docs.google.com/spreadsheets/d/1TYg6-4Ys6RMzmLvQNDq_Lxk6k5v7ykBscU6vmJB9414/edit?usp=sharing>`_.

The automated objectives spreadsheets contain a list of approved
agreement items between Erin, Varshi, and Daniele for the upcoming cycle.

Contributing to our RTD site
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Our internal RTD site is based on the `Canonical starter pack
<https://canonical-starter-pack.readthedocs-hosted.com/latest/>`_
and hosted on `Read the Docs <https://about.readthedocs.com/>`_.

For syntax help and guidelines,
refer to the `Canonical style guides
<https://canonical-documentation-with-sphinx-and-readthedocscom.readthedocs-hosted.com/#style-guides>`_.

When testing locally, some useful commands include:

* ``make help`` shows all the available commands.
* ``make run`` builds the RTD site locally; you can visit.
  :literalref:`http://127.0.0.1:8000` to view any changes you make.
* ``make spellcheck`` runs the spelling check. You can add words to
  ``.custom_wordlist.txt`` to prevent unnecessary failures.
* ``make linkcheck`` runs the link checker.
* ``make woke`` runs the inclusive tests.

Resources
~~~~~~~~~

* `Canonical blog post about Diátaxis <https://ubuntu.com/blog/diataxis-a-new-foundation-for-canonical-documentation>`_
* `Canonical starter pack documentation <https://canonical-starter-pack.readthedocs-hosted.com/latest/>`_

.. toctree::
   :hidden:
   :maxdepth: 1

   Architecture diagrams <architecture-diagram-guidance>
   Guidance on READMEs <readme-guidance>
   Guidance on tutorials <tutorial-guidance>
