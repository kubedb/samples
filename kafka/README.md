**Kafka Samples**

**To create kafka combined instance**
```bash
kubectl apply -f kafka-combined.yaml
```
**To create kafka topology instance**
```bash
kubectl apply -f kafka-topology.yaml
```
**To create kafka connect cluster instance**
```bash
kubectl apply -f connectcluster/connect-cluster.yaml
```
**To create mongodb source connector for connect cluster instance**
```bash
kubectl apply -f connector/mg-source-connector.yaml
```