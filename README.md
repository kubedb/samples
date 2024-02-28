# samples

```bash
$ helm upgrade -i kubedb oci://ghcr.io/appscode-charts/kubedb \
    --version v2024.2.14 \
    --namespace kubedb --create-namespace \
    --set-file global.license=${LICENSE} \
    --set kubedb-provisioner.operator.securityContext.seccompProfile.type=RuntimeDefault \
    --set kubedb-webhook-server.server.securityContext.seccompProfile.type=RuntimeDefault \
    --set kubedb-ops-manager.operator.securityContext.seccompProfile.type=RuntimeDefault \
    --set kubedb-autoscaler.operator.securityContext.seccompProfile.type=RuntimeDefault \
    --set global.featureGates.RabbitMQ=true \
    --set global.featureGates.ZooKeeper=true \
    --set global.featureGates.Solr=true \
    --set global.featureGates.Druid=true \
    --set global.featureGates.Pgpool=true \
    --set global.featureGates.FerretDB=true \
    --set global.featureGates.Singlestore=true \
    --set kubedb-provisioner.imagePullPolicy=Always \
    --set kubedb-webhook-server.imagePullPolicy=Always
```
