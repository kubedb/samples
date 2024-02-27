**First need to create Singlestore License Secret**
```bash
kubectl create secret generic -n sdb license-secret \
  --from-literal=username=license \
  --from-literal=password='******'
```
**To create singlesore standalone-mode object**
```bash
kubectl apply -f singlestore/standalone-mode/standalone.yaml 
```
**To create singlestore cluster-mode object**
```bash
kubectl apply -f singlestore/cluster-mode/cluster.yaml 
```
