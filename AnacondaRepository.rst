.. This sets up section numbering
.. sectnum::

=====================
Anaconda Repo Runbook
=====================

* Version: |release| | |today|

.. contents::
   :local:
   :depth: 1

This following runbook walks through the steps needed to install
Anaconda Repo. The runbook is designed for two audiences: those who have
direct access to the internet for installation and those where such
access is not available or restricted for security reasons. For these
restricted a.k.a. "Air Gap" environments, Continuum ships the entire
Anaconda product suite on portable storage medium or as a downloadable
TAR archive. Additionally, Continuum provides a set of Air Gap TAR archives for
those environments only needing certain platform architectures,
such as 64-Bit Linux, 32-Bit Linux, etc. 
With the exception of 64-Bit Linux, these platform-based archives include
all of the available packages for that platform.
The 64-Bit Linux archive contains 64-Bit Linux packages PLUS packages
neecessary to install Anaconda Repo.

Additional platforms can be added by downloading the corresponding
TAR archive and importing it to the local Anaconda Repo. See the section titled "Optional: Installing from Platform-based Archives" below to prepare your environment before starting the Anaconda Repo Installation. 

Where necessary, additional instructions for Air Gap
environments are noted throughout this document. If you have any questions about the
instructions, please contact your sales representative or Priority
Support team, if applicable, for additional assistance.

.. image:: _static/repo.png


Requirements
------------

Hardware Requirements
~~~~~~~~~~~~~~~~~~~~~

-  Physical server or VM
-  CPU: 2 x 64-bit 2 2.8GHz 8.00GT/s CPUs or better
-  Memory: 32GB RAM (per 50 users)
-  Storage: Recommended minimum of 300GB; Additional space is
   recommended if the repository is will be used to store packages built
   by the customer.

Software Requirements
~~~~~~~~~~~~~~~~~~~~~

-  RHEL/CentOS 6.7 (Other operating systems are supported, however this
   document assumes RHEL or CentOS 6.7)
-  MongoDB version 2.6
-  Anaconda Repo license file - given as part of the welcome packet -
   contact your sales representative or support representative if you
   cannot find your license.
-  cron: The anaconda-server user needs to add an entry to cron to start the server on reboot

Linux System Accounts Required
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some Linux system accounts (UIDs) are added to the system during installation.
If your organization requires special actions, here is the list of UIDs:

- anaconda-server: Created manually during installation
- mongod (RHEL) or mongodb (Ubuntu/Debian): Created by the RPM or deb package if doing a system wide mongo install

Security Requirements
~~~~~~~~~~~~~~~~~~~~~

-  Privileged (root) access or sudo capabilities
-  Ability to make (optional) iptables modifications

.. note:: SELinux does not have to be disabled for Anaconda Repo operation

Network Requirements
~~~~~~~~~~~~~~~~~~~~

* TCP Ports

  - Inbound TCP 8080 (Anaconda Repo)
  - Inbound TCP 22 (SSH)
  - Outbound TCP 443 (to Anaconda Cloud or local Anaconda Repo)
  - Outbound TCP 25 (SMTP)
  - Outbound TCP 389/636 (LDAP(s))

Other Requirements
~~~~~~~~~~~~~~~~~~

Assuming the above requirements are met, there are no additional
dependencies necessary for Anaconda Repo.

Air Gap vs. Regular Installation
----------------------------------

As stated previously, this document contains installation instructions
for two audiences: those with internet access on the destination
server(s) and those who have no access to internet resources. Many of
the steps below have two sections: **Air Gap Installation** and
**Regular Installation**. Those without internet access should follow
the **Air Gap Installation** instructions and those with internet access
should follow **Regular Installation** instructions.

Air Gap
~~~~~~~~

This document assumes that the Air Gap media is available on
the target server at $INSTALLER_PATH where the software is being installed. 

There are two ways to obtain the "Air Gap" data: 

1. A pen drive is over-nighted to client

2. Client downloads the latest archive tarballs and expands the archive to
   `/installer`. For example, `anaconda-full-2016-07-11.tar` expands to `scratch/anaconda-full-2016-07-11/`

.. note:: The $INSTALLER_PATH variable must be set to the location of the Air Gap media as displayed below. The $INSTALLER_PATH is the parent directory to the **anaconda-suite** directory. See examples below:

