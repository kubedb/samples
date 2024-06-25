## Install Cert Manager
` kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.0/cert-manager.yaml `

## Create an example issuer
As pre-requisite, at first, we have to create an Issuer.
That will be used to generate the certificate used for TLS settings and internal endpoint authentication of availability group replicas

- Start off by generating our ca-certificates using openssl   
` openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./ca.key -out ./ca.crt -subj "/CN=MSSQLServer/O=KubeDB" `

- create a secret using the certificate files we have just generated   
`kubectl create secret tls mssqlserver-ca --cert=ca.crt  --key=ca.key --namespace=demo`

- Now create an Issuer using the mssqlserver-ca secret that contains the ca-certificate we have just created.
Below is the YAML of the Issuer CR that we are going to create.   
`kubectl apply -f issuer.yaml`

## To provision Standalone MS SQL Server 
`kubectl apply -f standalone.yaml`

## To provision MS SQL Server Availability Group cluster
`kubectl apply -f availability-group.yaml`



## For TLS enabled MS SQL Server: Install csi-driver-cacerts
which will be used to add self-signed ca certificates to the OS trusted certificate issuers (eg, /etc/ssl/certs/ca-certificates.crt

```
$ helm repo add appscode https://charts.appscode.com/stable/
$ helm repo update
$ helm upgrade -i \
  cert-manager-csi-driver-cacerts appscode/cert-manager-csi-driver-cacerts \
  -n cert-manager --wait
```

## To provision TLS enabled Standalone MS SQL Server 
`kubectl apply -f tls/standalone.yaml`

## To provision TLS enabled MS SQL Server Availability Group cluster
`kubectl apply -f tls/availablity-group.yaml`
