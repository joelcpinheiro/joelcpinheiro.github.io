---
layout: page
permalink: /tips
permalink_name: /tips
title: Always share my content to growing the power of internet!
---

See my tips for Nomad click [here](post-nomad-2021-02-26).
See my POC using Docker Swarm, click [here](post-swarmpoc-2021-02-28).
See my WordPress CMS on docker, click <a href="https://github.com/joelcpinheiro/docker_wpmultisite">here</a>.
See my Jboss commands reference [here](post-jboss-2021-03-11)
See someone tips for Proxmox click [here](post-proxmox-2021-02-24).

# Install Grafana Loki with docker compose:

```sh
wget https://raw.githubusercontent.com/grafana/loki/v2.1.0/production/docker-compose.yaml -O docker-compose.yaml;
docker-compose -f docker-compose.yaml up
```
# Access WebUI http://IPGRAFANALOKI:3000 and insert a loki data source plugin.


# Kubernetes

# I will add content shortly ;)





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

## Commands...

# I will add content shortly ;)


# Linux

# Linux? He change my life <3 <3 <3....not so much hehe

# Customizing BASH Prompt Statement one:

```sh
PS1='\[\e[01;36m\]\u\[\e[01;37m\]@\[\e[01;33m\]\H\[\e[01;37m\]:\[\e[01;32m\]\w\[\e[01;37m\]\$\[\033[0;37m\]'
```

# check IP valid of virtual machine

```sh
curl ifconfig.me
```

# Install Terraform on CentOS/RHEL

Install package:

```sh
yum install -y yum-utils
```

Add repo on yum-config-manager:

```sh
yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
```

Now, install Terraform:

```sh
yum -y install terraform
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

# Recognize disk in guest linux on ESXi(VMware) and don't needed restart:

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
As soon as possible.

```