1. For Air-Gap pen drive media mounted on `/installer`:

.. code-block:: bash

    INSTALLER_PATH=/installer

2. For Air Gap tarball expanded from `anaconda-full-2016-07-11.tar`:

.. code-block:: bash

    tar xvf anaconda-full-2016-08-06.tar -C /installer/ --strip-components 2
    INSTALLER_PATH=/installer


3. For Air Gap tarball expanded without stripping directories `anaconda-full-2016-07-11.tar`:

.. code-block:: bash

    tar xvf anaconda-full-2016-08-06.tar -C /installer/
    INSTALLER_PATH=/installer/scratch/anaconda-full-2016-07-11



Air Gap media contents
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

  $INSTALLER_PATH
  anaconda-cluster/
  anaconda-suite/
  mongodb-org-tools-2.6.8-1.x86_64.rpm
  mongodb-org-shell-2.6.8-1.x86_64.rpm
  mongodb-org-server-2.6.8-1.x86_64.rpm
  mongodb-org-mongos-2.6.8-1.x86_64.rpm
  mongodb-org-2.6.8-1.x86_64.rpm
  R/
  wakari/

Configure Anaconda Repo Server
-----------------------------------------
Prior to installing Anaconda Repo components, the following needs to be done by either the IT admin or
need `sudo` access to do it ourselves.


Create Anaconda Repo administrator account
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In a terminal window, create a new user account for Anaconda Repo named "anaconda-server".

::

    sudo useradd -m anaconda-server

.. note:: The anaconda-server user is the default for installing Anaconda Repo.  Any username can be used, however the use of the root user is discouraged.

Create Anaconda Repo directories
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    sudo mkdir -m 0770 /etc/anaconda-server
    sudo mkdir -m 0770 /var/log/anaconda-server
    sudo mkdir -m 0770 -p /opt/anaconda-server/package-storage
    sudo mkdir -m 0770 /etc/anaconda-server/mirrors

Give the anaconda-server user ownership of directories
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    sudo chown -R anaconda-server. /etc/anaconda-server
    sudo chown -R anaconda-server. /var/log/anaconda-server
    sudo chown -R anaconda-server. /opt/anaconda-server/package-storage
    sudo chown -R anaconda-server. /etc/anaconda-server/mirrors

Switch to the Anaconda Repo administrator account
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    sudo su - anaconda-server


Install Miniconda bootstrap version
-----------------------------------

Fetch the download script using curl
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **Air Gap Installation:** Skip this step.

-  **Regular Installation:**

::

    curl 'http://repo.continuum.io/miniconda/Miniconda2-latest-Linux-x86_64.sh' > Miniconda.sh

Run the Miniconda.sh installer script
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-  **Air Gap Installation:**

::

  bash $INSTALLER_PATH/anaconda-suite/miniconda/Miniconda2-latest-Linux-x86_64.sh

-  **Regular Installation:**

::

   bash Miniconda.sh

Review and accept the license terms
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    Welcome to Miniconda (by Continuum Analytics, Inc.)
    In order to continue the installation process, please review the license agreement.
    Please, press ENTER to continue. Do you approve the license terms? [yes|no] yes

Accept the default location or specify an alternative:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    Miniconda will now be installed into this location:
    /home/anaconda-server/miniconda2
    -Press ENTER to confirm the location
    -Press CTRL-C to abort the installation
    -Or specify a different location below
     [/home/anaconda-server/miniconda2] >>>" [Press ENTER]
     PREFIX=/home/anaconda-server/miniconda2

Update the anaconda-server user's path
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Do you wish the installer to prepend the Miniconda install location to
PATH in your /home/anaconda-server/.bashrc ?

::

    [yes|no] yes

For the new path changes to take effect, “source” your .bashrc
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    source ~/.bashrc

Install Anaconda Repo Enterprise Packages
-----------------------------------------

Install `mongodb`
------------------

* System wide install of `mongodb`, which **requires `sudo` access**, see Section: 
  :ref:`system-mongo-install-sudo`. 
* For a local install of `mongodb`, see Section: :ref:`local-mongo-install-no-sudo`.
  Does not require `sudo` access but requires a few more manual steps.


.. _system-mongo-install-sudo:

System Wide mongodb Installation - Requires `sudo`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Download MongoDB packages
^^^^^^^^^^^^^^^^^^^^^^^^^^

