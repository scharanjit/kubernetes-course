###Configmap allows us to inject configuration files to inject configuration data within our application

Example ==> Putting nginx reverse proxy in front of nodejs

charanjiths@XXXX configmap % kubectl create configmap nginx-config --from-file=reverseproxy.conf
 configmap/nginx-config created
 
 charanjiths@XXXX configmap % kubectl create -f nginx.yml  
 pod/helloworld-nginx created
 
 charanjiths@XXXX configmap % kubectl create -f nginx-service.yml  
 service/helloworld-nginx-service created
 
 
 =====
 
 charanjiths@XXXX configmap % curl http://127.0.0.1:64790 -vvvv
 *   Trying 127.0.0.1...
 * TCP_NODELAY set
 * Connected to 127.0.0.1 (127.0.0.1) port 64790 (#0)
 > GET / HTTP/1.1
 > Host: 127.0.0.1:64790
 > User-Agent: curl/7.64.1
 > Accept: */*
 > 
 < HTTP/1.1 200 OK
 < Server: nginx/1.11.13
 < Date: Sun, 31 Jan 2021 22:44:25 GMT
 < Content-Type: text/html; charset=utf-8
 < Content-Length: 12
 < Connection: keep-alive
 < X-Powered-By: Express
 < ETag: W/"c-7Qdih1MuhjZehB6Sv8UNjA"
 < 
 * Connection #0 to host 127.0.0.1 left intact
 Hello World!* Closing connection 0



====


charanjiths@XXXX configmap % kubectl exec -i -t helloworld-nginx -c nginx -- bash
root@helloworld-nginx:/# ps x
    PID TTY      STAT   TIME COMMAND
      1 ?        Ss     0:00 nginx: master process nginx -g daemon off;
      9 pts/0    Ss     0:00 bash
     15 pts/0    R+     0:00 ps x
root@helloworld-nginx:/# cat /etc/nginx/conf.d/reverseproxy.conf 
server {
    listen       80;
    server_name  localhost;

    location / {
        proxy_bind 127.0.0.1;
        proxy_pass http://127.0.0.1:3000;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
root@helloworld-nginx:/# 