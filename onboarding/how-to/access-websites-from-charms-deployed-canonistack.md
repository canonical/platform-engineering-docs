# How to access websites from charms deployed on Canonistack (WIP)
**Source: [Discourse](https://discourse.canonical.com/t/how-to-access-websites-from-charms-deployed-on-canonistack-wip/4168)**

This post describes how to access websites for charms deployed on Canonistack. The process is first described generally, and then we will apply the process to three examples: the Discourse charm, the Indico charm and the Jenkins K8s charm.

## General instructions
Follow the instructions on the Canonical Wiki to [set up a Canonistack instance and log in](https://wiki.canonical.com/InformationInfrastructure/IS/CanoniStack-BOS01). Install Juju, bootstrap Juju to a controller, deploy your charm(s) and provide any relevant integrations. Note the IP address for the appropriate charm unit by running `juju status`.

Then, exit from your Canonistack instance and log back in using the `-L` flag and the IP address you previously noted:

```bash
ssh ubuntu@CANONISTACK_IP -i CANONISTACK_SSH_KEY -L 8080:JUJU_UNIT_IP:8080
```

Here, `CANONISTACK_IP` is the IP address of your Canonistack instance, `CANONISTACK_SSH_KEY` is the credentials for your instance, and `JUJU_UNIT_IP` is the IP address for the charm you want to access. Then you may the web page, for instance with [`http://localhost:8080`](http://localhost:8080). You must close the web page before exiting from the SSH command.

### When ingress is involved

Prepare MicroK8s by enabling MetalLB:

```bash
IPADDR=$(ip -4 -j route get 2.2.2.2 | jq -r '.[] | .prefsrc')
sudo microk8s enable metallb:$IPADDR-$IPADDR
```

When you log out of Canonistack and log back in, specify port 80 as your destination machine port for root access:

```bash
ssh ubuntu@CANONISTACK_IP -i CANONISTACK_SSH_KEY -L 8080:INGRESS_IP:80
```

Then you can visit the web page as `http://WEBSITE_URL:8080`.

If you reach an error in your web page browser (e.g., the web page can only be accessed via `http://WEBSITE_URL` with no port, or the connection is refused), you can install a browser extension such as [ModHeader](https://chromewebstore.google.com/detail/modheader-modify-http-hea/idgpnmonknjnojddfkpgkljpfnnfcklj) to set `Host` as `WEBSITE_URL`. Then you can visit the website at [`http://INGRESS_IP:8080`](http://INGRESS_IP:8080).

## Example: Jenkins K8s

Let's first complete the [Jenkins K8s charm tutorial](https://charmhub.io/jenkins-k8s/docs/tutorial-getting-started) on a Canonistack instance. Follow the tutorial's instructions, noting the IP address of the `jenkins-k8s/0` unit using `juju status`. Let's assume the IP address is `10.1.200.150`.

Exit your Canonistack instance and log back in with the `-L` flag and the IP address you noted:

```bash
ssh ubuntu@CANONISTACK_IP -i CANONISTACK_SSH_KEY -L 8080:10.1.200.150:8080
```

Then access your Jenkins server UI at [`http://localhost:8080`](http://localhost:8080) and login using the username "admin" and password you collected during the tutorial.

## Example: Discourse

Let's now complete the [Discourse K8s charm tutorial](https://charmhub.io/discourse-k8s/docs/tutorial) on a Canonistack instance. Follow the tutorial's instructions. When you run `juju status`, note that the `nginx-ingress-integrator` charm specifies the IP address as 127.0.0.1.

Prepare MicroK8s:

```bash
IPADDR=$(ip -4 -j route get 2.2.2.2 | jq -r '.[] | .prefsrc')
sudo microk8s enable metallb:$IPADDR-$IPADDR
```

Exit your Canonistack instance and log back in with the `-L` flag, specifying port 80 as the destination machine port:

```bash
ssh ubuntu@CANONISTACK_IP -i CANONISTACK_SSH_KEY -L 8080:127.0.0.1:80
```

Then access the website via [`http://discourse-k8s:8080`](http://discourse-k8s:8080).

## Example: Indico

Let's now complete the [Indico K8s charm tutorial](https://charmhub.io/indico/docs/tutorial) on a Canonistack instance. Follow the tutorial's instructions. When you run `juju status`, note that the `nginx-ingress-integrator` charm specifies the Ingress IP address as 127.0.0.1.

Prepare MicroK8s:

```bash
IPADDR=$(ip -4 -j route get 2.2.2.2 | jq -r '.[] | .prefsrc')
sudo microk8s enable metallb:$IPADDR-$IPADDR
```

Exit your Canonistack instance and log back in with the `-L` flag, specifying port 80 as the destination machine port:

```bash
ssh ubuntu@CANONISTACK_IP -i CANONISTACK_SSH_KEY -L 8080:127.0.0.1:80
```

Use a browser extension such as ModHeader to add `indico.local` as `Host`. Then you can visit your Indico website at [`http://127.0.0.1:8080`](http://127.0.0.1:8080).


