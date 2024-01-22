**First need to create Singlestore License Secret**
```bash
kubectl create secret generic -n demo license-secret \
  --from-literal=username=license \
  --from-literal=password='BGJjOGVjNDBlNmFjZTRhZDViODJmNWI4YmNjY2E0YTM5AAAAAAAAAAAEAAAAAAAAACgwNQIYKYMrNgqNFLwaIVWUzxBtVhtUV/9Tmg4JAhkA/pwwvZ1xVUKVCFIL5Wk1rAUcz20KtaoVAA=='
```
**To create singlesore standalone-mode object**
```bash
kubectl apply -f singlestore/standalone-mode/standalone.yaml 
```
**To create singlestore cluster-mode object**
```bash
kubectl apply -f singlestore/cluster-mode/cluster.yaml 
```