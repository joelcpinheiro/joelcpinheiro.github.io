---
layout: page
permalink: /post-jboss-2021-03-11
title: Jboss standalone tips
---

# Jboss best commands using for me at the ACG Course
# CHALLENGE: Create a Environment using Terraform + Ansible and JBoss docker image too.
# Installing OpenJDK:
```sh
sudo yum -y install java-11-openjdk-devel
```
# Install JBoss EAP

# Download and run the installer located in the user's home directory:
```sh
  sudo java -jar jboss-eap-7.2.0-installer.jar -console
```
# In the installer, set the following values:
 Select your language below: Enter 0 for English.
 End user license agreement: Enter 1 to continue.
 Select the installation path: Use /opt/jboss-eap to match default configuration and enter 1 to continue.
 Select the packs you want to install: Enter 0 to confirm and 1 to continue.
 Admin username: Enter admin.
 Admin password: Enter your password twice and 1 to continue.
 Configure Runtime Environment: Enter 0 to perform the default configuration and 1 to continue.
 Would you like to generate an automatic installation script and properties file?: Enter n.

# Configure JBoss EAP as a Service

# Create the service user:
```sh
sudo useradd --no-create-home --shell /bin/shell/ jboss-eap
```
# Open the configuration file:
```sh 
cd /opt/jboss-eap;
sudo vim bin/init.d/jboss-eap.conf
```
# Uncomment the JBOSS_HOME and JBOSS_USER settings; save and exit.

# Copy service files to the appropriate locations:
```sh
sudo cp bin/init.d/jboss-eap.conf /etc/default/
sudo cp bin/init.d/jboss-eap-rhel.sh /etc/init.d/
```
# Set script as an executable:
```sh
sudo chmod +x /etc/init.d/jboss-eap-rhel.sh
```
# Set the service to start automatically:
```sh
sudo chkconfig --add jboss-eap-rhel.sh
sudo chkconfig jboss-eap-rhel.sh on
```
# Create the /var/run/jboss-eap directory and set the jboss-eap user as the owner:
```sh
sudo mkdir /var/run/jboss-eap
sudo chown -R jboss-eap:jboss-eap /var/run/jboss-eap/
```
# Change the ownership of /opt/jboss-eap as well:
```sh
sudo chown -R jboss-eap:jboss-eap /opt/jboss-eap/
```

# Enable Management Console Web Interface

```sh
vim /opt/jboss-eap/standalone/configuration/standalone.xml
```

# Where you see management block, change the localhost IP(127.0.0.1) to 0.0.0.0(all interfaces), line 493.

```sh
<interfaces>
    <interface name="management">
        <inet-address value="${jboss.bind.address.management:0.0.0.0}"/>
    </interface>
    <interface name="public">
        <inet-address value="${jboss.bind.address:0.0.0.0}"/>
    </interface>
</interfaces>
```

# And finally start service with this command:

```sh
systemctl start jboss-eap-rhel
```

Now you can access the Management Console http://IP:9990 

# Update log level if you want adjusting the parameter ```logger.level```(line 8):


```sh
vim /opt/jboss-eap/standalone/configuration/logging.properties
```

# Setting users
.....
# Deploy a new application
.....
