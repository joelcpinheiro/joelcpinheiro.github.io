### Always share my content to growing the power of internet!

#### Command lists...

#### Docker

command line one, two and more...

#### Docker swarm implementation on CentOS 7 with steps below...

1. Prepare SO first and install docker:

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

2. Instructions to install Portainer on Docker Swarm nodes

```sh
# Install based on link https://www.portainer.io/installation/

curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml
docker stack deploy --compose-file=portainer-agent-stack.yml portainer

```

Access the Portainer URL IP:9000


3. Install the Traefik on Docker Swarm nodes

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

Access the Traefik URL IP:8080 and see the frontends and backends of services created.



#### K8S




#### Linux




Mount an network unit of rwindows to GNU/Linux:

```sh
mount.cifs //Host/<Pasta>/ /Local_Destino -o domain=dominio,username=<user>
mount.cifs //192.168.0.20/tmp/ /opt/tmp/ -o domain=meudominio,username=joel.pinheiro
```



####  VSFTP best configuration on CentOS 7

Comment anonymous_enable=YES<br>
Uncomment chroot_local_user=YES<br>
Uncomment chroot_list_file=/etc/vsftpd/chroot_list, create this file and add the user to access ftp...<br>
Adjust listen=YES and listen_ipv6=NO<br>

Insert the parameters below:

```sh
#Adjustments :)

anonymous_enable=NO
allow_writeable_chroot=YES
pasv_enable=Yes
xferlog_file=/var/log/vsftpd.log
idle_session_timeout=120
data_connection_timeout=300
accept_timeout=60
connect_timeout=60
anon_max_rate=50000
dual_log_enable=YES
```

Case have a firewalld enabled, execute these commands below:


```sh
firewall-cmd --add-service=ftp --permanent 
firewall-cmd --reload 
```















#### Learning English

Search on youtube:<br>
Crazy Koller;<br>
Small Advantages;<br>
Rachel's English;<br>
Elemental English;<br>
English in Brazil by Carina Fragoso;<br>
Ask Jackie;<br>
