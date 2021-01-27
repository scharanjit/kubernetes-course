charanjiths@XXXX deployment % kubectl create -f helloworld.yml            
deployment.apps/helloworld-deployment created

charanjiths@XXXX deployment % kubectl get deployments
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment   3/3     3            3           17s


charanjiths@XXXX deployment % kubectl get rs         
NAME                               DESIRED   CURRENT   READY   AGE
helloworld-deployment-7b4f4bd79b   3         3         3       27s

charanjiths@XXXX deployment % kubectl get pods       
NAME                                     READY   STATUS    RESTARTS   AGE
hello-minikube                           1/1     Running   3          2d23h
helloworld-deployment-7b4f4bd79b-lp7m6   1/1     Running   0          33s
helloworld-deployment-7b4f4bd79b-lzstf   1/1     Running   0          33s
helloworld-deployment-7b4f4bd79b-vpqln   1/1     Running   0          33s

charanjiths@XXXX deployment % kubectl get pods --show-labels
NAME                                     READY   STATUS    RESTARTS   AGE     LABELS
hello-minikube                           1/1     Running   3          2d23h   run=hello-minikube
helloworld-deployment-7b4f4bd79b-lp7m6   1/1     Running   0          64s     app=helloworld,pod-template-hash=7b4f4bd79b
helloworld-deployment-7b4f4bd79b-lzstf   1/1     Running   0          64s     app=helloworld,pod-template-hash=7b4f4bd79b
helloworld-deployment-7b4f4bd79b-vpqln   1/1     Running   0          64s     app=helloworld,pod-template-hash=7b4f4bd79b

charanjiths@XXXX deployment % kubectl rollout status deployment/helloworld-deployment
deployment "helloworld-deployment" successfully rolled out

## Service : In order to make this deployment accessible to outside world
charanjiths@XXXX deployment % kubectl expose deployment helloworld-deployment --type=NodePort
service/helloworld-deployment exposed

charanjiths@XXXX deployment % kubectl get service
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
helloworld-deployment    NodePort    10.107.63.84   <none>        3000:32402/TCP   35s
kubernetes               ClusterIP   10.96.0.1      <none>        443/TCP          2d23h
nodehelloworld-service   NodePort    10.107.89.29   <none>        3000:32473/TCP   47h

## To get url
charanjiths@XXXX deployment % minikube service helloworld-deployment --url
🏃  Starting tunnel for service helloworld-deployment.
|-----------|-----------------------|-------------|------------------------|
| NAMESPACE |         NAME          | TARGET PORT |          URL           |
|-----------|-----------------------|-------------|------------------------|
| default   | helloworld-deployment |             | http://127.0.0.1:61068 |
|-----------|-----------------------|-------------|------------------------|
http://127.0.0.1:61068
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.


Now this URL gives the output Hello world

## Update image or release next version of product without downtime

### Build the image and update to docker registry manually

### Set the image
charanjiths@XXXX kubernetes-course % kubectl set image deployment/helloworld-deployment k8s-demo=wardviaene/k8s-demo:2
deployment.apps/helloworld-deployment image updated

### Rollout the image. No downtime required
charanjiths@XXXX kubernetes-course % kubectl rollout status deployment/helloworld-deployment
deployment "helloworld-deployment" successfully rolled out

### Hello World updated to  Hello World v2
charanjiths@XXXX kubernetes-course % curl http://127.0.0.1:61216/
Hello World v2!%   


### Rollout history
charanjiths@XXXX kubernetes-course % kubectl rollout history deployment/helloworld-deployment
deployment.apps/helloworld-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

## Undo the fresh deployment and move to previous version

charanjiths@XXXX kubernetes-course % kubectl rollout undo deployment/helloworld-deployment
deployment.apps/helloworld-deployment rolled back


charanjiths@XXXX kubernetes-course % kubectl get pods
NAME                                     READY   STATUS              RESTARTS   AGE
hello-minikube                           1/1     Running             3          2d23h
helloworld-deployment-557f45b76d-2gfnz   1/1     Terminating         0          6m10s
helloworld-deployment-557f45b76d-kl8kj   1/1     Terminating         0          6m5s
helloworld-deployment-557f45b76d-mnqx5   1/1     Running             0          6m21s
helloworld-deployment-7b4f4bd79b-4fv75   0/1     ContainerCreating   0          3s
helloworld-deployment-7b4f4bd79b-6955v   1/1     Running             0          13s
helloworld-deployment-7b4f4bd79b-fn4qx   1/1     Running             0          8s

### Back to previous version
charanjiths@XXXX kubernetes-course % curl http://127.0.0.1:61216/
Hello World!%    


## By default , kubernetes keeps 2 revision history

charanjiths@XXXX kubernetes-course % kubectl rollout history deployment/helloworld-deployment
deployment.apps/helloworld-deployment 
REVISION  CHANGE-CAUSE
2         <none>
3         <none>

### Update revision history ==> refer screenshot





