Pulse retros
============

Towards the end of each pulse a retrospective should be held where the pulse is
reviewed.

Additionally, the retro should be used as an opportunity to get feedback on team
processes, external blockers, recognize achievements in the cycle, and discuss
any challenges the team is facing in general. This should also be prepared in
advance by everyone in the team using
`Team Improvement Ideas <https://docs.google.com/document/d/1S-YjcjWic1xZ9uPLvXNNY8mMCYtsZ3OTtohpZsQlVGA/edit?usp=sharing>`_.
The ideas in this document should be reviewed and any new or in-progress items
discussed.

 .. _internal-external-updates:

Internal and External Team Updates
----------------------------------

External Updates
~~~~~~~~~~~~~~~~

The external updates will be published as follows:

* The external updates are to be published on
  `https://discourse.charmhub.io/ <https://discourse.charmhub.io/>`_ in the
  “charm” category with the “team-updates” tag.
* The title should be “Platform Engineering Team Updates - Pulse #<pulse number>
  <year>”.
* The content should be:

  <brief summary highlighting the most significant items>

  # <name of a charm or group of charms/ tool/ standard/ …>

  \* <dotted list of items related to the charm>

  \* ...

  ...

Here is an
`external example <https://discourse.charmhub.io/t/platform-engineering-team-updates-pulse-25-2024/16122>`_.

The pulse number should be the pulse number that the items in the post where
completed in.

The scope of items to be covered are any charming related deliverable that an
external stakeholder might be interested in. This can include filing of bugs,
new charm versions released (edge or stable) or new tools published by or
standards adopted by the team. It does not include progress on charms that have
not been deployed to charmhub. It also does not include things like progress on
production deployments of charms.

The items should be based on the content in
`this document <https://docs.google.com/document/d/1bonE3AzlAdZsnWyXy2ygwFvowjB8xVGU5riPBWOQ-ss/edit?usp=sharing>`_
from the relevant period excluding items that should be internal (you can still
use a [Canonical Internal Only] tag). These items will need to be summarized and
grouped based on the headings in the content specification above.

Internal Updates
~~~~~~~~~~~~~~~~

The internal updates will be published as follows:

* The internal updates (including external updates content) are to be published
  on `https://discourse.charmhub.io/ <https://discourse.charmhub.io/>`_ in the
  “Platform Engineering” category with the “pulse-report” tag:
  `https://discourse.canonical.com/c/engineering/platform-engineering/44 <https://discourse.canonical.com/c/engineering/platform-engineering/44>`_
* The title should be “Platform Engineering Team Updates - Pulse #<pulse number>
  <year>”.
* The content should be:

  <brief summary highlighting the most significant items>

  # <name of a charm/ tool/ standard/ …>

  \* <dotted list of items related to the charm>

  \* ...

  ...

  For highlights of deliverables this pulse, please see our shared
  `demos <https://drive.google.com/drive/folders/1xCy9MASYNHFGc1Vi4vWWSE05Y-hySh1B>`_
  folder, his is the EMEA and APAC demo for the pulse.

Here is an
`internal example <https://discourse.canonical.com/t/is-devops-team-updates-pulse-1-2025/4982>`_

The scope of the items to be covered include anything in the external update in
addition to what the team delivers that would not be of interest to external
stakeholders, such as internal charm deployments.

The EMEA and APAC demo links should be added at the end of the content.

The items should be based on the content in the following document from the
relevant period:
`Platform Engineering Pulse Reports <https://docs.google.com/document/d/1bonE3AzlAdZsnWyXy2ygwFvowjB8xVGU5riPBWOQ-ss/edit?usp=sharing>`_.
These items will need to be summarized and grouped based on the headings in the
content specification above.

Keeping Track of Content
~~~~~~~~~~~~~~~~~~~~~~~~

The items are to be tracked in the following document:
`Platform Engineering Pulse Reports <https://docs.google.com/document/d/1bonE3AzlAdZsnWyXy2ygwFvowjB8xVGU5riPBWOQ-ss/edit?usp=sharing>`_.
Any items intended for internal stakeholders only should be noted after the
[Canonical Internal Only] tag.

Not all Jira stories will result in an item to be added to the tracking
documents. Some Jira stories may result in multiple items to be added. Some of
what the team does which isn’t tracked in Jira may also warrant an item in the
tracking document. These should be added as soon as an IS DevOps team member
becomes aware of them.

Guidance for items to be included
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Items should generally include a link to further material or to the artifact
  being discussed, such as a link to a release on charmhub. If there is no link,
  it may be that the item is not worth including until a tangible outcome has
  been achieved.
* When including a link to a bug that was filed, include a short description of
  the problem.
* Be succinct when describing the item, it may be one of many. Describe it in a
  way that is relevant to the stakeholder rather than someone who understands the
  charm in detail. Use plain English and avoid jargon.
* If the outcomes of spikes, bugs or tasks could be interesting to other teams,
  it is worth noting those on the updates as well.
* Reports purely on progress are usually not required, unless there is something
  tangible for stakeholder to look at such as a release to the edge channel, a
  specification document or some prototype.

Publishing
~~~~~~~~~~

The internal and external posts are to be done by the Platform Engineering ninja
assigned for a pulse who will write both the internal and external updates.
