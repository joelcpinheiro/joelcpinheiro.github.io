### Always share my content to growing the power of internet!

#### Command lists...

#### Docker

command line one, two and more...

***************************************************************************************************
**********************************  POC Environment  **********************************************

#### POC using Nginx(Reverse Proxy), Docker Swarm, GlusterFS and the tools Portainer, Traefik and Jenkins CI, see the topology below for more information.

Was used in this project six Virtual Machines, one to run containers at homologation, one to serve pages of applications(Nginx) using how reverse proxy, three Managers on Docker Swarm and one server to Storage with GlusterFS  

https://go.gliffy.com/go/publish/8047443

#### Nginx(Reverse Proxy)

See instructions on the link of the project, https://github.com/joelcpinheiro/revproxy-nginx

#### Docker Swarm implementation on CentOS 7 with steps...
##### I use 3 managers in Docker Swarm

#### 1. Prepare SO first and install docker-ce:

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

#### Start Docker Swarm on the three nodes as manager, executing the command below:

```sh

# Initiate swarm cluster on the first server

docker swarm init

# Execute the command an copy the line where display "docker swarm join.....

docker swarm join-token manager

```
Don't forget of allow the communication port 2377 on iptables to cluster work successfully, insert the line on the /etc/sysconfig/iptables:
<br>
```sh
-A INPUT -s 0.0.0.0/0 -m state --state NEW -j ACCEPT
```

Now you should install the portainer, accessing one server manage node and view the step 2.
The Portainer is a GUI manager wich permit view all the resources on your cluster of Docker Swarm.

#### 2. Instructions to run Portainer on Docker Swarm Managers

```sh
# Install based on link https://www.portainer.io/installation/

curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml
docker stack deploy --compose-file=portainer-agent-stack.yml portainer

```

Access the Portainer URL IP_ANY_MANAGER:9000

The Traefik will be manager all the applications on the containers and will serve frontend and backend these containers.

Access one of managers on Docker Swarm and execute the command below.

#### 3. Run the Traefik on Docker Swarm Managers

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

#### 4. Instructions to run Jenkins on Docker host homologation
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

#### 5. Instructions to install GlusterFS

```sh
yum -y install centos-release-gluster6
# Enable repo CentOS Gluster
sed -i -e "s/enabled=1/enabled=0/g" /etc/yum.repos.d/CentOS-Gluster-6.repo
# Install GlusterFS with the repo enabled
yum --enablerepo=centos-gluster6 -y install glusterfs glusterfs-fuse

```
Create the directory `/replica/brick/x/brk`, where x are a node number and execute the command on the first docker node:
<br>

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
<br>
<br>


<br>
<br>
<br>




#### Kubernetes

<br>
<br>
<br>

#### Rancher

<br>
<br>
<br>

##### Commands...

<br>
<br>
<br>

#### Linux

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
### clean all routes:

```sh
ip route flush table main
```

### Update URL git project:
```sh
git remote set-url origin http://git.domain.com/project.git
```
### Store credential user git(github/gitlab/etc...)
```sh
git config credential.helper store
```


#### Disable IPv6 on debian
Insert on the file /etc/sysctl.conf the parameters bellow:

```sh
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```


Mount an network unit of rwindows to GNU/Linux:

```sh
mount.cifs //Host/<Pasta>/ /Local_Destino -o domain=dominio,username=<user>
mount.cifs //192.168.0.20/tmp/ /opt/tmp/ -o domain=meudominio,username=joel.pinheiro
```


<br>
<br>
<br>

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
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>



Unban IP Fail2ban:
```sh
fail2ban-client set sshd unbanip 10.10.100.22
```

Recognize disk in guest linux on VMware and don't needed restart:

```sh
ls /sys/class/scsi_host/ | while read host ; do echo "- - -" > /sys/class/scsi_host/$host/scan ; done
```
<br>
<br>
<br>
<br>
<br>
<br>


#### PowerShell Commands
Reference:

```sh


```

<br>
<br>
<br>
<br>
<br>
<br>




#### Learning English

Search on youtube:<br>
Crazy Koller;<br>
Small Advantages;<br>
Rachel's English;<br>
Elemental English;<br>
English in Brazil by Carina Fragoso;<br>
Ask Jackie;<br>
