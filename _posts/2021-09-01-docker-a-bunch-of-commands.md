---
title: Docker a bunch of commands
slug: docker-a-bunch-of-commands
date_published: 2021-09-01T18:21:13.000Z
date_updated: 2023-01-18T16:56:55.000Z
tags: #Import 2023-04-07 15:04
---

### Docker best commands used for me

Install latest Docker version via script .sh:

    curl -fsSL https://get.docker.com | sh;
    

### View docker container logs:

    docker logs --follow --tail 20  containername

### View docker container logs since one specifc period:

    docker logs --since=1h  containername

### Cleaning logs of all docker containers:

    truncate -s 0 /var/lib/docker/containers/*/*-json.log
    

### Remove volumes don't used:

    docker volume rm $(docker volume ls -qf dangling=true)
    

### Remove images don't used:

    docker image prune -a -f
    

### Run this for command to verify the health of one app 5 times:

    for i in {1..5}; do curl -H Host:myappurl.com http://ipofcontainer; done
