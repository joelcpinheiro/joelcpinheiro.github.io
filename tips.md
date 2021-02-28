---
layout: page
permalink: /tips
permalink_name: /tips
title: Tips for life
---


See my tips for Nomad click [here](post-nomad-2021-02-26)
See my POC using Docker Swarm, click [here](post-swarmpoc-2021-02-28)
See tips for Proxmox click [here](post-proxmox-2021-02-24) <a href="https://joelpinheiro.com/post-proxmox-2021-02-24">here</a>

### Always share my content to growing the power of internet!

# Docker

command line one, two and more....

# Install Docker by command line

```sh
curl -fsSL https://get.docker.com | sh;
```

# Clean docker containers logs of Operational System
 
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


***************************************************************************************************
***************************************************************************************************
Proof of Concept Environment Cluster Docker Swarm
***************************************************************************************************
***************************************************************************************************


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






# Zabbix Server

Check if UserParameter exists on zabbix-agent:

```sh
zabbix_get -s <servername | ip> -p port -k "key.status"
```

External ping to another server on Windows Server using userparameter:

```sh
UserParameter=checkping[*],ping -n 1 -w 1 "$1" | find  /i "TTL=" /c
```

Delete old data DB Zabbix:

```sh
SET @history_interval = 7;
SET @trends_interval = 90;

DELETE FROM alerts WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@history_interval * 24 * 60 * 60);
DELETE FROM acknowledges WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@history_interval * 24 * 60 * 60);
DELETE FROM events WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@history_interval * 24 * 60 * 60);

DELETE FROM history WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@history_interval * 24 * 60 * 60);
DELETE FROM history_uint WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@history_interval * 24 * 60 * 60);
DELETE FROM history_str WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@history_interval * 24 * 60 * 60);
DELETE FROM history_text WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@history_interval * 24 * 60 * 60);
DELETE FROM history_log WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@history_interval * 24 * 60 * 60);

DELETE FROM trends WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@trends_interval * 24 * 60 * 60);
DELETE FROM trends_uint WHERE (UNIX_TIMESTAMP(NOW()) - clock) > (@trends_interval * 24 * 60 * 60);
```

# Kubernetes

# I will add content shortly ;)




# Rancher

# I will add content shortly ;)




## Commands...

# I will add content shortly ;)




# Linux

# Linux? He change my life <3 <3 <3....not so much hehe







# check IP valid of virtual machine

```sh
curl ifconfig.me
```


# Firewalld

# Using a VM Linux Router to forward ports to another VMs, is so simple, run commands below:

# I supposed that you set the forward packages to 1 on file `/proc/sys/net/ipv4/ip_forward`, that's okay? 

```sh
# Tell eth0 to be our WAN
nmcli con mod eth0 connection.zone external
# Tell eth1 to be our LAN (or a bridge if you have one)
nmcli con mod eth1 connection.zone internal
# Doesn't hurt to re-up
nmcli con up eth0 ; nmcli con up eth1
```

```sh
# The external zone already has masquerade on, but just in case
firewall-cmd --zone=external --add-masquerade --permanent
firewall-cmd --complete-reload

firewall-cmd --get-active-zones

external
  interfaces: eth0
internal
  interfaces: eth1
```

```sh
# Now, run this command to one or more IPs that needed

firewall-cmd --zone=external --add-forward-port=port=8989:proto=tcp:toport=8989:toaddr=10.10.2.3 --permanent

# Don't forget reload FireWalld

firewall-cmd --reload

```


# Do you change anything on interface on Ubuntu?
```sh
ip flush addr interface_name(ens192)
systemctl restart networking
```

# Reset password RHEL 7/ CentOS 7

# 1. Reboot SO;
# 2. On boot loader GRUB2 pressing any key and type "e";
# 3. Go to kernel command line and append at the final "rd.break" and press CTRL + X to boot;
# 4. Execute these commands below(Remount sysroot and switch to chroot):
```sh
mount -o remount,rw /sysroot;
chroot /sysroot;
```
# 5. Set a new password typing passwd;
# 6. Relabeled all files on next boot including /etc/shadow, just here :0;
```sh
touch /.autorelabel
```
# 7. Just enough, type exit twice times and will see boot on SO.



# Check url many times using for command:
```sh
for i in `seq 1 20`; do curl http://site; done
```

# Check logins of a specific user on CentOS 7 distribution:

```sh
utmpdump /var/log/wtmp | grep -E "\[7].*joel" | awk -v OFS=" ## " 'BEGIN {FS="] "}; {print $2,$4,$7,$8}' | sed -e 's/\[//g' -e 's/\]//g'
```

# clean all routes:

```sh
ip route flush table main
```

# Update URL git project:
```sh
git remote set-url origin http://git.domain.com/project.git
```
# Store credential user git(github/gitlab/etc...)
```sh
git config credential.helper store
```
# Good jenkins plugins:

Blue Ocean
Chucknorris Plugin
Gitlab
Green Bals
SSH Plugin

# Disable IPv6 on debian
Insert on the file /etc/sysctl.conf the parameters bellow:

```sh
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```


# Mount an network unit of rwindows to GNU/Linux:

```sh
mount.cifs //Host/<Pasta>/ /Local_Destino -o domain=dominio,username=<user>
mount.cifs //192.168.0.20/tmp/ /opt/tmp/ -o domain=meudominio,username=joel.pinheiro
```






#  VSFTP best configuration on CentOS 7

Comment anonymous_enable=YES
Uncomment chroot_local_user=YES
Uncomment chroot_list_file=/etc/vsftpd/chroot_list, create this file and add the user to access ftp...
Adjust listen=YES and listen_ipv6=NO

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






# To add IP/Network on the Fail2ban whitelist, edit the parameter "ignoreip" in the file /etc/fail2ban/jail.conf:

```sh
ignoreip = 127.0.0.1 YOURIP YOURNETWORK
```

After restart the fail2ban service


# Unban IP Fail2ban:
```sh
fail2ban-client set sshd unbanip 10.10.100.22
```

# Recognize disk in guest linux on VMware and don't needed restart:

```sh
ls /sys/class/scsi_host/ | while read host ; do echo "- - -" > /sys/class/scsi_host/$host/scan ; done
```



# Install LetsEncrypt on Zimbra

Reference: https://respirandolinux.com.br/2017/05/13/zimbra-8-7-implementando-certificado-assinado-com-lets-encrypt/ 

Execute the commands bellow case occurred an error of ldap with TLS 
```sh
zmlocalconfig -e ssl_allow_untrusted_certs=true 
zmlocalconfig -e ldap_starttls_supported=0
zmlocalconfig -e ldap_starttls_required=false
zmlocalconfig -e ldap_common_require_tls=0
```

# PowerShell Commands
Reference:

```sh
Soon as possible

```