-  **Air Gap Installation:** Skip this step.

-  **Regular Installation:**

::

   RPM_CDN="https://820451f3d8380952ce65-4cc6343b423784e82fd202bb87cf87cf.ssl.cf1.rackcdn.com"
   curl -O $RPM_CDN/mongodb-org-tools-2.6.8-1.x86_64.rpm
   curl -O $RPM_CDN/mongodb-org-shell-2.6.8-1.x86_64.rpm
   curl -O $RPM_CDN/mongodb-org-server-2.6.8-1.x86_64.rpm
   curl -O $RPM_CDN/mongodb-org-mongos-2.6.8-1.x86_64.rpm
   curl -O $RPM_CDN/mongodb-org-2.6.8-1.x86_64.rpm

Install MongoDB packages
^^^^^^^^^^^^^^^^^^^^^^^^^

- **Air Gap Installation:**

::

    sudo yum install -y $INST/mongodb-org*

-  **Regular Installation:**

::

    sudo yum install -y mongodb-org*


Start mongodb
^^^^^^^^^^^^^^^

::

    sudo service mongod start

Verify mongod is running
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    sudo service mongod status
    mongod (pid 1234) is running...

.. note:: Additional mongodb installation information can be found `here <https://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat/>`__.

.. _local-mongo-install-no-sudo:

Local mongodb Installation - `sudo` Not Required
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- **Air Gap Installation:**

::

    currently not part of airgap archive so this is not yet supported


- **Regular Installation:** 

::

    conda install mongodb=2.6.12


This will install mongodb in root conda environment of user: `anaconda-server`

::

    which mongod
    ~/miniconda2/bin/mongod


.. _install-ae-packages:

Install Anaconda Repo Enterprise Packages
------------------------------------------

The following sections detail the steps required to install Anaconda Repo.


Add the Binstar and Anaconda-Server Repo channels to conda:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **Air Gap Installation:** Add the channels from local files.

::

       conda config --add channels  file://$INSTALLER_PATH/anaconda-suite/pkgs/
       conda config --remove channels defaults --force

-  **Regular Installation:** Add the channels from Anaconda Cloud.

::

       export BINSTAR_TOKEN=<your binstar token>
       export ANACONDA_TOKEN=<your anaconda-server token>
       conda config --add channels https://conda.anaconda.org/t/$BINSTAR_TOKEN/binstar/
       conda config --add channels https://conda.anaconda.org/t/$ANACONDA_TOKEN/anaconda-server/


.. note:: You should have received **two** tokens from Continuum Support, one for each channel. If you haven't, please contact support@continuum.io. Tokens are not required for Air Gap installs.

Install the Anaconda Repo packages via conda:
---------------------------------------------

::

    conda install anaconda-client binstar-server binstar-static cas-mirror


Setup Anaconda Repo Server Config Files
-----------------------------------------

Initialize the web server for Anaconda Repo:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    anaconda-server-config --init --config-file /etc/anaconda-server/config.yaml

Set the Anaconda Repo package storage location:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    anaconda-server-config --set fs_storage_root /opt/anaconda-server/package-storage --config-file /etc/anaconda-server/config.yaml


Set up automatic restart on reboot, fail or error
-------------------------------------------------

.. _conf-supervisord:


Configure Supervisord
~~~~~~~~~~~~~~~~~~~~~

::

    anaconda-server-install-supervisord-config.sh

This step:

-  creates the following entry in the anaconda-server user’s crontab:

   ``@reboot /home/anaconda-server/miniconda/bin/supervisord``

-  generates the ``/home/anaconda-server/miniconda/etc/supervisord.conf`` file

.. _conf-mongo-supervisord:

Configure Supervisord For Local `mongodb` Install
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: follow this step **only** if you did a local install of mongodb as given in Section :ref:`local-mongo-install-no-sudo`

.. note:: ensure you have installed the Anaconda Repo packages (:ref:`install-ae-packages`) and configured Supervisord (:ref:`conf-supervisord`) before proceeding.

Create a local directory for mongo to use for writing out its databases and logs.

::

    $ mkdir -p ~/mongo/data && mkdir ~/mongo/log 

Append following lines for mongo to `~/miniconda2/etc/supervisord.conf`:

