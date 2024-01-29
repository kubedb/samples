# samples

```bash
$ helm install kubedb oci://ghcr.io/appscode-charts/kubedb \
  --version v2024.1.28-rc.1 \
  --namespace kubedb --create-namespace \
  --set-file global.license=/path/to/the/license.txt \
  --set global.featureGates.Druid=true \
  --set global.featureGates.RabbitMQ=true \
  --set global.featureGates.Singlestore=true \
  --set global.featureGates.ZooKeeper=true \
  --wait --burst-limit=10000 --debug
```
