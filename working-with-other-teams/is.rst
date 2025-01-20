IS
==

The primary means of requesting something from IS is by creating a ticket. This
can be done either via
`a form on the portal website <https://portal.admin.canonical.com/new/>`_, or
via
`a freeform email <https://portal.admin.canonical.com/ticket-creation-escalation/>`_.

Tickets should be created in the “IS DevOps” queue, which is a dedicated queue
for Platform Engineering.

Creating tickets
----------------

Before creating a ticket we should think about whether it is really needed. Is
there something we can do that would mean this ticket isn’t necessary? Can we
work around the problem in some way?

When creating a ticket, the content isn’t prescribed up front. However, the more
information we can provide the easier it will be for SREs to process it, and so
potentially the sooner it can be done.

Also, if there’s any work we can do to reduce the amount of work an SRE would
need to do to process a ticket, we should likely invest the time to do so, even
if we’re not familiar with the particular technology involved. Ticket size (how
long a ticket is expected to take to complete)  is a key component in how
tickets are sorted in the SRE’s priority queue, so anything we can do to reduce
the size of the ticket makes it more likely to be processed sooner. Taking the
time to learn about that technology and trying to debug ourselves so that we can
provide detailed information on exactly what we need from SREs will make us more
self-sufficient in the longer term, as well as reducing the size of the task
we’re requesting.

As an example, if we’re requesting help debugging a problem on an environment
managed by IS but for which we’re responsible (e.g. github runners,
wordpress-k8s models once migration to sidecar has been done), we should
provide:
 * Exact details on where the environment is hosted so that there’s no doubt as
   to which one we’re referring to.
  * This should include the host the environment is managed from, and the name
    of the role account the host is managed under.
* Specific timing of a particular problem, e.g. between 10:00UTC and 10:07UTC on
  2023-02-03.
* Which log files to look at (e.g. the specific location of the log files on
  disk).
* As much detail about the problem itself as possible.
* Links to the charms involved and their source code.

This is a somewhat contrived example, as we should have COS integration with any
services we’re responsible for that give us visibility on problems without
requiring intervention from SREs, but this may not always be the case if it’s a
new service that doesn’t yet have that functionality, for instance.

As another example, if we’re working with LDAP and need a change to the schema,
we should ideally get a copy of the existing schema, spin up a local copy of
LDAP ourselves (being careful to use the same version of LDAP as in production),
determine what needs to be done to apply the change, and then provide a diff for
SREs to apply, with the specific command involved.

After creating a ticket, please notify a Platform Engineering manager so they
can make sure the ticket is prioritised if appropriate.

Self-service whenever possible
------------------------------

We should avoid creating tickets for anything we can do ourselves, including:

* Firewall changes -
  `documented here <https://docs.admin.canonical.com/is-firewalls/mojo-is-firewalls/user/>`_.
* Proxy configuration changes -
  `create a merge proposal against this repository <https://code.launchpad.net/~canonical-is/canonical-is-internal-proxy-configs/+git/canonical-is-internal-proxy-configs/+ref/master>`_,
  using lpcraft run to test changes.
* `Terraform changes <https://code.launchpad.net/~canonical-is/canonical-terraform-plans/+git/canonical-terraform-plans/+ref/main>`_.
* `Mojo spec changes <https://code.launchpad.net/~canonical-is/canonical-mojo-specs/trunk>`_.