::

    [program:mongo]
    command=/home/anaconda-server/miniconda2/bin/mongod --dbpath /home/anaconda-server/mongo/data --logpath /home/anaconda-server/mongo/log/mongod.log --logappend --port 27017
    stdout_logfile=syslog
    stderr_logfile=syslog

Update the Supervisor process so it picks up the new config and runs the mongo process.

::

    $ supervisorctl update
    mongo: added process group


Verify the server and mongo is running:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    $ supervisorctl status

    binstar-scheduler                          RUNNING   pid 8445, uptime 0:00:09
    binstar-server                             RUNNING   pid 8263, uptime 0:06:39
    binstar-worker                             RUNNING   pid 8253, uptime 0:06:39
    binstar-worker-low:binstar-worker-low_00   RUNNING   pid 8261, uptime 0:06:39
    binstar-worker-low:binstar-worker-low_01   RUNNING   pid 8260, uptime 0:06:39
    binstar-worker-low:binstar-worker-low_02   RUNNING   pid 8259, uptime 0:06:39
    binstar-worker-low:binstar-worker-low_03   RUNNING   pid 8258, uptime 0:06:39
    binstar-worker-low:binstar-worker-low_04   RUNNING   pid 8257, uptime 0:06:39
    binstar-worker-low:binstar-worker-low_05   RUNNING   pid 8256, uptime 0:06:39
    binstar-worker-low:binstar-worker-low_06   RUNNING   pid 8255, uptime 0:06:39
    binstar-worker-low:binstar-worker-low_07   RUNNING   pid 8254, uptime 0:06:39
    mongo                                      RUNNING   pid 8451, uptime 0:00:05



Continue Server Configuration - requires `mongo` 
-------------------------------------------------

Create an initial “superuser” account for Anaconda Repo:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    anaconda-server-create-user --username "superuser" --password "yourpassword" --email "your@email.com" --superuser

.. note:: to ensure the bash shell does not process any of the characters in this password, limit the password to lower case letters, upper case letters and numbers, with no punctuation. After setup the password can be changed with the web interface.

Initialize the Anaconda Repo database:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    anaconda-server-db-setup --execute


Install Anaconda Repo License
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Visit **http://your.anaconda.server:8080**. Follow the onscreen
instructions and upload your license file. Log in with the superuser
user and password configured above. After submitting, you should see the
login page.

.. note:: Contact your sales representative or support representative if you cannot find or have questions about your license.

Mirror Installers for Miniconda
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Miniconda installers can be served by Anaconda Repo via the **static**
directory located at
**/home/anaconda-server/miniconda2/lib/python2.7/site-packages/binstar/static/extras**.
This is **required** for Anaconda Cluster integration. To serve up the
latest Miniconda installers for each platform, download them and copy
them to the **extras** directory.

Users will then be able to download installers at a URL that looks like the
following: http://<your host>:8080/static/extras/Miniconda3-latest-Linux-x86_64.sh

-  **Air Gap Installation:**

   ::

       # miniconda installers
       mkdir -p /tmp/extras
       pushd /tmp/extras
       URL="file://$INSTALLER_PATH/anaconda-suite/miniconda/"
       versions="Miniconda3-latest-Linux-x86_64.sh \
       Miniconda3-latest-MacOSX-x86_64.sh \
       Miniconda3-latest-Windows-x86.exe \
       Miniconda3-latest-Windows-x86_64.exe \
       Miniconda-latest-Linux-x86_64.sh \
       Miniconda-latest-MacOSX-x86_64.sh \
       Miniconda-latest-Windows-x86.exe \
       Miniconda-latest-Windows-x86_64.exe"

       for installer in $versions
        do
         curl -O $URL$installer
       done

       # Move installers into static directory
       popd
       cp -a /tmp/extras \
         /home/anaconda-server/miniconda2/lib/python2.7/site-packages/binstar/static

-  **Regular Installation:**

   ::

       # miniconda installers
       mkdir -p /tmp/extras
       pushd /tmp/extras
       URL="https://repo.continuum.io/miniconda/"
       versions="Miniconda3-latest-Linux-x86_64.sh \
       Miniconda3-latest-MacOSX-x86_64.sh \
       Miniconda3-latest-Windows-x86.exe \
       Miniconda3-latest-Windows-x86_64.exe \
       Miniconda-latest-Linux-x86_64.sh \
       Miniconda-latest-MacOSX-x86_64.sh \
       Miniconda-latest-Windows-x86.exe \
       Miniconda-latest-Windows-x86_64.exe"

       for installer in $versions
        do
         curl -O $URL$installer
       done

       # Move installers into static directory
       popd
       cp -a /tmp/extras /home/anaconda-server/miniconda2/lib/python2.7/site-packages/binstar/static

