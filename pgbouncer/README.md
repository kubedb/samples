## Install Cert Manager
` kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.0/cert-manager.yaml `

## Create an example issuer
As pre-requisite, at first, we have to create an Issuer.
That will be used to generate the certificate used for TLS settings and internal endpoint authentication of availability group replicas

- Start off by generating our ca-certificates using openssl   
  ` openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./ca.key -out ./ca.crt -subj "/CN=pgbouncer/O=KubeDB" `

- create a secret using the certificate files we have just generated   
  `kubectl create secret tls pgbouncer-ca --cert=ca.crt  --key=ca.key --namespace=demo`

- Now create an Issuer using the pgbouncer-ca secret that contains the ca-certificate we have just created.
  Below is the YAML of the Issuer CR that we are going to create.   
  `kubectl apply -f tls/issuer.yaml`

## To provision TLS enabled Postgres Server
`kubectl apply -f tls/postgres.yaml`

## To provision TLS enabled PgBouncer Server
`kubectl apply -f tls/pgbouncer.yaml`