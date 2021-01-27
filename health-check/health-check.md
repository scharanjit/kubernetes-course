charanjiths@XXXX health-check % kubectl create -f helloworld-healthcheck.yml 
deployment.apps/helloworld-deployment created

charanjiths@XXXX health-check % kubectl get pods
NAME                                   READY   STATUS              RESTARTS   AGE
helloworld-deployment-f9fd9fd4-8879c   1/1     Running             0          11s
helloworld-deployment-f9fd9fd4-nvv99   0/1     ContainerCreating   0          11s
helloworld-deployment-f9fd9fd4-v55cx   1/1     Running             0          11s

charanjiths@XXXX health-check % kubectl describe pod helloworld-deployment-f9fd9fd4-8879c

   Ready:          True
    Restart Count:  0
    Liveness:       http-get http://:nodejs-port/ delay=15s timeout=30s period=10s #success=1 #failure=3
    Environment:    <none>
    Mounts:


### Pod health  with  readiness  + liveliness

charanjiths@XXXX health-check % kubectl create -f helloworld-liveness-readiness.yml && watch -n1 kubectl get pods 

deployment.apps/helloworld-readiness created

Every 1.0s: kubectl get pods                             XXXX.local: Thu Jan 28 02:34:50 2021

NAME                                    READY   STATUS    RESTARTS   AGE
helloworld-readiness-77f955c568-49ksl   0/1     Running   0          24s
helloworld-readiness-77f955c568-8hs5n   1/1     Running   0          25s
helloworld-readiness-77f955c568-j6vb2   1/1     Running   0          24s


charanjiths@XXXX health-check % kubectl create -f helloworld-liveness-readiness.yml && watch -n1 kubectl get pods 

deployment.apps/helloworld-readiness created

Every 1.0s: kubectl get pods                             XXXX.local: Thu Jan 28 02:35:25 2021

NAME                                    READY   STATUS    RESTARTS   AGE
helloworld-readiness-77f955c568-49ksl   1/1     Running   0          59s
helloworld-readiness-77f955c568-8hs5n   1/1     Running   0          60s
helloworld-readiness-77f955c568-j6vb2   1/1     Running   0          59s


