Team practises
==============

The following outlines how we operate as a team starting with cycle planning,
pulse by pulse and activities within each pulse.

Cycle planning
--------------

During cycle planning the high-level roadmap for 6 months is defined. This is
done towards the end of a given cycle in preparation for the next cycle. Input
comes from the team, management, and external stakeholders such as IS. Items
should be grouped by charm. Items are estimated with the engineers to establish
the number of days required to deliver an item to ensure that we don’t commit
too much during a cycle. When calculating the capacity for the cycle, use the
following rough formula:

* Number of available engineers: number at the start of the cycle + expected
  hires during the cycle * 0.5
* Estimate the number of available weeks based on the length of the cycle and
  subtract any global events such as sprints and holidays such as the end of
  year shutdown
* Reserve 20% of time for management, training, hiring, leave, public holidays
* Reserve 20% for bugs and production support

Items are prioritized by management with input from the team and external
stakeholders. Each item on the roadmap becomes an epic for the cycle with
individual stories breaking down the epic to be created by engineers and
reviewed by management. The epic should include clear requirements to explain
what is needed. This can best be facilitated by a kick-off meeting to ensure
everyone understands the scope for what is needed. This is to be done during the
engineering sprint or shortly thereafter. More guidance on sprint preparation:
`PR016 - Sprint Readiness Review (SRR) <https://docs.google.com/document/d/1baTwzRxoiEW0RuivVbBFndbbeKnX4RR0IvQeXKgz5Is/edit>`_

Project meeting
---------------

For each major group of deliverables (equivalent to an objective in Jira), e.g.
a charm with significant work to be done, a per pulse project meeting should be
held. This could also be a group of charms if there isn’t sufficient work for an
individual charm. During this meeting, the progress against the roadmap should
be reviewed. Any items that are taking longer than estimated for the cycle
should be carefully reviewed for whether the scope or something else needs to
change to finish the item or whether it should be delayed.

It is useful to do this as a meeting since it is an opportunity for discussing
specific items that could be challenging, assessing priorities of stories for
the next pulse, and clarifying and reviewing stories that were created. This
usually involves quite a bit of discussion which is why a synchronous meeting is
useful. This should include reviewing the plan in Jira, and creating any new
tickets that are required to be delivered and the priority of tickets should be
clear after the meeting.

Engineers should come prepared to this meeting with the required stories
created.

Pulse plan
----------

Based on the project meetings, a pulse statement should be prepared in advance
by engineers which identifies what each engineer is planning to do during the
next pulse. This should be reviewed during a pulse planning meeting to keep the
rest of the team up to date with the status of a given project and provide an
opportunity for input for any of the items in the pulse statement. More details
in ISD073 - Written Pulse Statements (to be updated)

Stand up
--------

Each day during the pulse a stand up should be held. Engineers should discuss
what they were working on before and what is next, let the manager know if they
don’t have sufficient work to be going on with and discuss any blockers for
resolution. It is also a chance to discuss any technical challenges potentially
after each engineer has given their regular update.

There should be a standup scheduled for each timezone the team operates in.
There will be projects being worked on across different time zones. The
`Standup Project Allocations <https://docs.google.com/spreadsheets/d/1Gz0Owj7h_DzFCDz7g6-mU-VA9smer_DJPDKffMtnMfU/edit?usp=sharing>`_
defijnes which projects are discussed at a specific stand up and who should come
to a given standup. Engineers must attend the stand up they have been allocated
and give their update and are welcome to attend any other stand up as well. The
project allocations to stand ups will be done to find a best match to ensure
that everyone working on a project is in the same stand up. The stand up times
will be adjusted on a best effort basis to accommodate engineers being able to
attend and DST changes.

Delivering stories
------------------

The following sections discuss practices that should be followed when delivering
stories.

Define the public interface
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Stories that are anticipated to be marked as requiring senior review (see below)
have a two stage development process. The first stage is the public interface
and configuration design. This should be completed as a part of writing a spec
for the change which should include a link to a WIP PR with the public interface
design. Any stories that don't have a spec or already have an approved spec
without a public interface design also require the WIP PR as a first step.

