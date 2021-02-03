charanjiths@XXXX autoscaling % kubectl create -f hpa-example.yml 
deployment.apps/hpa-example created
service/hpa-example created
horizontalpodautoscaler.autoscaling/hpa-example-autoscaler created

charanjiths@XXXX autoscaling % kubectl get hpa                                            
NAME                     REFERENCE                TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
hpa-example-autoscaler   Deployment/hpa-example   <unknown>/50%   1         10        3          3m24s


charanjiths@XXXX autoscaling % kubectl run -i --tty load-generator --image=busybox /bin/sh
If you don't see a command prompt, try pressing enter.

/ # while true;  do wget -q -O- http://hpa-example.default.svc.cluster.local:31001; done


