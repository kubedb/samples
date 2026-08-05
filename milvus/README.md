# Milvus Deployment Guide

This sample shows how to deploy a standalone and distributed Milvus instance using KubeDB.

## Before You Begin

- Install KubeDB with Milvus feature gate enabled:
  ```bash
  helm install kubedb oci://ghcr.io/appscode-charts/kubedb \
    --namespace kubedb --create-namespace \
    --set global.featureGates.Milvus=true
  ```

- Create demo namespace:
  ```bash
  kubectl create namespace demo
  ```

## External Object Storage

Milvus uses MinIO/S3 as object storage. Deploy a MinIO Tenant via the MinIO Operator:

### 1. Install MinIO Operator
```bash
helm repo add minio https://operator.min.io/
helm repo update minio
helm upgrade --install --namespace "minio-operator" --create-namespace "minio-operator" minio/operator --set operator.replicaCount=1
```

### 2. Deploy MinIO Tenant
```bash
helm upgrade --install --namespace "demo" milvus-minio minio/tenant \
  --set tenant.name=milvus-minio \
  --set tenant.pools[0].servers=1 \
  --set tenant.pools[0].volumesPerServer=1 \
  --set tenant.pools[0].size=1Gi \
  --set tenant.certificate.requestAutoCert=false \
  --set tenant.buckets[0].name="mlv-release" \
  --set tenant.pools[0].name="default"
```

### 3. Create Storage Config Secret
Create a secret with MinIO connection details:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: milvus-storage-config
  namespace: demo
type: Opaque
stringData:
  address: "milvus-minio-hl.demo.svc.cluster.local"
  accessKeyID: "minio"
  secretAccessKey: "minio123"
  bucketName: "mlv-release"
  rootPath: "files"
  port: "9000"
```

Apply it:
```bash
kubectl apply -f milvus-storage-config.yaml
```

## Install Etcd Operator

Milvus uses etcd as its metadata store. Install the etcd operator:

```bash
kubectl apply -f https://raw.githubusercontent.com/etcd-io/etcd-operator/refs/heads/main/dist/install-v0.1.0.yaml
```

Verify:
```bash
kubectl get deployment -n etcd-operator-system
kubectl get crd etcdclusters.operator.etcd.io
```

### Externally Managed etcd

Set `spec.metaStorage.externallyManaged: true` and provide your endpoints:

```yaml
metaStorage:
  externallyManaged: true
  endpoints:
    - http://etcdcluster-sample-0.etcdcluster-sample.default.svc.cluster.local:2379
    - http://etcdcluster-sample-1.etcdcluster-sample.default.svc.cluster.local:2379
    - http://etcdcluster-sample-2.etcdcluster-sample.default.svc.cluster.local:2379
```

### Internally Managed etcd (default)

Omit `spec.metaStorage` — KubeDB auto-provisions etcd.

## Deployment

### Standalone
```bash
kubectl apply -f standalone/milvus-standalone.yaml
```

### Distributed
```bash
kubectl apply -f distributed/milvus-distributed.yaml
```

## Verification

```bash
kubectl get pods,svc,pvc -n demo
```
