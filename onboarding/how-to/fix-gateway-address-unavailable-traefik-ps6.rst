How to fix “Gateway Address Unavailable” for Traefik in PS6
===========================================================
**Source:** `Discourse <https://discourse.canonical.com/t/fix-gateway-address-unavailable-for-traefik-in-ps6/2803>`_

We encountered the “Gateway Address Unavailable” issue for Traefik (see
the Discourse post
`Troubleshooting “Gateway Address Unavailable” <https://discourse.charmhub.io/t/traefik-k8s-docs-troubleshooting-gateway-address-unavailable/10813>`_)
on our COS deployment on PS6 using an OpenStack load balancer. The
problem was that Traefik was not assigned an external IP by the
load balancer.

To confirm you’re affected by the same issue, run ``kubectl get svc`` on
the COS namespace. If the Traefik service doesn’t have an external IP
address, you are affected by the same issue.

Environment
~~~~~~~~~~~

* Juju 3.1.6
* ``traefik-k8s latest/edge 163``

Steps
~~~~~

1. We were able to fix this by removing the
   ``loadbalancer.openstack.org/load-balancer-id`` annotation from the
   Traefik service, which triggers a new load balancer.

   .. code::

      kubectl patch svc cos-ingress -p '{"metadata":{"annotations":{"loadbalancer.openstack.org/load-balancer-id":null}}}'

   After a short wait, a new IP should be assigned to Traefik.

2. There is a DNS record  (in this case it was ``cos-ps6.is-devops``) that needs to be
   updated in ``canonical-is-dns-configs``; see the
   `Launchpad repository <https://git.launchpad.net/canonical-is-dns-configs/>`_.

3. (Optional) The firewall rule ``ps6-prod-octavia`` should be updated for Octavia in
   `canonical-is-firewalls <https://git.launchpad.net/canonical-is-firewalls/>`_
   ``defs/subnets.yaml`` *if the new IP does not
   belong within the defined subnets* . This is managed by the Solutions
   engineering team (old Bootstack team) but may require us prompting
   the change as the service uptime is affected.
