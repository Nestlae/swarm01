## Ref
- [https://github.com/docker/awesome-compose/tree/master/apache-php](https://github.com/docker/awesome-compose/tree/master/apache-php)

## WakaTime - swarm01
- [https://wakatime.com/@spcn26/projects/usofysdjzo](https://wakatime.com/@spcn26/projects/usofysdjzo)

## [**Make VM template**](https://github.com/pitimon/docker-spcn-02) and spread for 3 parts
- *manager (master, monitoring)*
- *work1 (node)*
- *work2 (node)*

Then do the **swarm initialization** at the *manager VM* by follow this construction below.
- [https://github.com/pitimon/dockerswarm-inhoure](https://github.com/pitimon/dockerswarm-inhoure)

After the swarm initialization were done, get the command that you got from
```
docker swarm init
```
It's seem to be a token, after that you need to log in a different VM for make nodes and paste the token here to connect the nodes to manager VM

Then you need to deploy "Portainer" for monitoring nodes by follow this command.
```
curl -L https://downloads.portainer.io/ce2-17/portainer-agent-stack.yml -o portainer-agent-stack.yml //Download .yml file
docker stack deploy -c portainer-agent-stack.yml portainer //Stack deploying
```
## Revert Proxy
- Edit file hosts by follow paths
    - Windows > C:\Windows\System32\drivers\etc\hosts
    - Linux > /etc/hosts
- By follow the template
    ```
    172.xxx.xxx.xxx traefik.demo.local
    ```

- Create new network in docker
     ```
     docker network create --driver=overlay traefik-public
     ```

- Get node ID
     ```
     export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}') 
     echo $NODE_ID
     ```

- Create label for manager node
     ```
     docker node update --label-add traefik-public.traefik-public-certificates=true $NODE_ID
     ```

- Set Treafik
     ```
     export EMAIL=user@smtp.com
     export DOMAIN=<traefik domain>
     export USERNAME=admin
     export PASSWORD=<password>
     export HASHED_PASSWORD=$(openssl passwd -apr1 $PASSWORD)
     echo $HASHED_PASSWORD
     ```

- Deploy traefik stack
     ```
     docker stack deploy -c traefik-host.yml traefik
     ```
## Ref
   - [https://github.com/pitimon/dockerswarm-inhoure/tree/main/ep03-traefik](https://github.com/pitimon/dockerswarm-inhoure/tree/main/ep03-traefik)