charanjiths@XXXX kubernetes-course % kubectl create -f first-app/helloworld.yml 
 pods "nodehelloworld.example.com" created

charanjiths@XXXX kubernetes-course % kubectl create -f first-app/helloworld-service.yml 
service/helloworld-service created


In helloworld-service. we have   type: LoadBalancer

==> After execution of above two commands, it will create a 
Load balancer which will point to our  3 aws instances
 1 master and 2 slave pods
 
 
 ==> Now create a record set with alias in Route 53
 
 ==> Se can access our app with record set.
 
 
 ### Traffic Flow
 Route 53 goes to load balancer
 Load balancer directs the traffic from port 80 to one of the 3 runnig instances


