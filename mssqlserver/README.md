As pre-requisite, at first, we have to create an Issuer.
That will be used to generate the certificate used for TLS settings and internal endpoint authentication of availability group replicas

## create an example issuer
Start off by generating our ca-certificates using openssl    
` openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./ca.key -out ./ca.crt -subj "/CN=MSSQLServer/O=KubeDB" `

create a secret using the certificate files we have just generated,  
`kubectl create secret tls mssqlserver-ca --cert=ca.crt  --key=ca.key --namespace=sample`


Now, we are going to create an Issuer using the mssqlserver-ca secret that contains the ca-certificate we have just created.
Below is the YAML of the Issuer CR that we are going to create.  
`kubectl apply -f mssqlserver-issuer.yaml`

## To provision Standalone MS SQL Server 
`kubectl apply -f mssqlserver-standalone.yaml`

## To provision MS SQL Server Availability Group cluster
`kubectl apply -f mssqlserver-ag.yaml`



