charanjiths@XXXX resourcequotas % kubectl create -f resourcequota.yml 
namespace/myspace created
resourcequota/compute-quota created
resourcequota/object-quota created


charanjiths@XXXX resourcequotas % kubectl create -f helloworld-no-quotas.yml 
deployment.apps/helloworld-deployment created


charanjiths@XXXX resourcequotas % kubectl get deployment  --namespace=myspace
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment   0/3     0            0           101s


### The deployment is not running because

charanjiths@XXXX resourcequotas % kubectl get rs  --namespace=myspace
NAME                               DESIRED   CURRENT   READY   AGE
helloworld-deployment-7b4f4bd79b   3         0         0       3m14s



charanjiths@XXXX resourcequotas % kubectl describe rs/helloworld-deployment-7b4f4bd79b --namespace=myspace
Name:           helloworld-deployment-7b4f4bd79b
Namespace:      myspace
Selector:       app=helloworld,pod-template-hash=7b4f4bd79b
Labels:         app=helloworld
                pod-template-hash=7b4f4bd79b
Annotations:    deployment.kubernetes.io/desired-replicas: 3
                deployment.kubernetes.io/max-replicas: 4
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/helloworld-deployment
Replicas:       0 current / 3 desired
Pods Status:    0 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=helloworld
           pod-template-hash=7b4f4bd79b
  Containers:
   k8s-demo:
    Image:        wardviaene/k8s-demo
    Port:         3000/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type             Status  Reason
  ----             ------  ------
  ReplicaFailure   True    FailedCreate
Events:
  Type     Reason        Age                 From                   Message
  ----     ------        ----                ----                   -------
  Warning  FailedCreate  4m5s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-wp6lq" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  4m5s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-hfk8p" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  4m5s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-dh9gd" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  4m5s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-rzmkj" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  4m5s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-dtnhb" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  4m5s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-2m2cf" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  4m5s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-2z2d6" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  4m4s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-f6f5g" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  4m4s                replicaset-controller  Error creating: pods "helloworld-deployment-7b4f4bd79b-vrpwx" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  Warning  FailedCreate  81s (x7 over 4m3s)  replicaset-controller  (combined from similar events): Error creating: pods "helloworld-deployment-7b4f4bd79b-s9ldw" is forbidden: failed quota: compute-quota: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
  
  
  
### Therefore, delete this deployment


charanjiths@XXXX resourcequotas % kubectl delete deploy/helloworld-deployment --namespace=myspace
deployment.apps "helloworld-deployment" deleted


### ASKED FOR THREE REPLICAS BUT GOT 2 AS IT EXCEEDED QUOTA

charanjiths@XXXX resourcequotas % kubectl create -f helloworld-with-quotas.yml 
deployment.apps/helloworld-deployment created

charanjiths@XXXX resourcequotas % kubectl get deployment --namespace=myspace
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment   2/3     2            2           2m15s

charanjiths@XXXX resourcequotas % kubectl get pod --namespace=myspace
NAME                                     READY   STATUS    RESTARTS   AGE
helloworld-deployment-6fb5dc7b75-56b56   1/1     Running   0          34s
helloworld-deployment-6fb5dc7b75-kg8h7   1/1     Running   0          34s

charanjiths@XXXX resourcequotas % kubectl get rs --namespace=myspace
NAME                               DESIRED   CURRENT   READY   AGE
helloworld-deployment-6fb5dc7b75   3         2         2       3m1s
charanjiths@XXXX resourcequotas % kubectl describe rs/helloworld-deployment-6fb5dc7b75 --namespace=myspace
Name:           helloworld-deployment-6fb5dc7b75
Namespace:      myspace
Selector:       app=helloworld,pod-template-hash=6fb5dc7b75
Labels:         app=helloworld
                pod-template-hash=6fb5dc7b75
Annotations:    deployment.kubernetes.io/desired-replicas: 3
                deployment.kubernetes.io/max-replicas: 4
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/helloworld-deployment
Replicas:       2 current / 3 desired
Pods Status:    2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=helloworld
           pod-template-hash=6fb5dc7b75
  Containers:
   k8s-demo:
    Image:      wardviaene/k8s-demo
    Port:       3000/TCP
    Host Port:  0/TCP
    Limits:
      cpu:     400m
      memory:  1Gi
    Requests:
      cpu:        200m
      memory:     512Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type             Status  Reason
  ----             ------  ------
  ReplicaFailure   True    FailedCreate
Events:
  Type     Reason            Age                  From                   Message
  ----     ------            ----                 ----                   -------
  Normal   SuccessfulCreate  3m25s                replicaset-controller  Created pod: helloworld-deployment-6fb5dc7b75-56b56
  Warning  FailedCreate      3m25s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-t8qrr" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Normal   SuccessfulCreate  3m25s                replicaset-controller  Created pod: helloworld-deployment-6fb5dc7b75-kg8h7
  Warning  FailedCreate      3m25s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-6vpx9" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      3m25s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-f92cw" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      3m25s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-x84b6" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      3m25s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-xwqcm" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      3m25s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-tkd9j" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      3m25s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-62jtl" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      3m24s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-84zjb" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      3m24s                replicaset-controller  Error creating: pods "helloworld-deployment-6fb5dc7b75-gkwm7" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      31s (x8 over 3m22s)  replicaset-controller  (combined from similar events): Error creating: pods "helloworld-deployment-6fb5dc7b75-xq98m" is forbidden: exceeded quota: compute-quota, requested: limits.memory=1Gi,requests.memory=512Mi, used: limits.memory=2Gi,requests.memory=1Gi, limited: limits.memory=2Gi,requests.memory=1Gi




### Quota usage

charanjiths@XXXX resourcequotas % kubectl get quota --namespace=myspace
NAME            AGE   REQUEST                                                                                                                                   LIMIT
compute-quota   14m   requests.cpu: 400m/1, requests.memory: 1Gi/1Gi                                                                                            limits.cpu: 800m/2, limits.memory: 2Gi/2Gi
object-quota    14m   configmaps: 1/10, persistentvolumeclaims: 0/4, replicationcontrollers: 0/20, secrets: 1/10, services: 0/10, services.loadbalancers: 0/2  


charanjiths@XXXX resourcequotas % kubectl describe quota/compute-quota --namespace=myspace
Name:            compute-quota
Namespace:       myspace
Resource         Used  Hard
--------         ----  ----
limits.cpu       800m  2
limits.memory    2Gi   2Gi
requests.cpu     400m  1
requests.memory  1Gi   1Gi

