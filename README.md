# Local Docker Proxy
Setup http & https access via FQDN for development Docker containers

## TLDR
````shell
docker network create traefik-public
docker-compose up -d
````

## How to run
Before upping the first time we need to create a network 
````shell
docker network create traefik-public
````
Now we can run up
````shell
docker-compose up -d
````
This will give you UIs for the following useful apps
* https://traefik.localhost/dashboard/#/ - The Traefik UI
* https://docker.localhost/#!/home - Portainer Admin UI
* https://mail.localhost/#/ - A mailcatcher UI
* https://whoami.localhost/ - Return info about the browser calling the page

Now run your local docker-compose and add an entry to your /etc/hosts file.

## How to get your docker containers to use this?
1. Remove manual port mappings for any http or https ports
2. Add the network `traefik-public` to the networks section of each container you want traefik to work with
   1. ````yaml
      networks:
            - helix-network
            - traefik-public
3. Add various info via labels under the container
   1. The name part of `traefik.http.routers.<UNIQUE-NAME>-secure.tls` must be unique across all containers
      ````yaml
      labels:
        - "traefik.enable=true"
        #- "traefik.http.routers.helix-nestjs.rule=Host(`nestjs.local.helix.apollo.audio`)" # only needed if it must have http
        #- "traefik.http.routers.helix-nestjs.entrypoints=web" # only needed if it must have http
        - "traefik.http.routers.helix-nestjs-secure.entrypoints=websecure"
        - "traefik.http.routers.helix-nestjs-secure.rule=Host(`nestjs.local.helix.apollo.audio`)"
        - "traefik.http.routers.helix-nestjs-secure.tls=true"
      ````
      
## TODO
* also work for mysql ports
* auto add local domains to local hosts file
