---
layout: page
permalink: /post-swarmpoc-2021-02-28
permalink_name: /post-swarmpoc-2021-02-28
title: Proof of Concept Environment Cluster Docker Swarm
---

# Proof of Concept Environment Cluster Docker Swarm
# POC using Nginx(Reverse Proxy), Docker Swarm, GlusterFS and the tools Portainer, Traefik and Jenkins CI, see the topology below for more information.

Was used in this project six Virtual Machines, one to run containers at homologation, one to serve pages of applications(Nginx) using how reverse proxy, three Managers on Docker Swarm and one server to Storage with GlusterFS.

See the Environment<a href="https://go.gliffy.com/go/publish/8047443">Topology</a>

# Nginx(Reverse Proxy)

See instructions on the link of the project, on <a href="https://github.com/joelcpinheiro/revproxy-nginx">this link.</a>

# Docker Swarm implementation on CentOS 7 with steps...
### Ps. Soon a ansibe playbook will come out of the oven :)

# I use 3 managers in Docker Swarm

# 1. Prepare SO first and install docker-ce:

```sh
setenforce 0
yum install vim -y
# remove parameter on Selinux of enforcing to disabled

vim /etc/selinux/config

# Disable and stop firewalld, install iptables-services

systemctl stop firewalld
systemctl disable firewalld
systemctl install iptables-services
yum install iptables-services
systemctl start iptables

# Prepare docker packages and install on the servers

yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum update
yum install docker-ce
systemctl enable docker
systemctl start docker

```

# Start Docker Swarm on the three nodes as manager, executing the command below:

```sh

# Initiate swarm cluster on the first server

docker swarm init

# Execute the command an copy the line where display "docker swarm join.....

docker swarm join-token manager

```
# PLEASE ATTENTION #
Don't forget of allow the communication port 2377 on iptables to cluster work successfully, insert the line on the /etc/sysconfig/iptables:

```sh
-A INPUT -s 192.168.10.0/24 -m state --state NEW -j ACCEPT
```

Now you should install the portainer, accessing one server manage node and view the step 2.
The Portainer is a GUI manager wich permit view all the resources on your cluster of Docker Swarm.

# 2. Instructions to run Portainer on Docker Swarm Managers

```sh
# Install based on link https://www.portainer.io/installation/

curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml
docker stack deploy --compose-file=portainer-agent-stack.yml portainer

```

Access the Portainer URL IP_ANY_MANAGER:9000

The Traefik will be manager all the applications on the containers and will serve frontend and backend these containers.

Access one of managers on Docker Swarm and execute the command below.

# 3. Run the Traefik on Docker Swarm Managers

```sh
docker network create --driver=overlay traefik-pub

docker service create \
    --name traefik \
    --constraint=node.role==manager \
    --publish 80:80 \
    --publish 8080:8080 \
    --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
    --network traefik-pub \
    traefik:v1.6 \
    --docker \
    --docker.swarmmode \
    --docker.domain=traefik \
    --docker.watch \
    --web

```

Access the Traefik URL IP_ANY_MANAGER:8080 and see the frontends and backends of services created.

To continue create a service call <b>ussuapp</b>, don't remenber of add the labels which are traefik.port, network traefik-pub and label traefik.frontend.rule, that is an URL on the application.

```sh
docker service create \
    --name ussuapp \
    --label traefik.port=80 \
    --network traefik-pub \
    --label traefik.frontend.rule=Host:ussu.com\
    nginx:latest
```

Now the application call ussuapp can be accessed by URL ussu.com.

# 4. Instructions to run Jenkins on Docker host homologation
```sh
docker run \
  -u root \
  --rm \
  -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkinsci/blueocean

```
Reference Link: https://jenkins.io/doc/book/installing/

Now access the link IP:8080 and configure the Jenkins.

# 5. Instructions to install GlusterFS

```sh
yum -y install centos-release-gluster6
# Enable repo CentOS Gluster
sed -i -e "s/enabled=1/enabled=0/g" /etc/yum.repos.d/CentOS-Gluster-6.repo
# Install GlusterFS with the repo enabled
yum --enablerepo=centos-gluster6 -y install glusterfs glusterfs-fuse

```
Create the directory `/replica/brick/x/brk`, where x are a node number and execute the command on the first docker node:


```sh
gluster volume create gfs \
replica 3 \
moc-docker-01:/replica/brick/1/brk \
moc-docker-02:/replica/brick/2/brk \
moc-docker-03:/replica/brick/3/brk
```

Execute the echo on the each node correspond, in this case on the three managers:

```sh
echo '/dev/sdb1 /replica/brick/1 xfs defaults 0 0' >> /etc/fstab
echo '/dev/sdb1 /replica/brick/2 xfs defaults 0 0' >> /etc/fstab
echo '/dev/sdb1 /replica/brick/3 xfs defaults 0 0' >> /etc/fstab
```
Start the glusterfs call gfs and permit only the nodes docker

```sh
gluster volume set gfs auth.allow IPnode01,IPnode02,IPnode03
gluster volume start gfs

```