Mirror Anaconda Repo
~~~~~~~~~~~~~~~~~~~~~~~~

Now that Anaconda Repo is installed, we want to mirror packages into our
local repository. If mirroring from Anaconda Cloud, the process will
take hours or longer, depending on the available internet bandwidth. Use
the ``anaconda-server-sync-conda`` command to mirror all Anaconda
packages locally under the "anaconda" user account.

.. note:: Ignore any license warnings. Additional mirror filtering/whitelisting/blacklisting options can be found `here <https://docs.continuum.io/anaconda-repository/mirrors-sync-configuration>`_.

-  **Air Gap Installation:** Since we're mirroring from a local
   filesystem, some additional configuration is necessary.

   **1.** Create a mirror config file:


   ::

        echo "channels:" > /etc/anaconda-server/mirrors/conda.yaml
        echo "  - file://$INSTALLER_PATH/anaconda-suite/pkgs" >> /etc/anaconda-server/mirrors/conda.yaml


   **2.** Mirror the Anaconda packages:

   ::

       anaconda-server-sync-conda --mirror-config /etc/anaconda-server/mirrors/conda.yaml

-  **Regular Installation:** Mirror from Anaconda Cloud.

   ::

       anaconda-server-sync-conda

.. note:: Depending on the type of installation, this process may take hours.

To verify the local Anaconda Repo repo has been populated, visit
**http://your.anaconda.server:8080/anaconda** in a browser.

Optional: Mirror the R channel
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **Air Gap Installation:**

   **1.** Create a mirror config file:
   ::

        echo "channels:" > /etc/anaconda-server/mirrors/r-channel.yaml
        echo "  - file://$INSTALLER_PATH/R/pkgs" >> /etc/anaconda-server/mirrors/r-channel.yaml

   **2.** Mirror the r-packages::

       anaconda-server-sync-conda --mirror-config \
           /etc/anaconda-server/mirrors/r-channel.yaml --account=r-channel

-  **Regular Installation:**

   **1.** Create a mirror config file::

       vi /etc/anaconda-server/mirrors/r-channel.yaml

   **2.** Add the following::

       channels:
         - https://conda.anaconda.org/r

   **3.** Mirror the R packages::

       anaconda-server-sync-conda --mirror-config \
           /etc/anaconda-server/mirrors/r-channel.yaml --account=r-channel

Mirror the Anaconda Enterprise Notebooks Channel
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: If AEN is not setup and no packages from wakari channel are needed then this is an **optional** mirror. If you have an Anaconda Enterprise Notebooks server which will be using this Repo Server, then this channel must be mirrored.

If the local Anaconda Repo will be used by Anaconda Enterprise Notebooks
the recommended method is to mirror using the “wakari” user account.
To mirror the Anaconda Enterprise Notebooks repo, create the mirror config
YAML file below:

-  **Air Gap Installation:**

   **1.** Create a mirror config file
   ::

        echo "channels:" > /etc/anaconda-server/mirrors/wakari.yaml
        echo "  - file://$INSTALLER_PATH/wakari/pkgs" >> /etc/anaconda-server/mirrors/wakari.yaml


   **2.** Mirror the Anaconda Enteprise Notebooks packages:

   ::

       anaconda-server-sync-conda --mirror-config \
           /etc/anaconda-server/mirrors/wakari.yaml --account=wakari

-  **Regular Installation:**

   **1.** Create a mirror config file:

   ::

       vi /etc/anaconda-server/mirrors/wakari.yaml

   **2.** Add the following:

   ::

       channels:
         - https://conda.anaconda.org/t/<TOKEN>/anaconda-nb-extensions
         - https://conda.anaconda.org/wakari

   **3.** Mirror the Anaconda Enterprise Notebooks packages:

   ::

       anaconda-server-sync-conda --mirror-config \
         /etc/anaconda-server/mirrors/wakari.yaml --account=wakari

