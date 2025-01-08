Managed MicroK8S Cluster
========================

We chose to set up our own cluster for the system to enhance stability and
autonomy, ensuring it operates independently of other environments.

This reference will explain the steps required for doing that considering
specifically the Synapse architecture.

Create Microk8s Cluster
-----------------------

The spec `IS0057 <https://docs.google.com/document/d/1mgfNZ5KKWMtudUTWGYzR9GrVE4A0mVD4hIxKpzHC-AM/edit?usp=sharing>`_ describes how to create a MicroK8S cluster
and it was used for creating the environment.

TL;DR

- Create a machine environment
- Set all firewall access
- Create a Juju controller

The machine environment was created via GitOps so is defined in the `infrastructure-services repo <https://github.com/canonical/infrastructure-services/blob/main/services/definitions/compute/prod-synapse-microk8s.yaml>`_.

The reason for all the following MPs are described in the spec.

- `canonical-is-internal-proxy-configs MP <https://code.launchpad.net/~amandahla/canonical-is-internal-proxy-configs/+git/canonical-is-internal-proxy-configs/+merge/477156>`_ to allow access to Squid proxy:
- `canonical-terraform-plans MP <https://code.launchpad.net/~amandahla/canonical-terraform-plans/+git/canonical-terraform-plans/+merge/477157>`_ to add the microk8s terraform plan
- `canonical-is-firewalls MP <https://code.launchpad.net/~amandahla/canonical-is-firewalls/+git/canonical-is-firewalls/+merge/477239>`_ external ingress
- `canonical-is-firewalls MP <https://code.launchpad.net/~amandahla/canonical-is-firewalls/+git/canonical-is-firewalls/+merge/477248>`_ firewall rules for bastion/juju controller
- `canonical-is-dns-configs MP <https://code.launchpad.net/~amandahla/canonical-is-dns-configs/+git/canonical-is-dns-configs/+merge/477352>`_ DNS for microk8s workers

The next step accordingly to the spec would be registering the cluster as a
cloud and request it via ticket like it was done in the `C167497 <https://portal.admin.canonical.com/C167497/>`_.
But IS mentioned that issues in our cluster could affect other environments so
the solution was to create our own Juju controller.

Create a Juju controller
------------------------

Before bootstrapping the controller, we need to enable Metallb to use the
workers IP as a pool.

Login in any worker and enable Metallb
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On bastion - **prod-synapse-microk8s** environment

.. code:: bash

 juju ssh microk8s-worker/1
 IPADDR=$(ip -4 -j route get 2.2.2.2 | jq -r '.[] | .prefsrc')
 sudo microk8s enable metallb:$IPADDR-$IPADDR
 exit

Manually edit the resource by adding other workers IP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: bash

 kubectl edit IPAddressPool default-addresspool -n metallb-system

 kubectl get IPAddressPool default-addresspool -n metallb-system -o yaml

.. code:: yaml

 apiVersion: metallb.io/v1beta1
 kind: IPAddressPool
 metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"metallb.io/v1beta1","kind":"IPAddressPool","metadata":{"annotations":{},"name":"default-addresspool","namespace":"metallb-system"},"spec":{"addresses":["10.141.124.178/32"]}}
  creationTimestamp: "2024-12-18T14:22:32Z"
  generation: 2
  name: default-addresspool
  namespace: metallb-system
  resourceVersion: "5062482"
  uid: e9cc8322-402a-449e-b367-cfff684fb44c
 spec:
  addresses:
  - 10.141.124.41/32
  - 10.141.124.135/32
  - 10.141.124.250/32
  autoAssign: true

Submit FW rule allowing bastion to controller
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is the `MP <https://code.launchpad.net/~amandahla/canonical-is-firewalls/+git/canonical-is-firewalls/+merge/478599>`_.

Add the new microk8s cloud
~~~~~~~~~~~~~~~~~~~~~~~~~~

On bastion - **prod-synapse-external** environment

.. code:: bash
    KUBECONFIG=k8s_temp/prod-synapse-microk8s.yaml juju add-k8s cloud-prod-synapse-microk8s

The kubeconfig can be copied from the **prod-synapse-microk8s** environment.

