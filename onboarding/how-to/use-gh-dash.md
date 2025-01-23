# Using `gh dash` for fun and profit
**Source: [Discourse](https://discourse.canonical.com/t/using-gh-dash-for-fun-and-profit/3945)**

This posts describes how I use the extension dash 3 for the `gh` 1 CLI.
The examples/configuration is tailored for Platform Engineering repositories, though you can reconfigure to suit your needs.
Comments are more than welcome!

## The tl;dr
```
# gh extension install dlvhdr/gh-dash
# gh dash
???
profit
```
## Why `gh dash`
I’m mostly a CLI/TUI person and I will get back to that in a different (set of) post(s).

I like to have most things I interact with in the same env and not having to jump from one app to the other.

My main pain point with reviewing PRs and being on top of issues is that I would rely on e-mail and
GitHub can be especially chatty, and if you silence some notifications, you might miss things (you
can correct me here). I was looking for a workflow where I can glance at all PRs, approve the trivial/bot ones and open a (complex) PR in the browser only when needed.

## The defaults
`gh dash` ships with pretty sane defaults - you have “My Pull Requests”, “Needs My Review” and “Assigned to Me”. The good thing about these tabs is that you can also see the query against the GitHub API that is being made (and you can customize it, which we’ll do in the next section).

```{image} images/gh_dash_main.png
:alt: bastion-systems
:align: center
```
The extension ships with a configuration file, placed under `~/.config/gh-dash/config.yml` by default.

## A custom workflow
I wanted to have a way of looking at all PRs and all issues for repos that Platform Engineering manages.
By default, new PRs are assigned to a team (“is-charms”), though that is at PR creation. If you
join the party late, chances are someone has approved/requested changes and the assignees have changed. That way you won’t see these PRs in the default tabs that gh dash provides.

Enter messing with yaml!

PRs
I added two additional tabs that let me check open PRs on all repos IS DevOps manages and split that
into PRs opened by bots (currently that’s renovate) and the inverse of that:
```
prSections:
- title: My Pull Requests
  filters: is:open author:@me
- title: Needs My Review
  filters: is:open review-requested:@me
- title: Involved
  filters: is:open involves:@me -author:@me
- title: IS Charms
  filters: &repos is:open repo:canonical/netbox repo:canonical/bind-operator repo:canonical/content-cache-k8s-operator repo:canonical/digest-squid-auth-helper repo:canonical/discourse-k8s-operator repo:canonical/element-web-operator repo:canonical/flask-k8s-operator repo:canonical/flask-multipass-saml-groups repo:canonical/gateway-api-integrator-operator repo:canonical/.github repo:canonical/github-actions-exporter-operator repo:canonical/github-runner-operator repo:canonical/github-runner-image-builder-operator repo:canonical/gunicorn-k8s-operator repo:canonical/github-runner-webhook-router repo:canonical/httprequest-lego-provider repo:canonical/indico-operator repo:canonical/irc-bridge-operator repo:canonical/jenkins-agent-deb repo:canonical/jenkins-agent-k8s-operator repo:canonical/jenkins-agent-operator repo:canonical/jenkins-k8s-operator repo:canonical/mattermost-k8s-operator repo:canonical/netbox repo:canonical/nginx-ingress-integrator-operator repo:canonical/openldap-k8s-operator repo:canonical/penpot-operator repo:canonical/repo-policy-compliance repo:canonical/pollen-operator repo:canonical/saml-integrator-operator repo:canonical/smtp-integrator-operator repo:canonical/smtp-relay-operator repo:canonical/synapse-operator repo:canonical/tmate-ssh-server-operator repo:canonical/ubuntu-repository-metadata-operator repo:canonical/wordpress-k8s-operator repo:canonical/matrix-plugins-lib -author:app/renovate
- title: renovate
  filters: &renovate is:open repo:canonical/netbox repo:canonical/bind-operator repo:canonical/content-cache-k8s-operator repo:canonical/digest-squid-auth-helper repo:canonical/discourse-k8s-operator repo:canonical/element-web-operator repo:canonical/flask-k8s-operator repo:canonical/flask-multipass-saml-groups repo:canonical/gateway-api-integrator-operator repo:canonical/.github repo:canonical/github-actions-exporter-operator repo:canonical/github-runner-operator repo:canonical/github-runner-image-builder-operator repo:canonical/gunicorn-k8s-operator repo:canonical/github-runner-webhook-router repo:canonical/httprequest-lego-provider repo:canonical/indico-operator repo:canonical/irc-bridge-operator repo:canonical/jenkins-agent-deb repo:canonical/jenkins-agent-k8s-operator repo:canonical/jenkins-agent-operator repo:canonical/jenkins-k8s-operator repo:canonical/mattermost-k8s-operator repo:canonical/netbox repo:canonical/nginx-ingress-integrator-operator repo:canonical/openldap-k8s-operator repo:canonical/penpot-operator repo:canonical/repo-policy-compliance repo:canonical/pollen-operator repo:canonical/saml-integrator-operator repo:canonical/smtp-integrator-operator repo:canonical/smtp-relay-operator repo:canonical/synapse-operator repo:canonical/tmate-ssh-server-operator repo:canonical/ubuntu-repository-metadata-operator repo:canonical/wordpress-k8s-operator repo:canonical/matrix-plugins-lib author:app/renovate
```

```{image} images/gh_dash_prs_charms.png
:alt: bastion-systems
:align: center
```
```{image} images/gh_dash_prs_renovate.png
:alt: bastion-systems
:align: center
```

## Issues
This is the same as with PRs and I also used the only YAML trick I know…(anchors):

```
issuesSections:
- title: My Issues
  filters: is:open author:@me
- title: Assigned
  filters: is:open assignee:@me
- title: Involved
  filters: is:open involves:@me -author:@me
- title: IS Charms
  filters: *repos
- title: renovate
  filters: *renovate
```

```{image} images/gh_dash_issues_charms.png
:alt: bastion-systems
:align: center
```

## Final remarks
`gh dash` is written in Golang with the [bubble tea](https://github.com/charmbracelet/bubbletea) library for creating nice TUIs and the code is very readable. In fact it was missing the option to approve a PR, you could only merge one, and in a team you normally approve (and then merge, or some auto merge happens…), so I [added that feature](https://github.com/dlvhdr/gh-dash/pull/399).

Another thing I’m missing and would add is the ability to rerun failing tests, which is a simple wrapper
around `gh run rerun`.

Thanks for reading!

