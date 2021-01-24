1.) Setup kops cluster on system https://kubernetes.io/docs/setup/production-environment/tools/kops/
2.) aws configure

charanjiths@XXXXXXXX ~ % ls -ahl ~/.aws/
total 16
drwxr-xr-x   4 charanjiths  staff   128B Jan 22 16:47 .
drwxr-xr-x+ 41 charanjiths  staff   1.3K Jan 22 16:50 ..
-rw-------   1 charanjiths  staff    10B Jan 22 16:47 config
-rw-------   1 charanjiths  staff   116B Jan 22 16:47 credentials

charanjiths@XXXXXXXX ~ % aws iam list-users

2.1) Setup kube config (for aws https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html) 

charanjiths@XXXXXXXX ~ % aws sts get-caller-identity

charanjiths@PRINHYLTPAP1459 ~ % kubectl config get-contexts
CURRENT   NAME   CLUSTER   AUTHINFO   NAMESPACE



3.) Setup S3 bucket with admin

4.) Setup DNS Route 53 ==> Domain Name Service
  Setup Hosted zone
  Name would be DOmain Name
  Value ==> Name Server

  The domain name is a component of a uniform resource locator (URL)
   used to access web sites,

   for example: URL: http://www.example.net/index.html.
   Top-level domain: net
   Second-level domain: example

   NS record => Name Server Record
   NS records tell the Internet where to go to find out a domain's IP address.


   When we register a domain + subdomain in Example website (NameCheap)

   For Ex charanjitsingh.com


   It will register and give output as ns record


5.) Trouble Shoot Name server


5.1) 
charanjiths@XXXXXXX ~ % host -t NS imaginea.com
imaginea.com name server ns-615.awsdns-12.net.
imaginea.com name server ns-406.awsdns-50.com.
imaginea.com name server ns-1759.awsdns-27.co.uk.
imaginea.com name server ns-1386.awsdns-45.org.

charanjiths@XXXXXX ~ % host -t NS pramati.com
pramati.com name server ns-1793.awsdns-32.co.uk.
pramati.com name server ns-641.awsdns-16.net.
pramati.com name server ns-1488.awsdns-58.org.
pramati.com name server ns-23.awsdns-02.com.


5.2) 
charanjiths@PRINHYLTPAP1459 ~ % whois pramati.com
% IANA WHOIS server
% for more information on IANA, visit http://www.iana.org
% This query returned 1 object

   Domain Name: PRAMATI.COM
   Registry Domain ID: 602623_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.networksolutions.com
   Registrar URL: http://networksolutions.com
   Updated Date: 2012-05-29T18:04:24Z
   Creation Date: 1997-10-13T04:00:00Z
   Registry Expiry Date: 2021-10-12T04:00:00Z
   Registrar: Network Solutions, LLC
   Registrar IANA ID: 2
   Registrar Abuse Contact Email: abuse@web.com
   Registrar Abuse Contact Phone: +1.8003337680
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Name Server: NS-1488.AWSDNS-58.ORG
   Name Server: NS-1793.AWSDNS-32.CO.UK
   Name Server: NS-23.AWSDNS-02.COM
   Name Server: NS-641.AWSDNS-16.NET
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2021-01-23T20:43:21Z <<<


5.3) NameCheap.com free DNS

CHARANJIT.SINGH.LOVE

Congratulations! Your domain/subdomain has been added to our FreeDNS service.

To start using FreeDNS, you must change nameservers of your domain/subdomain to the following:
freedns1.registrar-servers.com
freedns2.registrar-servers.com
freedns3.registrar-servers.com
freedns4.registrar-servers.com
freedns5.registrar-servers.com  

6.) Cluster setup on AWS using kops 

==> kubectl should be already present
==>  sudo mv kubectl /usr/local/bin
==> sudo chmod +x /usr/local/bin/kubectl
==> Before creating the cluster, create ssh keys
  ==> those ssh keys will be used to login the cluster
   
   Create ssh key
   charanjiths@Xxxxx ~ % ssh-keygen -f .ssh/id_rsa_kops
   
   Copy pub (public key goes to instance)
   charanjiths@PRINHYLTPAP1459 ~ % cat .ssh/id_rsa_kops.pub
   
   
   Check Zonal Info
   
   ```
   aws ec2 describe-availability-zones --region us-east-2
   ```
  
   
   Create Cluster
   
   
   ```
   kops create cluster  --name=charanjit.singh.love --state=s3://kops-state-cj --zones=us-east-2a --ssh-public-key ~/.ssh/id_rsa_kops.pub  --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=charanjit.singh.love
   ```
   
   
   Launch cluster 
   ```
   kops update cluster  charanjit.singh.love --state=s3://kops-state-cj --yes     
   ```

   Delete cluster 
   ```
   kops delete cluster  charanjit.singh.love --state=s3://kops-state-cj --yes     
   ```

   Get info to login to cluster (This will create pwd etc )
   
   ````
   charanjiths@XXXXX ~ % cat ~/.kube/config
  ````

   Get Node
   ```
    kubectl get node
   ```

  Test same deployment should run on  kops managed kubernetes cluster
  
  
  Minikube deployment  (Deployment consist of docker image)
  
  
  charanjiths@XXXXXX ~ % minikube start
  charanjiths@XXXXXX ~ % minikube stop
  
  charanjiths@XXXXX ~ % kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.4  --port 8080
  pod/hello-minikube created
  
  hello-minikube  ==> Name of deployment
  k8s.gcr.io/echoserver:1.4  ==> docker image
  
  
  charanjiths@XXXXXX ~ % kubectl expose deployment hello-minikube  --type=NodePort
  
  charanjiths@XXXXXX ~ % kubectl get services
  
  
  
  
  
  
  
  
  
   
   