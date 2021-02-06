charanjiths@XXXX affinity % kubectl create -f node-affinity.yaml 
deployment.apps/node-affinity created


charanjiths@XXXX affinity % kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
node-affinity-5f666b6496-chvzg   0/1     Pending   0          20s
node-affinity-5f666b6496-mdwl2   0/1     Pending   0          20s
node-affinity-5f666b6496-vgpm4   0/1     Pending   0          20s


##Since label dint created

charanjiths@XXXX affinity % kubectl get node 
NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   14d   v1.20.0


charanjiths@XXXX affinity %  kubectl label node minikube env=dev
node/minikube labeled

charanjiths@XXXX affinity % kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
node-affinity-5f666b6496-chvzg   1/1     Running   0          2m2s
node-affinity-5f666b6496-mdwl2   1/1     Running   0          2m2s
node-affinity-5f666b6496-vgpm4   1/1     Running   0          2m2s
