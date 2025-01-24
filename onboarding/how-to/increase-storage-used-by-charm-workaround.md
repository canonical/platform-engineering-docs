# Increase storage used by a charm (workaround)
**Source: [Discourse](https://discourse.canonical.com/t/increase-storage-used-by-a-charm-workaround/2760)**

Currently, there is no way of resizing storage via `juju` command so this document explains how to do it by doing a backup of the Prometheus K8S charm database, removing the charm, deploying with the new storage, and checking if it worked.

While working on this document, it was tried to:
-  Change the PVC/PV resources: failed because it was not reflected on the Juju storage entity.
- Change the [`metadata.yaml`](https://juju.is/docs/sdk/metadata-yaml#heading--storage) requiring bigger storage and refresh the charm: failed because is not allowed.

## Environment
* Juju 3.1.6
* Prometheus K8S latest/edge  154

## Steps

1. Create a snapshot of your Prometheus data.
Follow the how to: [Enable Admin API and create a snapshot for Prometheus K8S charm (workaround)](https://discourse.canonical.com/t/enable-admin-api-in-prometheus-k8s-charm-workaround/2759)

      1.1 After creating the snapshot, copy it to your local system to be restored later.
      ```bash
      juju ssh  --container prometheus prometheus/0 /bin/bash
      cd /var/lib/prometheus/snapshots
      tar zcvf /prometheus-snapshot-20231120.tar.gz 20231120T174358Z-46d365675da92d7c/*
      juju scp --container prometheus prometheus/0:/snapshots/prometheus-snapshot-20231120.tar.gz       prometheus-snapshot-20231120.tar.gz
      ```

      1.2 Check the TSDB status to compare later
      Access [`http://10.10.10.10:9090/tsdb-status`](http://10.10.10.10:9090/tsdb-status) where 10.10.10.10 is the Juju Unit IP.

2. Remove offers if any (don&#39;t use --force parameter)

```bash
juju remove-offer prometheus
juju remove-offer prometheus-receive-remote-write
```
If you get an error like `ERROR current model for controller $YOUR-CONTROLLER not found`, mind the issue regarding JUJU_MODEL and JUJU_CONTROLLER environment variables [here](https://bugs.launchpad.net/juju/+bug/2038800).

If you get an error like `ERROR cannot delete application offer "prometheus": offer has 1 relation` you can use the parameter `--force` but be aware that this might have unexpected behavior later as you can check in this [Charmhub thread](https://chat.charmhub.io/charmhub/pl/yqy3ofefhf8yjbn1wwgehomwpo).

3. Remove Prometheus application (don't use `--force` parameter)
```bash
juju remove-application prometheus
juju status # check if everything looks fine
```

4. Deploy Prometheus with the new storage
```bash
juju deploy prometheus-k8s prometheus --channel latest/edge --revision 154 --trust --storage database=100GB,kubernetes
```

5. Restore the snapshot
```bash
juju scp --container prometheus ./prometheus-snapshot-20231120.tar.gz prometheus/0:/prometheus-snapshot-20231120.tar.gz
juju ssh  --container prometheus prometheus/0 /bin/bash
cd /var/lib/prometheus/
rm -rf ./*
tar zxvf /prometheus-snapshot-20231120.tar.gz
mv 20231120T174358Z-46d365675da92d7c/* .
rm -rf 20231120T174358Z-46d365675da92d7c
```
6. Create the integrations
```bash
juju relate prometheus:ingress cos-ingress:ingress-per-unit
juju relate prometheus:alertmanager alertmanager
juju relate alertmanager prometheus:metrics-endpoint
juju relate grafana prometheus:metrics-endpoint
juju relate loki prometheus
juju relate prometheus:grafana-dashboard grafana:grafana-dashboard
juju relate prometheus:grafana-source grafana:grafana-source
```

7. Create the offers
```bash
juju offer admin/prod-cos-k8s-ps6-is-charms.prometheus:receive-remote-write prometheus-receive-remote-write
juju offer admin/prod-cos-k8s-ps6-is-charms.prometheus:metrics-endpoint prometheus
```

8. Grant permissions
```bash
juju grant prod-synapse-k8s consume prodstack-is:admin/prod-cos-k8s-ps6-is-charms.prometheus
juju grant prod-chat-synapse-db consume prodstack-is:admin/prod-cos-k8s-ps6-is-charms.prometheus
juju grant prod-chat-synapse-db consume prodstack-is:admin/prod-cos-k8s-ps6-is-charms.prometheus-receive-remote-write
```

You'll need to create the integrations again from the consumer's side as well.

9. Access the Prometheus console and check if there are metrics from now and from the snapshot
```
http://10.10.10.10:9090/graph
```

## Make this guide work for Jenkins

```
bash backup.sh
juju scp --container jenkins jenkins-k8s/0:/backup/jenkins_backup.tar.gz jenkins_backup.tar.gz
juju scp --container jenkins ./jenkins_backup.tar.gz jenkins-k8s/0:/jenkins_backup.tar.gz
tar zxvf jenkins_backup.tar.gz
chown -R 2000:2000 /backup
cp -R /backup/* /var/lib/jenkins
pebble restart jenkins
```


