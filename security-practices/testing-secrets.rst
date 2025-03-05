Testing Secrets
===============

Many of our charms require integrations to 3rd party services and hence may
require credentials that help integrate with such services. This documentation
covers the conventions and guidlines in setting up testing secrets.

Setting up BitWarden
--------------------

See the `IS guide on BitWarden
<https://canonical-information-systems-documentation.readthedocs-hosted.com/en/
latest/how-to/bitwarden/>`_.

Organising secrets in BitWarden
-------------------------------

In BitWarden under collections, save the secrets under Platform Engineering.

1. Add a new testing secret by clicking ``New`` button on the UI. Select the
``Note`` type secret.

2. For ``Item name``, use the following convention to save the testing secrets: 
``<repository-name-without-"operator"-suffix>-testing-secrets``.
i.e. wordpress-k8s-testing-secrets.

3. Select ``Platform Engineering`` for the collections to save it under.

4. Under ``Additional options`` field, describe the secrets and how they are used.
i.e. --akismet-key=<akismet-key>

5. The secrets should be saved under ``Custom Fields`` section, which allows the
secrets to be saved safely. Click on the edit button next to the text input to
name the secret. Map all individual secrets to each field and label them
accordingly. 
