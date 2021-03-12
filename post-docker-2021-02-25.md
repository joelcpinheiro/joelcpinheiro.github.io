---
layout: page
permalink: /post-docker-2021-02-25
permalink_name: /post-docker-2021-02-25
title: Docker tips
---


# Docker best commands using for me

# Install Docker by command line

```sh
curl -fsSL https://get.docker.com | sh;
```

# View docker container logs:

```sh
docker logs --follow --tail 20  containername
```

# Clean docker containers all logs of Operational System
 
```sh
truncate -s 0 /var/lib/docker/containers/*/*-json.log
```

# Remove volumes don't used:
```sh
docker volume rm $(docker volume ls -qf dangling=true)
```
# Remove images don't used:

```sh
docker image prune -a -f
```
