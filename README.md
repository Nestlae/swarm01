# **SWARM01 / Apache PHP - Swarm Deployment**

**Reference**
- [https://github.com/docker/awesome-compose/tree/master/apache-php](https://github.com/docker/awesome-compose/tree/master/apache-php)

**WakaTime - swarm01**
- [https://wakatime.com/@spcn26/projects/usofysdjzo](https://wakatime.com/@spcn26/projects/usofysdjzo)

**URL for Apache PHP**
- [https://spcn26web.xops.ipv9.xyz/](https://spcn26web.xops.ipv9.xyz/)

## **Steps for preparing deployment**

1. Create [index.php](https://github.com/Nestlae/swarm01/blob/master/app/index.php) for simulating Apache PHP.

2. Create a [Dockerfile](https://github.com/Nestlae/swarm01/blob/master/app/Dockerfile) to build the image.

```ruby
#declare a variable named "builder" as an images
FROM --platform=$BUILDPLATFORM php:8.0.9-apache as builder

#set working directory in container
WORKDIR /var/www/html/

#copy files into "/var/www/html/" folder on images
COPY . /var/www/html/

#set container port to allow the host to access it
EXPOSE 80

#run the last command before docker builds the container
CMD ["apache2-foreground"]

#from image to dev environments
FROM builder as dev-envs

#run file
#update packages
#download lastest version of git and avoid to install recommended packages
#end of file
RUN <<EOF
apt-get update
apt-get install -y --no-install-recommends git
EOF

#run file
#set user login shell & home directory to VSCode
#add docker to the group
#grant VScode for docker authorited
#end of file
RUN <<EOF
useradd -s /bin/bash -m vscode
groupadd docker
usermod -aG docker vscode
EOF

# install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / /

CMD ["apache2-foreground"]

```
3. Create directory called [app](https://github.com/Nestlae/swarm01/tree/master/app) and move 2 files to the [app](https://github.com/Nestlae/swarm01/tree/master/app) directory.

4. Build the image from Dockerfile using this command.
```ruby
docker build . -t <usernameDockerHub>/<repository>:<tag>
```
5. Push the image to DockerHub using this command.
```ruby
docker push <imageID> <usernameDockerHub>/<repository>:<tag>
```

6. Create [docker-compose.yml](https://github.com/Nestlae/swarm01/blob/master/docker-compose.yml) for preparing stack deployment in the root directory.
```ruby
version: '3.3' #version of compose file
services: #create a service
  web: #service that from images on dockerhub named "web"
    image: nestlae/swarm01-web:110323 #image on dockerhub
    networks:
      - webproxy #network for services named "webproxy"
    logging:
      driver: json-file #captures the standard output of all your containers using JSON format
    volumes:
      - app:/var/www/html/ #data path on host : data path on container
    container_name: apache2-php
    deploy: #deployment part
      replicas: 1 #number of containers which can be run
      labels: #label for traefik create rules
        - traefik.docker.network=webproxy #traefik in docker network named webproxy
        - traefik.enable=true #enable traefik service
        - traefik.http.routers.${APPNAME}-https.entrypoints=websecure #webscure listens on port 443 (https)
        - traefik.http.routers.${APPNAME}-https.rule=Host("${APPNAME}.xops.ipv9.xyz") #tells traefik to route requests the domain name
        - traefik.http.routers.${APPNAME}-https.tls.certresolver=default #traefik requests a certificate
        - traefik.http.services.${APPNAME}.loadbalancer.server.port=80 #tells traefik to route requests to a specific port to a container
      resources: #resources setting part
        reservations: #resources reservation for the application
          cpus: '0.1'
          memory: 10M
        limits: #maximum resources for the application
          cpus: '0.4'
          memory: 50M

volumes: #request to create a volumes on docker in this cluster
  app:

networks:
  webproxy: #request to connect to a reverse proxy network that named "webproxy" in this cluster
    external: true #request to connent to an existing service
```
7. Push [docker-compose.yml](https://github.com/Nestlae/swarm01/blob/master/docker-compose.yml) and [app](https://github.com/Nestlae/swarm01/tree/master/app) directory to GitHub ([swarm01](https://github.com/Nestlae/swarm01))

## **Steps for stack deployment**

1. Open the cluster area to deploy the stack. https://portainer.ipv9.me/

<div align="center"><img src="images/1.png" width="800px"></div>

2. Click on "Images" on the left.
- Find the images that you pushed in DockerHub (Number 1) by following the syntax down below.
```ruby
<usernameDockerHub>/<repository>:<tag>
```
- Click "Pull the image" (Number 2) to pull the image into this cluster

<div align="center"><img src="images/2.png" width="800px"></div>

3. Click "Stacks" on the left and click "Add stack" on the right to create a new stack.

<div align="center"><img src="images/3.png" width="800px"></div>

4. After that, you need to pull the file named [docker-compose.yml](https://github.com/Nestlae/swarm01/blob/master/docker-compose.yml) from your repository (GitHub) to use that file to create a new stack in this cluster.<br>
And fill the information by following instruction down below. **Then click "Deploy the stack"**
- Name : Name of stack (Number 1)
- Repository URL : https://github.com/Nestlae/swarm01/ (Number 2)
- Repository reference : refs/heads/master (Number 3)
- Compose path : docker-compose.yml (Number 4)
- Automatic updates : Enable (Number 5)
- Fetch interval : 5m (*optional*, Number 6)
- Environment variables
    - name : APPNAME (Number 7)
    - value : spcn26web (*optional*, Number 8)

<div align="center"><img src="images/4.png" width="800px"></div>

<div align="center"><img src="images/5.png" width="800px"></div>

5. After the stack is deployed. Wait for status to be "running".

<div align="center"><img src="images/6.png" width="800px"></div>

6. After the status says "running", try to go to the website https://spcn26web.xops.ipv9.xyz/.<br> If [docker-compose.yml](https://github.com/Nestlae/swarm01/blob/master/docker-compose.yml) is done correctly, it will display something like this.

<div align="center"><img src="images/7.png" width="600px"></div>