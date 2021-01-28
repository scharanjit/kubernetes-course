charanjiths@XXXX wordpress % kubectl create -f wordpress-secrets.yml 
secret/wordpress-secrets created

charanjiths@XXXX wordpress % kubectl create -f wordpress-single-deployment-no-volumes.yml 
deployment.apps/wordpress-deployment created

## This service will expose port 31001

charanjiths@XXXX wordpress % kubectl create -f wordpress-service.yml 
service/wordpress-service created

charanjiths@XXXX wordpress % minikube service wordpress-service --url
🏃  Starting tunnel for service wordpress-service.
|-----------|-------------------|-------------|------------------------|
| NAMESPACE |       NAME        | TARGET PORT |          URL           |
|-----------|-------------------|-------------|------------------------|
| default   | wordpress-service |             | http://127.0.0.1:52073 |
|-----------|-------------------|-------------|------------------------|
http://127.0.0.1:52073
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.

