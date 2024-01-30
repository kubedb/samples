# Druid Setup and Installation
- **Install ZooKeeper**:

```bash
kubectl apply -f zookeeper.yaml
```
- **Install MySQL or PostgreSQL**:
  - Create a configMap using mysql-init-script file:
  
    ```bash
    kubectl create configmap -n druid my-init-script \
    --from-file=mysql-init-script.sql    
    ```
  - Create MySQL:
    ```bash
    kubectl apply -f mysql.yaml
    ```
- **Create a S3 Bucket**:
  - Install MinIO and S3 Bucket
  
  ```bash
  helm upgrade --install --namespace "minio-operator" --create-namespace "minio-operator" minio/operator -f minio-operator-override.yaml
  ```
  ```bash
  helm upgrade --install --namespace "druid" --create-namespace druid-minio \
  minio/tenant -f minio-tenant-override.yaml
  ```
  - Create deep-storage-config secret:
  
  ```bash
  kubectl apply -f deep-storage-config.yaml
  ```
- **Install Druid**:

```bash
  kubectl apply -f druid.yaml
```