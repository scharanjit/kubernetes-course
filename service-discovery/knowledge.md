charanjiths@XXXX kubernetes-course % kubectl get pods
No resources found in default namespace.


charanjiths@XXXX kubernetes-course % kubectl get pods --namespace=kube-system
NAME                               READY   STATUS    RESTARTS   AGE
coredns-74ff55c5b-bl6bl            1/1     Running   6          5d
etcd-minikube                      1/1     Running   6          5d
kube-apiserver-minikube            1/1     Running   6          5d
kube-controller-manager-minikube   1/1     Running   6          5d
kube-proxy-wsp4l                   1/1     Running   6          5d
kube-scheduler-minikube            1/1     Running   6          5d
storage-provisioner                1/1     Running   19         5d

in kube-system name space, there are few pods which are always running



Service Discovery ==> When a container from pod1 wants to connect container
 of pod2 , it needs to define service
The whole process achieved by Service Discovery.


======


charanjiths@XXXX service-discovery % kubectl create -f secrets.yml 
secret/helloworld-secrets created

charanjiths@XXXX service-discovery % kubectl create -f database.yml
pod/database created

charanjiths@XXXX service-discovery % kubectl create -f database-service.yml 
service/database-service created

charanjiths@XXXX service-discovery % kubectl create  -f helloworld-db.yml 
deployment.apps/helloworld-deployment created

charanjiths@XXXX service-discovery % kubectl create -f helloworld-db-service.yml 
service/helloworld-db-service created

charanjiths@XXXX service-discovery % kubectl get pods
NAME                                    READY   STATUS    RESTARTS   AGE
database                                1/1     Running   0          3m
helloworld-deployment-8cdd6879f-4gmqj   1/1     Running   0          23s
helloworld-deployment-8cdd6879f-bqs9k   1/1     Running   0          23s
helloworld-deployment-8cdd6879f-vjt9w   1/1     Running   0          23s

charanjiths@XXXX service-discovery % minikube service helloworld-db-service --url
🏃  Starting tunnel for service helloworld-db-service.
|-----------|-----------------------|-------------|------------------------|
| NAMESPACE |         NAME          | TARGET PORT |          URL           |
|-----------|-----------------------|-------------|------------------------|
| default   | helloworld-db-service |             | http://127.0.0.1:62958 |
|-----------|-----------------------|-------------|------------------------|
http://127.0.0.1:62958
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.


========

Mysql is running on Pod 1 (pod name database)

App is in Pod 2

App is able to connect to Mysql Container running in Pod 1


======

charanjiths@XXXX service-discovery % kubectl get svc                            
NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
database-service        NodePort    10.97.164.91     <none>        3306:30175/TCP   11m
helloworld-db-service   NodePort    10.102.226.125   <none>        3000:31301/TCP   8m11s
kubernetes              ClusterIP   10.96.0.1        <none>        443/TCP          7d23h

### Debug Mode

charanjiths@XXXX service-discovery % kubectl run -i --tty busybox --image=busybox --restart=Never -- sh
If you don't see a command prompt, try pressing enter.
/ # nslookup database-service
Server:		10.96.0.10
Address:	10.96.0.10:53

Name:	database-service.default.svc.cluster.local
Address: 10.97.164.91



^C
/ # nslookup helloworld-db-service
Server:		10.96.0.10
Address:	10.96.0.10:53

Name:	helloworld-db-service.default.svc.cluster.local
Address: 10.102.226.125


### Debug Mode

charanjiths@XXXX service-discovery % kubectl run -i --tty busybox --image=busybox --restart=Never -- sh
If you don't see a command prompt, try pressing enter.
/ # telnet database-service  3000
^C
/ # telnet helloworld-db-service  3000
Connected to helloworld-db-service
GET /

HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 38
ETag: W/"26-8XSczjvd8ImmRePIRevQpQ"
Date: Sun, 31 Jan 2021 21:48:48 GMT
Connection: close

Hello World! You are visitor number 25Connection closed by foreign host