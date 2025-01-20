Specs and Interfaces
====================

.. toctree::
   :maxdepth: 2

When a spec should be written
-----------------------------

The below discussion focuses on when a spec may or may not be necessary.
Guidance on this can be provided during the project meetings. It is a guideline
and exceptions may be necessary. Exceptions should be rare. If it is a
borderline decision, write the spec. A spec can cover one or more PRs/ Jira
stories/ GitHub issues. There are some general guidelines below for whether a
spec is needed. During backlog refinement and planning is a good time to
consider whether a spec is needed for a given item.

When a spec may not be needed:

* Simple bug fix
* Trivial changes (e.g., upgrading a package version) or straightforward changes
* We have done something similar in the past (already covered by a spec or
  pattern like COS integration) and there have been no significant changes since
  then that warrant considering an alternate approach
* Writing documentation

For these kinds of tasks a detailed Jira ticket is sufficient.

When a spec probably is needed:

* Writing a new charm or tool
* Adding a new feature to an existing charm or tool with a caveat that simple
  features may not require a spec
* Refactoring an existing charm or tool except for simple refactors
* Introducing a process to the team
* We have done something similar in the past without writing a spec that would
  qualify as requiring a spec

Additional notes:

* If a spec makes a previous spec obsolete, the spec becoming obsolete should
  have the status updated to “obsolete” once the spec making it obsolete has
  been approved. That spec should be updated with a link to the new spec.
* If a spec only partially makes another spec obsolete, consider updating that
  spec and put it up for approval again.
* If the item requiring a spec is related to an existing spec that is still
  being worked on (at a status before “approved”), consider enhancing that spec
  to also include the item.

Reviews and Approvals
---------------------

Spec writers should seek feedback from:

* The Platform Engineering team
* Any external stakeholders (e.g., IS, Observability, Data Platforms, etc.)

Start with a discussion with the key stakeholders, such as during project
meetings, before starting to draft a spec. During this discussion, a braindump
version of the spec can be created. A good way to gather additional feedback is
to include the braindump spec in the EOD digest of the author.

Initial feedback should be sought by adding an entry to the changelog for each
of the final approvers so that they have a chance for an initial round of review
and comments can be addressed. Their line should have a status of Pending
Review. The comment feature can be used to assign a task to the reviewer. See
the changelog table and comment history on
`this spec <https://docs.google.com/document/d/1TdfRQazCTBa7_tMnJWljdAFG_KtFOU8I_KHxlM8-wh8/edit?usp=sharing>`_
as an example.

Approvals are required from:

* Management:
  * Manager or technical lead (this can be different to the line manager of the
    author of the spec) accountable for the project for simple/ local specs such
    as those delivering on a project
  * Platform Engineering Director (Varshi) and Leadership (Sebastien, Gregory,
    David) for specs with a broader impact, such as proposals to change
    architecture guidance, new processes or those requiring external review by
    senior stakeholders
* Representatives for external teams were appropriate (e.g., Kris for IS, Simon
  for Observability, Mykola for Data Platforms, etc.)

Reviewers should set their entry in the changelog table to the Approved status.
Once all reviewers have approved the spec, the status of the spec in the header
can be changed to Approved. If there are issues with the spec or the team
changes direction, the reviewer should mark the spec as Rejected and the status
of the spec should be updated to Rejected.

Whilst final approval is required before merging significant PRs, work can start
on an item once the discussion with approvals has advanced and key design
decisions have been settled.

