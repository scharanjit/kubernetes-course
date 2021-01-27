# Docker demo


###To give access to user for docker


sudo usermod -G docker username

sudo usermod -G docker charanjiths


### Build docker image

charanjiths@XXXXXX docker-demo % docker build .

### Check docker images
charanjiths@XXXXXX ~ % docker images

### Run the container

charanjiths@XXXXXX docker-demo % docker run -p 3000:3000 -t a732ab48538c



charanjiths@XXXXXX docker-demo % docker run -p 3000:3000 -it a732ab48538c

i ==> injected mode  ..Ctrl + C will kill 

### Next step is
Get the image online on docker hub 
so that kubernetes can download this image
and we will run the image on kubernetes cluster


### Docker Registry
#### Push containers to Docker Hub



charanjiths@XXXXXXXX docker-demo % docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: sich6002
Password: 
Login Succeeded

charanjiths@XXXXXXXXX docker-demo % docker tag a732ab48538c sich6002/k8s-demo:latest
                                    docker tag <imageid> <image-name>
charanjiths@XXXXXXX docker-demo % docker push sich6002/k8s-demo