Where **“TOKEN”** is the Anaconda NB Extensions token you should
have received from Continuum Support.

Optional: Mirror the Anaconda Adam channel
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If the local Anaconda Repo will be used by Anaconda Adam, the
recommended method is to mirror using an “anaconda-adam” user.
To mirror the Anaconda Adam channel, create the mirror config
YAML file below:

-  **Air Gap Installation:**

   **1.** Create a mirror config file:

   ::

       echo "channels:" > /etc/anaconda-server/mirrors/anaconda-adam.yaml
       echo "  - file://$INSTALLER_PATH/anaconda-adam/pkgs" >> /etc/anaconda-server/mirrors/anaconda-adam.yaml

   **2.** Mirror the Anaconda Cluster Management packages:

   ::

       anaconda-server-sync-conda --mirror-config \
          /etc/anaconda-server/mirrors/anaconda-adam.yaml \
          --account=anaconda-adam

-  **Regular Installation:**

   **1.** Create a mirror config file:

   ::

       vi /etc/anaconda-server/mirrors/anaconda-adam.yaml

   **2.** Add the following:

   ::

       channels:
         - https://conda.anaconda.org/anaconda-adam

   **3.** Mirror the Anaconda Adam packages:

   ::

       anaconda-server-sync-conda --mirror-config \
          /etc/anaconda-server/mirrors/anaconda-adam.yaml \
          --account=anaconda-adam

Optional: Assemble installer resources manually
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

** Describe the process of adding platform tarballs to an $INSTALLER_PATH **

Optional: Adjust iptables to accept requests on port 80
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The easiest way to enable clients to access an Anaconda Repo on standard
ports is to configure the server to redirect traffic received on
standard HTTP port 80 to the standard Anaconda Repo HTTP port 8080.

.. note:: These commands assume the default state of iptables on CentOS 6.7 which is “on” and allowing inbound SSH access on port 22. Take caution; mistakes with iptables rules can render a remote machine inaccessible.

**Allow inbound access to tcp port 80:**

::

    sudo iptables -I INPUT -i eth0 -p tcp --dport 80 -m comment --comment "# Anaconda Repo #" -j ACCEPT

**Allow inbound access to tcp port 8080:**

::

    sudo iptables -I INPUT -i eth0 -p tcp --dport 8080 -m comment --comment "# Anaconda Repo #" -j ACCEPT

**Redirect inbound requests to port 80 to port 8080:**

::

    sudo iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 80 -m comment --comment "# Anaconda Repo #" -j REDIRECT --to-port 8080

**Display the current iptables rules:**

::

    sudo iptables -L -n
    Chain INPUT (policy ACCEPT)
    target     prot opt source               destination
    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:8080 # Anaconda Repo #
    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:80 # Anaconda Repo #
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

.. note:: the PREROUTING (nat) iptables chain is not displayed by default; to show it, use:

::

    sudo iptables -L -n -t nat
    Chain PREROUTING (policy ACCEPT)
    target     prot opt source               destination
    REDIRECT   tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:80 # Anaconda Repo # redir ports 8080

    Chain POSTROUTING (policy ACCEPT)
    target     prot opt source               destination

    Chain OUTPUT (policy ACCEPT)
    target     prot opt source               destination

Write the running iptables configuration to **/etc/sysconfig/iptables:**

::

    sudo service iptables save

Optional: Installing From Platform-based Archives 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Using the **64-Bit Linux** platform-based TAR archive to install Anaconda Repo is almost identical to the full install as described above, however there are a few things to note:

- The installer contains **ONLY** 64-Bit Linux packages. If support for additional platfoms is necessary, archives for those platforms should be downloaded as well.
- The installer does not contain packages for Anaconda Notebook, Anaconda Cluster or R for 64-Bit Linux. The full TAR archive is required if these packages are needed.

Adding support for additional platforms can be accomplished by downloading the corresponding TAR archives and using the following command (using 32-Bit Linux as an example):

::

    tar xvf linux-32-2016-07-06.tar -C $INSTALLER_PATH/anaconda-suite/pkgs/

This creates the **$INSTALLER_PATH/anaconda-suite/pkgs/linux-32** directory containing 32-Bit Linux packages.
The steps in the "Mirror Anaconda Repo" section above will then mirror these packages into the default (anaconda) channel in your local Anaconda Repo.




