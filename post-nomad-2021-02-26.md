---
layout: page
permalink: /post-nomad-2021-02-26
title: Nomad tips
---


# Little thing about nomad used on ACG Course.
# My personal challenge is create an environment and share the content on GitHub.
# Some commands learned on until at moment...

# Add on your .bash_profile the environment variables for nomad, that are:

```sh
NOMAD_ADDR=https://127.0.0.1:4646
NOMAD_CACERT=~/nomad-certs/nomad-ca.pem
NOMAD_CLIENT_CERT=~/nomad-certs/cli.pem
NOMAD_CLIENT_KEY=~/nomad-certs/cli-key.pem
NOMAD_TOKEN="secretid" # in case of use ACL
```

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

# Process to upgrade jobs on nomad

Change > Plan > Run

# Blue and Green Canary Deployments

# Under update, insert the code below:

```sh
update {
    max_parallel = 1
    min_healthy_time = "10s"
    healthy_deadline = "3m"
    progress_deadline = "10m"
    auto_revert = false
    canary = 2
}
```

# Modify a job with a version of your image, for example...

# And run a example job again:
```sh
nomad job run example.nomad
```

# Finally, promote a job using the command:
```sh
nomad deployment promote IDOFJOB
```


# Create a new JOB called example

```sh
nomad job init -short example.nomad
```

# See logs of one JOB

```sh
nomad alloc logs IDJOB
```
