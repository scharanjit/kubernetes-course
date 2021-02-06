# Taint and Tolerations

# Taint a node
```
kubectl taint nodes NODE-NAME type=specialnode:NoSchedule
```

# Taint a node with NoExecute
```
kubectl taint nodes NODE-NAME testkey=testvalue:NoExecute
```


### Multi-node via minikube 
 minikube start --nodes 3 -p multinode-demo
 
 
### Remove taints 
charanjiths@XXXX tolerations % kubectl taint node multinode-demo-m02 testkey-                    
node/multinode-demo-m02 untainted

charanjiths@XXXX tolerations % kubectl taint node multinode-demo type-                          
node/multinode-demo untainted

 
### Don't run any pod on master

charanjiths@XXXX tolerations % kubectl get nodes
NAME                 STATUS   ROLES                  AGE     VERSION
multinode-demo       Ready    control-plane,master   9m53s   v1.20.0
multinode-demo-m02   Ready    <none>                 9m16s   v1.20.0
multinode-demo-m03   Ready    <none>                 8m27s   v1.20.0



### Tainted the master node

charanjiths@XXXX tolerations % kubectl taint nodes multinode-demo type=testvalue:NoExecute
node/multinode-demo tainted


### No pod deployed on master node

charanjiths@XXXX tolerations % kubectl get pods -o wide
NAME                             READY   STATUS              RESTARTS   AGE   IP            NODE                 NOMINATED NODE   READINESS GATES
tolerations-1-67845bcf6d-7nsfh   0/1     ContainerCreating   0          9s    <none>        multinode-demo-m02   <none>           <none>
tolerations-1-67845bcf6d-hrl6j   1/1     Running             0          9s    10.244.2.11   multinode-demo-m03   <none>           <none>
tolerations-1-67845bcf6d-t4n7l   0/1     ContainerCreating   0          9s    <none>        multinode-demo-m02   <none>           <none>
tolerations-2-84dd659845-44gds   0/1     ContainerCreating   0          9s    <none>        multinode-demo-m03   <none>           <none>
tolerations-2-84dd659845-9lgxf   0/1     ContainerCreating   0          9s    <none>        multinode-demo-m03   <none>           <none>
tolerations-2-84dd659845-nx8c2   0/1     ContainerCreating   0          9s    <none>        multinode-demo-m03   <none>           <none>

