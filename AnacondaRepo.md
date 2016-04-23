# Anaconda Repo Runbook

This following runbook walks through the steps needed to install Anaconda Repo. The runbook is designed for two audiences: those who have direct access to the internet for installation and those where such access is not available or restricted for security reasons. For these restricted a.k.a. "Air Gap" environments, Continuum ships the entire Anaconda product suite on portable storage medium or as a downloadable TAR archive.  Where necessary, additional instructions for Air Gap environments are noted. If you have any questions about the instructions, please contact your sales representative or Priority Support team, if applicable, for additional assistance.

![](https://www.lucidchart.com/publicSegments/view/591eb3b5-b326-49fb-addd-d733e6ceae18/image.png)
## Requirements
### Hardware Requirements

* Physical server or VM
* CPU: 2 x 64-bit 2 2.8GHz 8.00GT/s CPUs or better
* Memory: 32GB RAM (per 50 users)
* Storage: Recommended minimum of 300GB; Additional space is recommended if the repository is will be used to store packages built by the customer.

### Software Requirements

* RHEL/CentOS 6.7 (Other operating systems are supported, however this document assumes RHEL or CentOS 6.7)
* MongoDB version 2.6
* Anaconda Repo license file - given as part of the welcome packet - contact your sales representative or support representative if you cannot find your license.

### Security Requirements

* Privileged (root) access or sudo capabilities
* Ability to make (optional) iptables modifications

**NOTE**: SELinux does not have to be disabled for Anaconda Repo operation
### Network Requirements
#### TCP Ports
* Inbound TCP 8080 (Anaconda Repo)
* Inbound TCP 22 (SSH)
* Outbound TCP 443 (to Anaconda Cloud or local Anaconda Repo)
* Outbound TCP 25 (SMTP)
* Outbound TCP 389/636 (LDAP(s))

### Other Requirements
Assuming the above requirements are met, there are no additional dependencies necessary for Anaconda Repo.

### Air Gap vs. Regular Installation
As stated previously, this document contains installation instructions for two audiences: those with internet access on the destination server(s) and those who have no access to internet resources. Many of the steps below have two sections: **Air Gap Installation** and **Regular Installation**. Those without internet access should follow the **Air Gap Installation** instructions and those with internet access should follow **Regular Installation** instructions.

### Air Gap Media
This document assumes that the Air Gap Repo installer has been downloaded from http://airgap.demo.continuum.io/installers/anaconda-repository-2.16.9-Linux-x86_64.sh
Note: This installer does not ship with any packages. 

This document also assumes that you will be installing your own build of MongoDB locally on the server.

---

## Anaconda Repo Installation
The following sections detail the steps required to install Anaconda Repo.

Note: If you plan to install your own MongoDB build on the server, skip to step #2
### 1.  MongoDB
##### 1.1. Install MongoDB

##### **1.2.** Start mongodb:


    sudo service mongod start


##### **1.3.** Verify mongod is running:

    sudo service mongod status
    mongod (pid 1234) is running...


**NOTE:** Additional mongodb installation information can be found here.

### 2. Create Anaconda Repo administrator account

In a terminal window, create a new user account for Anaconda Repo named “binstar”

    sudo useradd -m binstar

**NOTE:** The binstar user is the default for installing Anaconda Repo. Any username can be used, however the use of the root user is discouraged.

### 3. Create Anaconda Repo directories

    sudo mkdir -m 0770 /etc/binstar
    sudo mkdir -m 0770 /var/log/anaconda-server
    sudo mkdir -m 0770 -p /opt/anaconda-server/package-storage
    sudo mkdir -m 0770 /etc/binstar/mirrors

### 4. Give the binstar user ownership of directories:

    sudo chown -R binstar. /etc/binstar
    sudo chown -R binstar. /var/log/anaconda-server
    sudo chown -R binstar. /opt/anaconda-server/package-storage
    sudo chown -R binstar. /etc/binstar/mirrors

### 5. Switch to the Anaconda Repo administrator account

    sudo su - binstar

### 6. Install Anaconda Enterprise Repository
##### **6.1.** Fetch the download script using curl:

* **Air Gap Installation:** 

	(Assumed) http://airgap.demo.continuum.io/installers/anaconda-repository-2.16.9-Linux-x86_64.sh has been downloaded to a machie with internet access and copied to the server.

* **Regular Installation:**

        curl -O 'http://airgap.demo.continuum.io/installers/anaconda-repository-2.16.9-Linux-x86_64.sh' > Miniconda.sh

##### **6.2.** Run the Miniconda.sh installer script:

* **Air Gap Installation:**
	
        bash anaconda-repository-2.16.9-Linux-x86_64.sh

* **Regular Installation:**

        bash Miniconda.sh

##### **6.3.** Review and accept the license terms:

```
Welcome to anaconda-repository

In order to continue the installation process, please review the license agreement.  
Please, press ENTER to continue. Do you approve the license terms? [yes|no] yes
```
##### **6.4.** Accept the default location or specify an alternative:

```    
anaconda-repository will now be installed into this location:
/home/binstar/miniconda2  
-Press ENTER to confirm the location
-Press CTRL-C to abort the installation
-Or specify a different location below
 [/home/binstar/miniconda2] >>>" [Press ENTER]
 PREFIX=/home/binstar/miniconda2
```

##### **6.5.** Update the binstar user's path:

```
Do you wish the installer to prepend the Miniconda install location to PATH in your /home/binstar/.bashrc ? [yes|no] yes
```

##### **6.6.** For the new path changes to take effect, “source” your .bashrc:
```
source ~/.bashrc
```

### 7.Configure Anaconda Repo
##### **7.1.** Initialize the web server for Anaconda Repo:

    anaconda-server-config --init

##### **7.2.** Set the Anaconda Repo package storage location:

    anaconda-server-config --set fs_storage_root /opt/anaconda-server/package-storage

##### **7.3.** Create an initial “superuser” account for Anaconda Repo:

    anaconda-server-create-user --username "superuser" --password "yourpassword" --email "your@email.com" --superuser

**NOTE:** to ensure the bash shell does not process any of the characters in this password, limit the password to lower case letters, upper case letters and numbers, with no punctuation. After setup the password can be changed with the web interface.

##### **7.4.** Initialize the Anaconda Repo database:

    anaconda-server-db-setup --execute

### 8. Set up automatic restart on reboot, fail or error

##### **8.1.** Configure Supervisord:

    anaconda-server-install-supervisord-config.sh

This step:

* creates the following entry in the binstar user’s crontab:


    `@reboot /home/binstar/miniconda/bin/supervisord`


* generates the `/home/binstar/miniconda/etc/supervisord.conf` file

##### **8.2.** Verify the server is running:

    supervisorctl status

    binstar-server RUNNING   pid 10831, uptime 0:00:05
    binstar-worker RUNNING   pid 2784, uptime 0:00:04

### 9. Install Anaconda Repo License
Visit **http://your.anaconda.server:8080**. Follow the onscreen instructions and upload your license file. Log in with the superuser user and password configured above. After submitting, you should see the login page.

**NOTE:** Contact your sales representative or support representative if you cannot find or have questions about your license.




### Optional: Adjust iptables to accept requests on port 80

The easiest way to enable clients to access an Anaconda Repo on standard ports is to configure the server to redirect traffic received on standard HTTP port 80 to the standard Anaconda Repo HTTP port 8080.


**NOTE:** These commands assume the default state of iptables on CentOS 6.7 which is “on” and allowing inbound SSH access on port 22. Take caution; mistakes with iptables rules can render a remote machine inaccessible.


**Allow inbound access to tcp port 80:**

```
sudo iptables -I INPUT -i eth0 -p tcp --dport 80 -m comment --comment "# Anaconda Repo #" -j ACCEPT
```

**Allow inbound access to tcp port 8080:**

```
sudo iptables -I INPUT -i eth0 -p tcp --dport 8080 -m comment --comment "# Anaconda Repo #" -j ACCEPT
```

**Redirect inbound requests to port 80 to port 8080:**

```
sudo iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 80 -m comment --comment "# Anaconda Repo #" -j REDIRECT --to-port 8080
```

**Display the current iptables rules:**

```
sudo iptables -L -n
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:8080 /* # Anaconda Repo # */
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:80 /* # Anaconda Repo # */
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22
REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         
REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination  
```

**NOTE:** the PREROUTING (nat) iptables chain is not displayed by default; to show it, use:

```
sudo iptables -L -n -t nat
Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination         
REDIRECT   tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:80 /* # Anaconda Repo # */ redir ports 8080

Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination       
```

Write the running iptables configuration to /etc/sysconfig/iptables:

```
sudo service iptables save
```
