---
title: Little things about Hashicorp Nomad
slug: little-things-about-hashicorp-nomad
date_published: 2021-11-09T18:08:20.000Z
date_updated: 2021-11-09T18:08:38.000Z
tags: #Import 2023-04-07 15:04
---

### My personal challenge is creating an Environment and share the content on GitHub.

### Some commands learned on until at moment...

### Add on your .bash_profile the environment variables for nomad, that are:

    NOMAD_ADDR=https://127.0.0.1:4646
    NOMAD_CACERT=~/nomad-certs/nomad-ca.pem
    NOMAD_CLIENT_CERT=~/nomad-certs/cli.pem
    NOMAD_CLIENT_KEY=~/nomad-certs/cli-key.pem
    NOMAD_TOKEN="secretid" # in case of use ACL

### To create a server member, add the param datacenter = namedc and remove or comment block client, like an example bellow:

    # Full configuration options can be found at https://www.nomadproject.io/docs/configuration
    
    data_dir = "/opt/nomad/data"
    
    datacenter = "namedc"
    
    server {
      enabled = true
      bootstrap_expect = 1
    }

### To create a client member, use this config bellow:

    data_dir = "/opt/nomad/data"
    
    datacenter = "namedc"
    
    client {
      enabled = true 
      servers = ["IP_NAMEDC"]
    }

### Check node member of cluster

    nomad server members

### Check status of nodes

    nomad node status

### Process to upgrade jobs on nomad(Change > Plan > Run)

Blue and Green Canary Deployments

### Under update, insert the code below:

    update {
        max_parallel = 1
        min_healthy_time = "10s"
        healthy_deadline = "3m"
        progress_deadline = "10m"
        auto_revert = false
        canary = 2
    }
    

### Modify a job with a version of your image, for example...

### And run a example job again:

    nomad job run example.nomad

### Finally, promote a job using the command:

    nomad deployment promote IDOFJOB

### Create a new JOB called example

    nomad job init -short example.nomad

### See logs of one JOB

    nomad alloc logs IDJOB
