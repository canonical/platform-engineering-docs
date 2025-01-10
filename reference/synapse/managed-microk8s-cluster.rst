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
- `canonical-is-firewalls MP <https://code.launchpad.net/~amandahla/canonical-is-firewalls/+git/canonical-is-firewalls/+merge/477239>`__ external ingress
- `canonical-is-firewalls MP <https://code.launchpad.net/~amandahla/canonical-is-firewalls/+git/canonical-is-firewalls/+merge/477248>`__ firewall rules for bastion/juju controller
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

See `canonical-is-firewalls MP <https://code.launchpad.net/~amandahla/canonical-is-firewalls/+git/canonical-is-firewalls/+merge/478599>`_.

Add the new microk8s cloud
~~~~~~~~~~~~~~~~~~~~~~~~~~

On bastion - **prod-synapse-external** environment

.. code:: bash

    KUBECONFIG=k8s_temp/prod-synapse-microk8s.yaml juju add-k8s cloud-prod-synapse-microk8s

The kubeconfig can be copied from the **prod-synapse-microk8s** environment.

Bootstrap the controller
~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: bash

    juju bootstrap cloud-prod-synapse-microk8s juju-controller-prod-synapse-microk8s-ps6 --config controller-service-type=loadbalancer

Create a Juju model
-------------------

On bastion - **prod-synapse-external** environment

Create the model
~~~~~~~~~~~~~~~~

.. code:: bash

    juju add-model prod-synapse-external
    # change JUJU_CONTROLLER and JUJU_MODEL in .bashrc file too
    juju add-user prod-synapse-external
    juju change-user-password prod-synapse-external
    juju grant prod-synapse-external admin prod-synapse-external

Create the user
~~~~~~~~~~~~~~~

.. code:: bash

    juju add-user prod-synapse-external
    juju change-user-password prod-synapse-external
    juju grant prod-synapse-external admin prod-synapse-external

The password will be used in next step *Apply the terraform plan*.

Apply the terraform plan
------------------------

On bastion - **prod-synapse-external** environment

Since we are using our own Juju controller now, the credentials should be added
to Vault and the providers.tf file needs to be changed as well.

Add credentials to Vault
~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: bash

  load_creds vault
  vault write secret/prodstack6/roles/prod-synapse-external/juju-prod-synapse-microk8s password=[PASSWORD] username=prod-synapse-external
  juju show-controller juju-controller-prod-synapse-microk8s-ps6 --format json| jq '.["juju-controller-prod-synapse-microk8s-ps6"].details["ca-cert"]'|sed 's/\\n/\n/g' > /tmp/cert
  vault write secret/prodstack6/roles/prod-synapse-external/juju-controller-prod-synapse-microk8s-ps6 ca_cert="$(cat /tmp/cert)"

Change the terraform plan
~~~~~~~~~~~~~~~~~~~~~~~~~

The locals.tf and providers.tf need to be updated with the new cloud/vault information.

See this `is-prod-synapse-external PR <https://github.com/canonical/is-prod-synapse-external/pull/11>`_  for reference.

Re-import the model
~~~~~~~~~~~~~~~~~~~

Since the model was re-created, we need to re-import it.

.. code:: bash

    load_creds s3
    terraform state rm module.synapse.juju_model.synapse
    terraform import module.synapse.juju_model.synapse prod-synapse-external

Apply the plan
~~~~~~~~~~~~~~

Upgrade the providers, apply the terraform and verify the changes.

.. code:: bash

  https_proxy=http://squid.internal:3128 NO_PROXY=radosgw.ps6.canonical.com terraform init -upgrade
  terraform plan
  juju status

Configure Ingress
-----------------

Now we need to expose Synapse externally as it was set before via url
`chat.staging.ubuntu.com <https://chat.staging.ubuntu.com>`_ . To do this, first let's enable Ingress in our cluster.

Login in any worker and enable Ingress
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On bastion - **prod-synapse-microk8s** environment

.. code:: bash

 juju ssh microk8s-worker/1
 sudo microk8s enable ingress
 exit

Edit ingress daemonset
~~~~~~~~~~~~~~~~~~~~~~

On bastion - **prod-synapse-external** environment
Note: you can do this in prod-synapse-microk8s as well just mind the namespaces.

Edit ingress daemonset to be deployed on all worker nodes and publish-status-address to 0.0.0.0

