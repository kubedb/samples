# Druid Setup and Installation
- **Install ZooKeeper**:
```yaml
apiVersion: kubedb.com/v1alpha2
kind: ZooKeeper
metadata:
  name: zk-cluster
  namespace: druid
spec:
  version: "3.7.2"
  replicas: 3
  disableAuth: false
  storage:
    resources:
      requests:
        storage: "100Mi"
    storageClassName: "standard"
    accessModes:
      - ReadWriteOnce
  terminationPolicy: "WipeOut"
```
- **Install MySQL or PostgreSQL**:
  - Create `mysql-init-script` file with the following command:
    ```sql
    CREATE DATABASE druid;
    ```
  - Create a secret using that file:
    ```bash
    kubectl create configmap -n druid my-init-script \
    --from-file=yamls/mysql/mysql-init-script.sql    
    ```
  - Create MySQL:
    ```sql
        apiVersion: kubedb.com/v1alpha2
        kind: MySQL
        metadata:
          name: mysql-cluster
          namespace: druid
        spec:
          version: "8.0.35"
          topology:
            mode: GroupReplication
          replicas: 3
          storage:
            storageClassName: "standard"
            accessModes:
              - ReadWriteOnce
            resources:
              requests:
                storage: 100Mi
          init:
            script:
              configMap:
                name: my-init-script
    ```
- **Create a S3 Bucket**:
  - Install MinIO and S3 Bucket
  ```bash
  helm upgrade --install --namespace "minio-operator" --create-namespace "minio-operator" minio/operator
  ```
  ```yaml
  helm upgrade --install --namespace "druid" --create-namespace druid-minio minio/tenant  
  ```
  - Create deep-storage-config secret:
  ```yaml
  apiVersion: v1
  kind: Secret
  metadata:
      name: deep-storage-config
      namespace: druid
  stringData:
      druid.storage.type: "s3"
      druid.storage.bucket: "druid"
      druid.storage.baseKey: "druid/segments"
      druid.s3.accessKey: "minio"
      druid.s3.secretKey: "minio123"
      druid.s3.protocol: "http"
      druid.s3.endpoint.signingRegion: "us-east-1"
      druid.s3.enablePathStyleAccess: "true"
      druid.s3.endpoint.url: "http://myminio-hl.druid.svc.cluster.local:9000/"
      druid.indexer.logs.type: "s3"
      druid.indexer.logs.s3Bucket: "druid"
      druid.indexer.logs.s3Prefix: "druid/indexing-logs"
  ```
- **Install Druid**:
```yaml
apiVersion: kubedb.com/v1alpha2
kind: Druid
metadata:
  name: druid-sample
  namespace: druid
spec:
  version: 28.0.1
  storageType: Ephemeral
#  configSecret:
#    name: custom-config
  deepStorage:
    type: s3
    configSecret:
      name: deep-storage-config
  metadataStorage:
    name: mysql-cluster
    namespace: druid
    createTables: true
  zooKeeper:
    name: zk-cluster
    namespace: druid
  topology:
    coordinators:
      replicas: 1
#    overlords:
#      replicas: 1
    brokers:
      replicas: 1
    historicals:
      replicas: 1
      storage:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 1Gi
        storageClassName: standard
    middleManagers:
      replicas: 1
      storage:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 1Gi
        storageClassName: standard
    routers:
      replicas: 1
```