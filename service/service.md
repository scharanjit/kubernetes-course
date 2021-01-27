### Run Minikube

charanjiths@XXXX kubernetes-course % kubectl get node
NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   3d    v1.20.0


### Create deployment


charanjiths@XXXX kubernetes-course % kubectl create -f first-app/helloworld.yml 
pod/nodehelloworld.example.com created

charanjiths@XXXX kubernetes-course % kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
nodehelloworld.example.com   1/1     Running   0          94s


charanjiths@XXXX kubernetes-course % kubectl describe pod nodehelloworld.example.com
Name:         nodehelloworld.example.com
Namespace:    default
Priority:     0
Node:         minikube/192.168.49.2
Start Time:   Wed, 27 Jan 2021 04:12:54 +0530
Labels:       app=helloworld
Annotations:  <none>
Status:       Running
IP:           172.17.0.2
IPs:
  IP:  172.17.0.2
Containers:
  k8s-demo:
    Container ID:   docker://ae85cc0b9024ce06b42efe32f199b159b796bdbc5491ad9b59060a9f7427235f
    Image:          wardviaene/k8s-demo
    Image ID:       docker-pullable://wardviaene/k8s-demo@sha256:2c050f462f5d0b3a6430e7869bcdfe6ac48a447a89da79a56d0ef61460c7ab9e
    Port:           3000/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 27 Jan 2021 04:12:58 +0530
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-f9m2d (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-f9m2d:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-f9m2d
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  9m16s  default-scheduler  Successfully assigned default/nodehelloworld.example.com to minikube
  Normal  Pulling    9m15s  kubelet            Pulling image "wardviaene/k8s-demo"
  Normal  Pulled     9m12s  kubelet            Successfully pulled image "wardviaene/k8s-demo" in 3.2825693s
  Normal  Created    9m12s  kubelet            Created container k8s-demo
  Normal  Started    9m11s  kubelet            Started container k8s-demo
  
### create service

charanjiths@XXXX kubernetes-course % kubectl create -f service/helloworld-nodeport-service.yml
service/helloworld-service created

charanjiths@XXXX kubernetes-course % minikube service helloworld-service --url
🏃  Starting tunnel for service helloworld-service.
|-----------|--------------------|-------------|------------------------|
| NAMESPACE |        NAME        | TARGET PORT |          URL           |
|-----------|--------------------|-------------|------------------------|
| default   | helloworld-service |             | http://127.0.0.1:62681 |
|-----------|--------------------|-------------|------------------------|
http://127.0.0.1:62681
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.


charanjiths@XXXX service % curl  http://127.0.0.1:62681
Hello World!%                                                                                           

### describe service

charanjiths@XXXX service % kubectl describe svc helloworld-service             
Name:                     helloworld-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=helloworld
Type:                     NodePort
IP:                       10.101.174.41
Port:                     <unset>  31001/TCP
TargetPort:               nodejs-port/TCP
NodePort:                 <unset>  31001/TCP
Endpoints:                172.17.0.2:3000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>


NodePort is static
Port will change everytime on service restart