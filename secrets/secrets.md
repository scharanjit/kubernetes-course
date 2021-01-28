charanjiths@XXXX secrets % kubectl create -f helloworld-secrets.yml 
secret/db-secrets created

charanjiths@XXXX secrets % kubectl create -f helloworld-secrets-volumes.yml 
deployment.apps/helloworld-deployment created

charanjiths@XXXX secrets % kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
helloworld-deployment-7d944b54b6-9x7hw   1/1     Running   0          98s
helloworld-deployment-7d944b54b6-nvkdb   1/1     Running   0          98s
helloworld-deployment-7d944b54b6-tr5d7   1/1     Running   0          98s


charanjiths@XXXX secrets % kubectl exec -it helloworld-deployment-7d944b54b6-9x7hw  /bin/bash
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
root@helloworld-deployment-7d944b54b6-9x7hw:/app# cat /etc/creds/username
root

root@helloworld-deployment-7d944b54b6-9x7hw:/app# cat /etc/creds/password
password

root@helloworld-deployment-7d944b54b6-9x7hw:/app# 