Only after this should implementation proceed. Note that implementation may
start on the same branch as soon as any draft PR is raised and doesn’t have to
wait for review comments.

Specifically, the WIP PR should include:

* Any changes, additions or deletions of the public interfaces (including
  docstrings) of any of the modules in the source code (note that, at this
  stage, tests and linting won’t pass due to the missing implementation)
* Any changes, additions or deletions of juju events being handled
* Any changes, additions or deletions of configuration options and actions
  (by modifying ``config.yaml`` and ``actions.yaml`` or ``charmcraft.yaml``)
* Any changes to provides and requires by modifying ``charmcraft.yaml``

In regards to the public interfaces, consider the following public function in a
module:

.. code-block:: python

    def wait_ready(timeout: int = 300, check_interval: int = 10) -> None:
        """Wait until Jenkins service is up.

        Args:
            timeout: Time in seconds to wait for jenkins to become ready in 10
                second intervals.
            check_interval: Time in seconds to wait between ready checks.

        Raises:
            TimeoutError: if Jenkins status check did not pass within the
                timeout duration.
        """
        try:
            _wait_for(_is_ready, timeout=timeout, check_interval=check_interval)
        except TimeoutError as exc:
            raise TimeoutError(
                "Timed out waiting for Jenkins to become ready."
            ) from exc

For the purpose of the WIP PR, only the function signature and docstring should
be included:

.. code-block:: python

    def wait_ready(timeout: int = 300, check_interval: int = 10) -> None:
        """Wait until Jenkins service is up.

        Args:
            timeout: Time in seconds to wait for jenkins to become ready in 10
                second intervals.
            check_interval: Time in seconds to wait between ready checks.

        Raises:
            TimeoutError: if Jenkins status check did not pass within the
                timeout duration.
        """

If, for example, a new argument is required to be added in the PR, just the
function signature and docstring should initially be updated for raising the WIP
PR:

.. code-block:: python

    def wait_ready(
        timeout: int = 300,
        check_interval: int = 10,
        use_exp_backoff: bool = False,
    ) -> None:
        """Wait until Jenkins service is up.

        Args:
            timeout: Time in seconds to wait for jenkins to become ready in 10
                second intervals.
            check_interval: Time in seconds to wait between ready checks.
            use_exp_backoff: Whether exponential backoff should be used to wait
                for Jenkins to be ready.

        Raises:
            TimeoutError: if Jenkins status check did not pass within the
                timeout duration.
        """

Similarly, for classes, any public attributes (including their type) and
functions (including their signature and docstring) are in scope. It is likely
that during the implementation the public interface may change. The WIP PR
should represent the best view before implementation of what will be required.

The design review can be done in the following ways:

* A draft PR review from a senior team member
* Meeting with a senior to go over the draft PR
* Take the draft PR to the architecture office hours
* Have the design approved as a part of as spec
* Pair program with a senior on the interface design

Once completed, the person doing the senior review should leave a comment on the
WIP PR approving the public interface.

PR complexity and senior review requirement
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

PRs should be as small as they can pragmatically be. For example, if it includes
multiple features it should be split into multiple PRs.

PRs raised by developers should be tagged according to the following labels to
help indicate the complexity of the review:

* ``trivial``: minor bug fix, documentation updates, making changes based on the
  pre-reviewed code, adding a minor test case, refactoring a small code segment
* ``senior-review-required``: anything else, such as, architecture change, new
  feature introduced, new charm, refactor

PRs in GitHub can be labeled in the following way below the reviewers section:

.. image:: add-labels.jpg
  :width: 400

PRs with documentation
~~~~~~~~~~~~~~~~~~~~~~

Once documentation has been written for a project, PRs that make a change to a
feature, introduce a new feature or otherwise make significant changes that our
user should know about should include documentation in the PR.

If there is documentation in the PR, it should be tagged with documentation to
indicate to the technical author in the team that they should review the PR. Do
not tag PRs with trivial changes in docs like typos, small fixes or deleting
documentation when a feature is dropped.

