Testing Secrets
===============

Many of our charms require integrations to 3rd party services and hence may
require credentials that help integrate with such services. This documentation
covers the conventions and guidelines in setting up testing secrets.

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

1. Add a new testing secret by clicking ``New`` button on the UI. Select the
``Note`` type secret.

1. For ``Item name``, use the following convention to save the testing secrets: 
``<repository-name-without-"operator"-suffix>-testing-secrets``.
i.e. wordpress-k8s-testing-secrets.

1. Select ``Platform Engineering`` for the collections to save it under.

2. Under ``Additional options`` field, describe the secrets and how they are used.
i.e. --akismet-key=<akismet-key>

1. The secrets should be saved under ``Custom Fields`` section, which allows the
secrets to be saved safely. Click on the edit button next to the text input to
name the secret. Map all individual secrets to each field and label them
accordingly. 
