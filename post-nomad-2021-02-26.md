---
layout: page
permalink: /post-nomad-2021-02-26
permalink_name: /post-nomad-2021-02-26
title: Proxmox tips
---


# Little thing about nomad used on ACG Course

# Some commands learned on until at moment...

# To create a server member, add the param ```datacenter = namedc``` and remove or comment block client, like an example bellow:

```sh
# Full configuration options can be found at https://www.nomadproject.io/docs/configuration

data_dir = "/opt/nomad/data"

datacenter = "namedc"

server {
  enabled = true
  bootstrap_expect = 1
}

```

# To create a client member, use this config bellow:

```sh
data_dir = "/opt/nomad/data"

datacenter = "namedc"

client {
  enabled = true 
  servers = ["IP_NAMEDC"]
}

```


# Check node member of cluster
```sh
nomad server members
```

# Check status of nodes

```sh
nomad node status
```