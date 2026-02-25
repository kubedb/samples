# Milvus Deployment Guide
This document describes the pre-requisites and deployment steps required to provision Milvus on Kubernetes using external object storage and Meta Storage.
## Install External Object Storage (MinIO)
Milvus requires an external object storage backend for storing indexes and data.
We use MinIO as an S3-compatible storage.

Apply the MinIO manifest.
```bash
kubectl apply -f minio.yaml
````

Verify MinIO resources:

```bash
kubectl get pods,svc,pvc -n kubedb
```

---
## Install Etcd Operator
Milvus uses Etcd for metadata storage.
 
Clone Etcd Operator Repository

```bash
git clone https://github.com/etcd-io/etcd-operator
cd etcd-operator
```

---

Install Cert Manager (Required for Etcd Operator)

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.17.2/cert-manager.yaml
```

---

## Build and Deploy Etcd Operator

Build and Push Etcd Operator Image

```bash
make docker-build docker-push IMG=<docker-registry>/etcd-operator:1.0.0
```

Install CRDs and RBAC

```bash
make install
```

Deploy Etcd Operator

```bash
make deploy IMG=<docker-registry>/etcd-operator:1.0.0
```

---

Verify Etcd Operator Installation

```bash
kubectl get all,cm,pvc,secrets,deploy -n etcd-operator-system
```

---
## Etcd Configuration Options

Milvus supports two Etcd modes:
### Externally Managed Etcd (`externallyManaged: true`)

Create Etcd cluster:

```bash
kubectl apply -k config/samples/
```

Verify Etcd resources:

```bash
kubectl get all,cm,pvc,secrets,deploy
```

Add the generated Etcd service endpoints to the Milvus YAML.

---

### Internally Managed Etcd (`externallyManaged: false`)

No action required.

Etcd will be automatically provisioned by KubeDB.

---

## To Provision Milvus (Standalone)

```bash
kubectl apply -f milvus-standalone.yaml
```

---

## To Provision Milvus (Distributed)

```bash
kubectl apply -f milvus-distributed.yaml
```

---

## Verify Milvus Deployment

```bash
kubectl get pods,svc,pvc -n kubedb
```
