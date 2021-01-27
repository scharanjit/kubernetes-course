### Create deployment
charanjiths@XXXX labels % kubectl create -f helloworld-nodeselector.yml 
deployment.apps/helloworld-deployment created


### Get Pods
charanjiths@XXXX labels % kubectl get pods 
NAME                                    READY   STATUS    RESTARTS   AGE
helloworld-deployment-cb6fd95b9-k8d5v   0/1     Pending   0          16s
helloworld-deployment-cb6fd95b9-tw97k   0/1     Pending   0          16s
helloworld-deployment-cb6fd95b9-wp8nm   0/1     Pending   0          16s

##### Why no pod is running? 


charanjiths@XXXX labels % kubectl describe pod helloworld-deployment-cb6fd95b9-wp8nm 
Name:           helloworld-deployment-cb6fd95b9-wp8nm
Namespace:      default
Priority:       0
Node:           <none>

Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  27s   default-scheduler  0/1 nodes are available: 1 node(s) didn't match Pod's node affinity.
  Warning  FailedScheduling  27s   default-scheduler  0/1 nodes are available: 1 node(s) didn't match Pod's node affinity.
  
### Because  nodeSelector:
                    hardware: high-spec
                    
                    We don't have node labeled as high spec
                    
                    
charanjiths@XXXX labels % kubectl get nodes --show-labels
NAME       STATUS   ROLES                  AGE     VERSION   LABELS
minikube   Ready    control-plane,master   3d21h   v1.20.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=minikube,kubernetes.io/os=linux,minikube.k8s.io/commit=9f1e482427589ff8451c4723b6ba53bb9742fbb1,minikube.k8s.io/name=minikube,minikube.k8s.io/updated_at=2021_01_24T03_43_12_0700,minikube.k8s.io/version=v1.16.0,node-role.kubernetes.io/control-plane=,node-role.kubernetes.io/master=


## Let's label this as high spec

charanjiths@XXXX labels % kubectl label node minikube hardware=high-spec
node/minikube labeled


charanjiths@XXXX labels % kubectl get pods
NAME                                    READY   STATUS    RESTARTS   AGE
helloworld-deployment-cb6fd95b9-k8d5v   1/1     Running   0          4m32s
helloworld-deployment-cb6fd95b9-tw97k   1/1     Running   0          4m32s
helloworld-deployment-cb6fd95b9-wp8nm   1/1     Running   0          4m32s
 

