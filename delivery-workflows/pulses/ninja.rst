Platform Engineering Ninja
==========================

Expectations of time spent
--------------------------

Not more than 2 hours/day. You should plan your pulse items accordingly keeping
in mind almost 2.5 days in a pulse will be dedicated to Ninja tasks.

Checklist
---------

Multiple times a day
~~~~~~~~~~~~~~~~~~~~

* Monitor the public channels Platform Engineering, IS, IS Outage, team public
  rooms on Matrix and reply/report any issues to relevant people by either
  tagging them on the chat or posting in the internal team channel. Reach out to
  the manager if unclear
* Monitor the Charm Development channel on the public Matrix instance, looking
  out for comments or questions from community members about any of the charms
  we’re responsible for and addressing them promptly
* Monitor the `Discourse community forum <https://discourse.charmhub.io/>`_ We
  should look out for comments or questions from community members about any of
  the charms we’re responsible for, or updates to or comments on the specific
  documentation pages hosted there for our charms and address them promptly
  (this can be done by viewing any recent posts in
  `with the "docs" tag in the "charm" category) <https://discourse.charmhub.io/tags/c/charm/41/docs>`_.
* Monitor alerts and Grafana dashboards (if needed) for all products deployed in
  production and report on IS charms channel tagging relevant individuals in
  case of issues or improvement suggestions. Could be done twice a day(start and
  end). Identify if any new alerts need to be added/modified to reduce noise.

  * `GH Runners <https://cos-ps6.is-devops.canonical.com/prod-cos-k8s-ps6-is-charms-grafana/d/44304e53d8a6d8bc/github-self-hosted-runner-metrics>`_
  * `Discourse <https://cos-ps6.is-devops.canonical.com/prod-cos-k8s-ps6-is-charms-grafana/d/ccaed73a5712d5f6/discourse-stats?orgId=1>`_
  * `Synapse <https://cos-ps6.is-devops.canonical.com/prod-cos-k8s-ps6-is-charms-grafana/d/528989afbcc43cea/synapse-operator?orgId=1>`_
  * `Charm status <https://cos-ps6.is-devops.canonical.com/prod-cos-k8s-ps6-is-charms-grafana/d/cf5659dc-dfd9-45b6-a124-1956296e3a11/charm-status?orgId=1>`_ (contains information about last alerts for model in PS6)

Once a day
~~~~~~~~~~

* Check unassigned issues and security overview on Github and reply/ tag the
  engineer responsible to create JIRA tickets under the project epic linking to
  it. This should only cover the period for a given Ninja when they are
  assigned, previously created issues are out of scope. If no response to a tag
  is received, Ninja should add the repo

  * `https://github.com/canonical/discourse-k8s-operator <https://github.com/canonical/discourse-k8s-operator>`_
  * `https://github.com/canonical/gateway-api-integrator-operator <https://github.com/canonical/gateway-api-integrator-operator>`_
  * `https://github.com/canonical/github-runner-operator <https://github.com/canonical/github-runner-operator>`_
  * `https://github.com/canonical/haproxy-operator <https://github.com/canonical/haproxy-operator>`_
  * `https://github.com/canonical/httprequest-lego-provider <https://github.com/canonical/httprequest-lego-provider>`_
  * `https://github.com/canonical/indico-operator <https://github.com/canonical/indico-operator>`_
  * `https://github.com/canonical/irc-bridge-operator <https://github.com/canonical/irc-bridge-operator>`_
  * `https://github.com/canonical/jenkins-agent-k8s-operator <https://github.com/canonical/jenkins-agent-k8s-operator>`_
  * `https://github.com/canonical/mattermost-k8s-operator <https://github.com/canonical/mattermost-k8s-operator>`_
  * `https://github.com/canonical/maubot-operator <https://github.com/canonical/maubot-operator>`_
  * `https://github.com/canonical/netbox <https://github.com/canonical/netbox>`_
  * `https://github.com/canonical/nginx-ingress-integrator-operator <https://github.com/canonical/nginx-ingress-integrator-operator>`_
  * `https://github.com/canonical/operator-workflows <https://github.com/canonical/operator-workflows>`_
  * `https://github.com/canonical/paas-charm <https://github.com/canonical/paas-charm>`_
  * `https://github.com/canonical/penpot-operator <https://github.com/canonical/penpot-operator>`_
  * `https://github.com/canonical/pollen-operator <https://github.com/canonical/pollen-operator>`_
  * `https://github.com/canonical/synapse-operator <https://github.com/canonical/synapse-operator>`_
  * `https://github.com/canonical/wazuh-server-operator <https://github.com/canonical/wazuh-server-operator>`_
  * `https://github.com/canonical/wordpress-k8s-operator <https://github.com/canonical/wordpress-k8s-operator>`_
  * `https://github.com/canonical/chrony-operator <https://github.com/canonical/chrony-operator>`_
  * `https://github.com/canonical/content-cache-k8s-operator <https://github.com/canonical/content-cache-k8s-operator>`_
  * `https://github.com/canonical/github-runner-webhook-router <https://github.com/canonical/github-runner-webhook-router>`_
  * `https://github.com/canonical/github-runner-image-builder <https://github.com/canonical/github-runner-image-builder>`_
  * `https://github.com/canonical/github-runner-image-builder-operator <https://github.com/canonical/github-runner-image-builder-operator>`_
  * `https://github.com/canonical/tmate-ssh-server-operator <https://github.com/canonical/tmate-ssh-server-operator>`_
  * `https://github.com/canonical/repo-policy-compliance <https://github.com/canonical/repo-policy-compliance>`_

* Merge renovate PRs across all GH repos if tests pass, if not retrigger tests/tag responsible
  engineers to fix
* Check `IS DevOps queue <https://portal.admin.canonical.com/q/is_devops/>`_ for
  new tickets, escalate to ticket creator if there is an update or missing data
  or to your manager if a ticket priority needs to be update or we have an empty
  spot in the priority queue
* Post in your EOD report any issues found, tickets created, PRs assigned or
  bug fixes Review or tag reviewers for community contributions
* Review or tag reviewers for community contributions

Once a pulse
~~~~~~~~~~~~

* Update  the
  `product feedback spreadsheet <https://docs.google.com/spreadsheets/d/1p3hqyyjG9Mb2cTDeEumCHl8Bx8WGm0uJdYTHVUzABvE/edit?gid=0#gid=0>`_
  if a new bug needs attention or is closed out
* Check the IS DevOps demo notes and remind the team if none is added 1 day
  before the demo day
* Post pulse report to internal and external discourse at the end of pulse
  following the guidance here: :ref:`internal-external-updates`
* Schedule a handoff session with upcoming Ninja at the end of pulse and inform
  them of any pending PRs, specs, issues you were monitoring
* Add a reflection section on top of the checklist with any issues discovered or
  repos with unassigned issues that had no action taken during the period.
  Managers can follow up with the issues identified and unassigned items after.

Rotation
--------

This role will be rotated among the team every pulse with ongoing Ninja
conducting a handoff with the upcoming one before the end of pulse. In the event
the designated Ninja is unavailable (e.g., due to vacation or illness), a
backfill arrangement should be pre-agreed to ensure continuity. We will use
`this list <https://docs.google.com/spreadsheets/d/18QF7jRw1_rsVzd6Zs2zrafm6hEZBuhRhN9jjEq7Sa0U/edit?gid=0#gid=0>`_
with pulses assigned with a primary and secondary where the secondary will take
over in case primary isn’t available.
