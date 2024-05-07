**First need to create config secret for postgres with desired max_connections value else default max_connections will be 100**
```bash
kubectl create secret generic -n demo user-conf --from-file=./user.conf
```
**Now create Postgres**
```bash
kubectl create -f postgres.yaml
```
**Now create Pgpool**
```bash
kubectl create -f pgpool.yaml
```
**Pooling configuration tips**
num_init_children must be greater than 4 also max_pool per children must be greater or equal number of pgpool replicas * 15. So, finally total connections supported will be (number of pgpool replicas * num_init_children * max_pool). So, make sure postgres max_connections value match this. If these requirements is not properly followed, pgpool will not be ready.