It should look like this:

.. code:: bash

    kubectl describe daemonset nginx-ingress-microk8s-controller -n ingress

.. code:: yaml

    Pod Template:
      Labels:           name=nginx-ingress-microk8s
      Service Account:  nginx-ingress-microk8s-serviceaccount
      Containers:
      nginx-ingress-microk8s:
        Image:       registry.k8s.io/ingress-nginx/controller:v1.8.0
        Ports:       80/TCP, 443/TCP, 10254/TCP
        Host Ports:  80/TCP, 443/TCP, 10254/TCP
        Args:
          /nginx-ingress-controller
          --configmap=$(POD_NAMESPACE)/nginx-load-balancer-microk8s-conf
          --tcp-services-configmap=$(POD_NAMESPACE)/nginx-ingress-tcp-microk8s-conf
          --udp-services-configmap=$(POD_NAMESPACE)/nginx-ingress-udp-microk8s-conf
          --ingress-class=public
          
          --publish-status-address=0.0.0.0
          
          nodeSelector:
            node.kubernetes.io/microk8s-worker: microk8s-worker


Extracted backup from previous secret
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Since we have a certificate set, we'll get it from the previous secret.

.. code:: bash

  KUBECONFIG=~/.kube/config-20241218 kubectl get secret nginx-ingress-integrator-cert-tls-secret-chat.staging.ubuntu.com -o yaml > nginx-ingress-integrator-cert-tls-secret-chat.staging.ubuntu.com.bkp.yaml

The kubeconfig can be copied from the **prod-synapse-microk8s** environment.

Re-create the secret
~~~~~~~~~~~~~~~~~~~~

.. code:: bash

  kubectl apply -f nginx-ingress-integrator-cert-tls-secret-chat.staging.ubuntu.com.bkp.yaml

Edit ingress to enable TLS
~~~~~~~~~~~~~~~~~~~~~~~~~~

After applying the terraform, the ingress is created by the NGINX Integrator charm.

.. code:: bash

    kubectl edit ing relation-27-chat-staging-ubuntu-com-ingress

    kubectl get ing relation-27-chat-staging-ubuntu-com-ingress -o yaml

.. code:: yaml

    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      annotations:
        nginx.ingress.kubernetes.io/backend-protocol: HTTP
        nginx.ingress.kubernetes.io/proxy-body-size: 21m
        nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
        nginx.ingress.kubernetes.io/ssl-redirect: "true"
      creationTimestamp: "2024-12-19T17:23:15Z"
      generation: 2
      labels:
        app.juju.is/created-by: nginx-ingress-integrator
        nginx-ingress-integrator.charm.juju.is/managed-by: nginx-ingress-integrator
      name: relation-27-chat-staging-ubuntu-com-ingress
      namespace: prod-synapse-external
      resourceVersion: "5370088"
      uid: f018e32e-a75d-4e48-a343-f425981657be
    spec:
      ingressClassName: public
      rules:
      - host: chat.staging.ubuntu.com
        http:
          paths:
          - backend:
              service:
                name: relation-27-synapse-service
                port:
                  number: 8080
            path: /
            pathType: Prefix
      tls:
      - hosts:
        - chat.staging.ubuntu.com
        secretName: nginx-ingress-integrator-cert-tls-secret-chat.staging.ubuntu.com
    status:
      loadBalancer:
        ingress:
        - ip: 0.0.0.0

Submit firewall rules
~~~~~~~~~~~~~~~~~~~~~

We need to add FW rule to allow access to our Ingress.

This rule also allows communication from the K8S workers to Swift/Rados required by Synapse Media Integration.

See `canonical-is-firewalls MP <https://code.launchpad.net/~amandahla/canonical-is-firewalls/+git/canonical-is-firewalls/+merge/478693>`_.

And this `canonical-is-firewalls MP <https://code.launchpad.net/~amandahla/canonical-is-firewalls/+git/canonical-is-firewalls/+merge/478742>`_  adds the control nodes to proxy access as well.

Change the DNS
~~~~~~~~~~~~~~

The URL chat.staging.ubuntu.com should point for the new IP now.

See `canonical-is-dns-configs MP <https://code.launchpad.net/~amandahla/canonical-is-dns-configs/+git/canonical-is-dns-configs/+merge/478729>`_.

