charanjiths@XXXX kubernetes-course %    minikube start

charanjiths@XXXX kubernetes-course % kubectl get node
NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   23h   v1.20.0



charanjiths@XXXX kubernetes-course % kubectl create -f first-app/helloworld.yml
pod/nodehelloworld.example.com created


charanjiths@XXXX kubernetes-course % kubectl get pods
NAME                         READY   STATUS              RESTARTS   AGE
hello-minikube               1/1     Running             1          23h
nodehelloworld.example.com   0/1     ContainerCreating   0          58s


charanjiths@XXXX kubernetes-course % kubectl describe pod nodehelloworld.example.com
              node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  2m10s  default-scheduler  Successfully assigned default/nodehelloworld.example.com to minikube
  Normal  Pulling    2m9s   kubelet            Pulling image "wardviaene/k8s-demo"
  Normal  Pulled     33s    kubelet            Successfully pulled image "wardviaene/k8s-demo" in 1m36.3653938s
  Normal  Created    33s    kubelet            Created container k8s-demo
  Normal  Started    33s    kubelet            Started container k8s-demo
  
  
  =====================
  
  
## Multiple Ways to get to this running pod

### 1st way Port Forwarding

charanjiths@XXXX kubernetes-course % kubectl port-forward  nodehelloworld.example.com 8081:3000
Forwarding from 127.0.0.1:8081 -> 3000
Forwarding from [::1]:8081 -> 3000
Handling connection for 8081
Handling connection for 8081
Handling connection for 8081

charanjiths@XXXX ~ % curl localhost:8081
Hello World!%   

### 2nd Way Create a service and expose it

### In minikube , just expose the pod and direct connectly
charanjiths@XXX kubernetes-course % kubectl expose pod nodehelloworld.example.com --type=NodePort --name nodehelloworld-service
service/nodehelloworld-service exposed

###  For aws , method to create a service is different
### IN aws, we have possibilty to use load balancer, which is always better

For minikube

charanjiths@XXXX kubernetes-course % minikube service nodehelloworld-service --url
🏃  Starting tunnel for service nodehelloworld-service.
|-----------|------------------------|-------------|------------------------|
| NAMESPACE |          NAME          | TARGET PORT |          URL           |
|-----------|------------------------|-------------|------------------------|
| default   | nodehelloworld-service |             | http://127.0.0.1:59624 |
|-----------|------------------------|-------------|------------------------|
http://127.0.0.1:59624
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.



  charanjiths@XXXX ~ % kubectl get service
  NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
  kubernetes               ClusterIP   10.96.0.1      <none>        443/TCP          24h
  nodehelloworld-service   NodePort    10.107.89.29   <none>        3000:32473/TCP   5m33s


===============================


##Useful kubectl Commands

### Get pods

charanjiths@XXXX ~ % kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
hello-minikube               1/1     Running   1          24h
nodehelloworld.example.com   1/1     Running   0          42m


### Execute something inside running container


charanjiths@XXXXX ~ % kubectl exec nodehelloworld.example.com -- ls /app
Dockerfile
docker-compose.yml
index-db.js
index.js
misc
node_modules
package.json


### create some file and view inside a running container

charanjiths@XXXX ~ % kubectl exec nodehelloworld.example.com --  touch /app/test.txt 
charanjiths@XXXX ~ % kubectl exec nodehelloworld.example.com -- ls /app               
Dockerfile
docker-compose.yml
index-db.js
index.js
misc
node_modules
package.json
test.txt

#### If container restarts or stop, test.txt will disapper

#### Data is not persistent inside container

#### We need to use volumes , to make data persistent


### Connect container to other container for debugging purpose

charanjiths@XXXX ~ % kubectl get service                            
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes               ClusterIP   10.96.0.1      <none>        443/TCP          24h
nodehelloworld-service   NodePort    10.107.89.29   <none>        3000:32473/TCP   37m

charanjiths@XXXX ~ % kubectl describe service nodehelloworld-service 
Name:                     nodehelloworld-service
Namespace:                default
Labels:                   app=helloworld
Annotations:              <none>
Selector:                 app=helloworld
Type:                     NodePort
IP:                       10.107.89.29
Port:                     <unset>  3000/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  32473/TCP
Endpoints:                172.17.0.4:3000
Session Affinity:         None
External Traffic Policy:  Cluster

#### DEBUG COMMUNICATION BETWEEN CONTAINERS

charanjiths@XXXX ~ % kubectl run -i --tty busybox --image=busybox --restart=Never -- sh
If you don't see a command prompt, try pressing enter.
/ # telnet 172.17.0.4 3000
Connected to 172.17.0.4
GET /

HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 12
ETag: W/"c-7Qdih1MuhjZehB6Sv8UNjA"
Date: Sun, 24 Jan 2021 23:09:12 GMT
Connection: close

Hello World!Connection closed by foreign host

