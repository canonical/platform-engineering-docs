Documentation
=============

Single sentence about company standards: Diátaxis, high quality docs, setting high standard

Section about roadmap items

* Documentation dashboard from Daniele 
* How we are represented (two items, Wordpress and 12-factor project)
* Why we are represented in this way
* Each item has an automated objectives spreadsheet that only Daniele touches 
* Each spreadsheet has a list of approved agreement items for the cycle 
* For Wordpress: every other charm needs to follow the specimen 
* 12-factor project does not necessarily include 12-factor charms (e.g. ``repo-policy-compliance``), but rather the ``paas-charm`` library and product 

Section about contributing to this site

* link canonical starter pack docs for more info
* ``make help`` will show you all the available commands
* ``make run`` will build the RTD site locally, and you can visit :literalref:`http://127.0.0.1:8000` in your browser to view any changes you make
* ``make spellcheck`` will run the spelling check. If you have a word that fails the spelling check but is not actually misspelled, you can add the word to ``.custom_wordlist.txt`` and rerun. You may need to do a ``make clean`` followed by ``make html`` for the changes to stick 
* ``make linkcheck`` will run the link checker  
* ``make woke`` will run the inclusive tests
* ``make vale`` will run the Vale style checker 

.. toctree::
   :hidden:
   :maxdepth: 1

   Architecture diagrams <architecture-diagram-guidance>
   Guidance on READMEs <readme-guidance>
   Guidance on tutorials <tutorial-guidance>
