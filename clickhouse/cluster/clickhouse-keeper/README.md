# Installation of ClickHouse Keeper

Create `click-keeper` namespace to install ClickHouse Keeper Server.
```bash
kubectl create ns click-keeper
```
Deploy the ClickHouse Keeper server in `click-keeper` namespace

 ```bash
      kubectl apply -f clickhouse-keeper-1-node.yaml -n click-keeper
 ```

ClickHouse Keeper server is running, and exposed on port `2181`.
To use this ClickhHouse keeper server on your ClickHouse cluster use -
 ```bash
      host: clickhouse-keeper.click-keeper
      port: 2181
 ```

