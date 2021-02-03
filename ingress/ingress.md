charanjiths@XXXX ingress % kubectl create -f ingress.yml 
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/helloworld-rules created

charanjiths@XXXX ingress % kubectl create -f nginx-ingress-controller.yml 
daemonset.apps/nginx-ingress-controller created
configmap/nginx-configuration created
configmap/tcp-services created
configmap/udp-services created
serviceaccount/nginx-ingress-serviceaccount created
Warning: rbac.authorization.k8s.io/v1beta1 ClusterRole is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 ClusterRole
clusterrole.rbac.authorization.k8s.io/nginx-ingress-clusterrole created
Warning: rbac.authorization.k8s.io/v1beta1 Role is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 Role
role.rbac.authorization.k8s.io/nginx-ingress-role created
Warning: rbac.authorization.k8s.io/v1beta1 RoleBinding is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 RoleBinding
rolebinding.rbac.authorization.k8s.io/nginx-ingress-role-nisa-binding created
Warning: rbac.authorization.k8s.io/v1beta1 ClusterRoleBinding is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 ClusterRoleBinding
clusterrolebinding.rbac.authorization.k8s.io/nginx-ingress-clusterrole-nisa-binding created

### 3 services

charanjiths@XXXX ingress % kubectl create -f echoservice.yml 
replicationcontroller/echoheaders created
service/echoheaders-default created

charanjiths@XXXX ingress % kubectl create -f helloworld-v1.yml 
deployment.apps/helloworld-v1-deployment created
service/helloworld-v1 created

charanjiths@XXXX ingress % kubectl create -f helloworld-v2.yml 
deployment.apps/helloworld-v2-deployment created
service/helloworld-v2 created


### Output

charanjiths@XXXX ingress % minikube ip
192.168.49.2

charanjiths@XXXX ingress % curl 192.168.49.2 -H 'Host: helloworld-v1.example.com'

charanjiths@XXXX ingress % curl 192.168.49.2 -H 'Host: helloworld-v2.example.com'

charanjiths@XXXX ingress % kubectl get svc
NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
echoheaders-default   NodePort    10.101.75.218   <none>        80:30302/TCP   14m
helloworld-v1         NodePort    10.102.36.113   <none>        80:30303/TCP   14m
helloworld-v2         NodePort    10.108.42.47    <none>        80:30304/TCP   14m
kubernetes            ClusterIP   10.96.0.1       <none>        443/TCP        8d


### cleaning

charanjiths@XXXX ingress % kubectl delete --all daemonset.apps 
daemonset.apps "nginx-ingress-controller" deleted