JIRA
====

.. toctree::
   :maxdepth: 2

Definition of Done
------------------

The following items should be completed for each Jira story:

1. Any code written complies with the
   `contributing guide <https://github.com/canonical/is-charms-contributing-guide>`_.
   This is to ensure that the code meets all the expected standards.
1. The code meets the acceptance criteria of the ticket. This can be established
   by, for example, deploying a new version of the charm and checking that the
   feature works. Preferably done using an automated e2e test.
1. If a charm is proposed to be deployed to production based on the release
   following a PR or already has been deployed to production, the documentation
   is written/ updated. This ensures that our users can use what we create.
1. Any code merged into the main branch is successfully released to at least the
   edge channel (or equivalent). This ensures that the code is ready to be used
   by our users.
1. Update the
   `pulse reports document <https://www.google.com/url?q=https://docs.google.com/document/d/1bonE3AzlAdZsnWyXy2ygwFvowjB8xVGU5riPBWOQ-ss/edit?tab%3Dt.0&sa=D&source=docs&ust=1736426796056234&usg=AOvVaw1Xw5niRUEcyFvQMU3a-qwg>`_