PR reviews
~~~~~~~~~~

Anyone on the team can review trivial PRs and PRs generated by automation such
as bumps to library or dependency versions. These PRs should be skipped by
senior team members to help them focus on the PRs requiring their review. PRs
marked as requiring senior review should be reviewed by at least one senior team
member, such as an architect, manager, senior developer (see Canonical leveling
framework) or as identified by management. PRs with this tag should not be
merged until the senior reviewer has approved the PR, even if 2 other people
have already approved the PR.

The
`IS & IS DevOps Roadmap planning <https://docs.google.com/spreadsheets/d/1iSkut6Qf_mm7_HynYeCX_lB47noCGJuXNe1ODrbXTPk/edit?usp=sharing>`_
spreadsheet identifies who should be doing the senior review and the second
review for each roadmap item. When raising a PR, please request review from
those people. In the case of trivial PRs, a second review can be requested from
anyone on the team.

PR description
~~~~~~~~~~~~~~

Unless the PR is trivial or self-explanatory (for example, fixing typos in the
documentation, a well known task that needs to be done across many repositories
such as enabling a new bot), the PR description should include:

* A high level overview of the change
* The reason the change is needed
* Any applicable spec if relevant and it is publicly available
* Any changes to the juju events being observed (newly added, significantly
  modified or deleted)
* Any high level changes to modules and why (Service, Observer, helper)
* Any changes to charm libraries
* Either
    * A confirmation that
        * The `charm style guide <https://juju.is/docs/sdk/styleguide>`_ was applied
        * The
          `contributing guide <https://github.com/canonical/is-charms-contributing-guide>`_
          was applied
        * The changes are compliant with
          `ISD014 - Managing Charm Complexity <https://docs.google.com/document/d/1G62PosrObvmQY5KbxvqaxByojlhDxrmNtcbPS39YbaY/edit?usp=sharing>`_
    * Or which of the standards/ guidelines was not applied and why

The above should be created as a template for raising PRs across all of our
repositories:

.. code-block:: markdown

    Applicable spec: <link>

    ### Overview

    <A high level overview of the change>

    ### Rationale

    <The reason the change is needed>

    ## Juju Events Changes

    <Any changes to the juju events being observed (newly added, significantly modified or deleted)>

    ### Module Changes

    <Any high level changes to modules and why (Service, Observer, helper)>

    ### Library Changes

    <Any changes to charm libraries>

    ### Checklist

    - [ ] The [charm style guide](https://juju.is/docs/sdk/styleguide) was applied
    - [ ] The [contributing guide](https://github.com/canonical/is-charms-contributing-guide) was applied
    - [ ] The changes are compliant with [ISD054 - Manging Charm Complexity](https://discourse.charmhub.io/t/specification-isd014-managing-charm-complexity/11619)
    - [ ] The documentation is generated using `src-docs`
    - [ ] The documentation for charmhub is updated.
    - [ ] The PR is tagged with appropriate label (`urgent`, `trivial`, `require-senior-review`, `documentation`)

    <Explanation for any unchecked items above>

Retro
-----

Towards the end of each pulse a retrospective should be held where the pulse
statement is reviewed for the result of each of the items that was intended for
the pulse which should be prepared in advance by the engineers, if not already
done during a project meeting. This should be done on the pulse statement as
discussed in ISD073 - Written Pulse Statements (to be updated).

Additionally, the retro should be used as an opportunity to get feedback on team
processes, external blockers, recognize achievements in the cycle, and discuss
any challenges the team is facing in general. This should also be prepared in
advance by everyone in the team using
`Team Improvement Ideas <https://docs.google.com/document/d/1S-YjcjWic1xZ9uPLvXNNY8mMCYtsZ3OTtohpZsQlVGA/edit?usp=sharing>`_.
The ideas in this document should be reviewed and any new or in-progress items
discussed.

Cycle Retro
-----------

During the engineering sprint the cycle should be reviewed especially for any
items that were planned but not delivered.
