---
title: Kubernetes tips & commands
slug: kubernetes-commands
date_published: 2021-09-01T18:23:48.000Z
date_updated: 2022-01-20T16:36:13.000Z
tags: #Import 2023-04-07 15:04
---

# K8s Management Tools

Kubectl(Oficial commmand line interface)
Kubeadm(A tool for easily create a K8s cluster)
Minikube(Set up a single node K8s cluster, only for develepment)
Helm(Package management for K8s objects)
Kompose(Translate docker compose do K8s)
Kustomize(Manage K8s objects configurations)
Hashicorp Waypoint(Build, deploy and release apps on K8s cluster)

# Checking which the version of resources to create our manifests on K8s:

    kubectl api-resources
    

## Safety draining a K8s node

    kubectl drain <node name> --ignore-daemonsets --force
    

# Uncordoning a node (get come back one node)

    kubectl uncordon <node name> 
    

# $$$$ Upgrading a K8s with Kubeadm $$$$

1. Upgrade Control Plane

    sudo apt-get update && \
    sudo apt-get install -y --allow-change-held-packages kubeadm=1.20.2-00
    

1. Drain the control plane node

    kubectl drain k8s-control --ignore-daemonsets
    

1. Plan the upgrade

    sudo kubeadm upgrade plan v1.20.2
    

1. Upgrade in fact

    sudo kubeadm upgrade apply v1.20.2
    

1. Upgrade Kubelet and Kubectl

    sudo apt-get update && \
    sudo apt-get install -y --allow-change-held-packages kubelet=1.20.2-00 kubectl=1.20.2-00
    

1. Updade SystemV Daemon and restart kubelet

    sudo systemctl daemon-reload
    sudo systemctl restart kubelet
    

1. Come back control node to cluster

    kubectl uncordon <control node name>
    

1. Follow the same steps on the worker node.

Yet on control plane node you have to drain the worker node that you I'll upgrade, running this command:

    kubectl drain <worker node 01> --ignore-daemonsets --force
    

Now you have to access the <worker node 01> and execute these commands bellow:

    sudo apt-get update && \
    sudo apt-get install -y --allow-change-held-packages kubeadm=1.20.2-00
    
    sudo kubeadm upgrade node
    
    sudo apt-get update && \
    sudo apt-get install -y --allow-change-held-packages kubelet=1.20.2-00 kubectl=1.20.2-00
    
    sudo systemctl daemon-reload
    
    sudo systemctl restart kubelet
    

You need to run this command:

    kubectl uncordon <worker node 01>
    

# Back up and restore etcd cluster data using etcdctl tool

# Lookup the value for the key cluster.name:

    ETCDCTL_API=3 etcdctl get <cluster name> \
      --endpoints=https://IPcluster:2379 \
      --cacert=/home/user/etcd-certs/etcd-ca.pem \
      --cert=/home/user/etcd-certs/etcd-server.crt \
      --key=/home/user/etcd-certs/etcd-server.key
    

# Running command do backup etcd data:

    ETCDCTL_API=3 etcdctl snapshot save /home/user/etcd_backup.db \
      --endpoints=https://IPCluster:2379 \
      --cacert=/home/user/etcd-certs/etcd-ca.pem \
      --cert=/home/user/etcd-certs/etcd-server.crt \
      --key=/home/user/etcd-certs/etcd-server.key
    
    sudo systemctl stop etcd
    
    sudo rm -rf /var/lib/etcd
    

# Restore the etcd Data from the Backup

    
    sudo ETCDCTL_API=3 etcdctl snapshot restore /home/user/etcd_backup.db \
      --initial-cluster etcd-restore=https://IPCluster:2380 \
      --initial-advertise-peer-urls https://IPCluster:2380 \
      --name etcd-restore \
      --data-dir /var/lib/etcd
    

Set ownership on the new data directory:

    sudo chown -R etcd:etcd /var/lib/etcd
    

Start etcd:

    sudo systemctl start etcd
    

Finally, verify the key cluster.name running this command:

    ETCDCTL_API=3 etcdctl get cluster.name \
      --endpoints=https://IPCluster:2379 \
      --cacert=/home/user/etcd-certs/etcd-ca.pem \
      --cert=/home/user/etcd-certs/etcd-server.crt \
      --key=/home/user/etcd-certs/etcd-server.key
    
