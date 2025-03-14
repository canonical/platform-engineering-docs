Testing Secrets
===============

Many of our charms require integrations to third party services and hence may
require credentials that help integrate with such services. This documentation
covers the conventions and guidelines to set up testing secrets.

Setting up Bitwarden
--------------------

See the `IS guide on Bitwarden
<https://canonical-information-systems-documentation.readthedocs-hosted.com/en/
latest/how-to/bitwarden/>`_.
Also probably worth a read: `Lastpass going out, Bitwarden coming in
<https://discourse.canonical.com/t/lastpass-going-out-bitwarden-coming-in/
4258>`_.

Organizing secrets in Bitwarden
-------------------------------

In Bitwarden under collections, save the secrets under Platform Engineering.

#. Add a new testing secret by clicking ``New`` button on the UI.
   Select the ``Note`` type secret.
#. For ``Item name``, use the following convention to save the testing secrets:
   ``<repository-name-without-"operator"-suffix>-testing-secrets``.
   i.e. wordpress-k8s-testing-secrets.
#. Select ``Platform Engineering`` for the collections to save under.
#. Under ``Additional options`` field, describe the secrets and how they are
   used. i.e. ``--akismet-key=<akismet-key>``
#. The secrets should be saved under ``Custom Fields`` section, which allows the
   secrets to be saved safely. Click on the edit button next to the text input to
   name the secret. Map all individual secrets to each field and label them accordingly. 
