.. This sets up section numbering
.. sectnum::

====================================
Anaconda Repository Install Method A
====================================

* Version: |Release| | |Today|

.. contents::
   :local:
   :depth: 1

This install method A walks through the steps needed to install
Anaconda Repository. This document is written for two audiences: 

* Those with access to the internet for installation, and 
* Those without internet access for security 
  reasons, referred to as "air gapped" environments. 
  Where necessary, additional instructions for air gapped
  environments are noted throughout this document. 

If you have any questions about these installation instructions, please contact your sales representative or Priority Support team for additional assistance.

.. image:: _static/repo.png


System requirements
-------------------

Hardware requirements
~~~~~~~~~~~~~~~~~~~~~

-  Physical server or VM
-  CPU: 2 x 64-bit 2 2.8GHz 8.00GT/s CPUs or better
-  Memory: 32GB RAM (per 50 users), minimum 4 GB
-  Storage: 300GB. (1TB for air gapped deployments) Additional space recommended if the repository will be used to store packages built by the customer.  With an empty repository, a base install requires 2 GB.

Software requirements
~~~~~~~~~~~~~~~~~~~~~

-  RHEL/CentOS 6.5 or later, Ubuntu 12.04+ 
-  Ubuntu users may need to install cURL.
-  Client environments may be Windows, Mac OS X or Linux
-  MongoDB version 2.6 (provided)
-  Anaconda Repository license file - included as part of the welcome packet 
-  cron: The anaconda-server user needs to add an entry to cron to start the server on reboot.

Linux system accounts
~~~~~~~~~~~~~~~~~~~~~

One Linux system account (UID) is added to the system during installation.

- ``mongod`` (RHEL) or ``mongodb`` (Ubuntu) created by the RPM or deb package
- ``anaconda-server``: create before installation or manually during installation; it is configurable to other names.

Software prerequisites
~~~~~~~~~~~~~~~~~~~~~~~

- Mongo version: >= 2.6.8 and < 3.0

Security Requirements
~~~~~~~~~~~~~~~~~~~~~

-  Privileged (``root``) access OR ``sudo`` capabilities
-  Open HTTP(S) port (configurable, default ``8080``)
-  SELinux policy edit privileges (SELinux does not have to be disabled for Anaconda Repository operation)
-  Optional: Ability to make ``iptables`` modifications
-  Optional: SSL certificate

Network Requirements
~~~~~~~~~~~~~~~~~~~~

* TCP Ports

========= ==== ======= ======== ======== ============ ========
Direction Type Port    Protocol Optional Configurable Comments
--------- ---- ------- -------- -------- ------------ --------
Inbound   TCP  8080    HTTP              yes          Anaconda Repository
Inbound   TCP    22    SSH      yes
Outbound  TCP   443    HTTPS    yes                   to Anaconda Cloud or secondary local Anaconda Repo
Outbound  TCP    25    SMTP     yes                   email notifications
Outbound  TCP  389/636 LDAP(S)  yes      yes          authentication integration
========= ==== ======= ======== ======== ============ ========

Other requirements
~~~~~~~~~~~~~~~~~~

- License file provided to you by Continuum at the time of purchase
- Installation tokens for binstar and anaconda-server channels provided by Continuum at the time of purchase. Not applicable for air gapped installs.
- Optional: Your Anaconda Cloud (anaconda.org) account credentials. Not applicable for air gapped installs.

.. _airgap:

Air gapped installation
~~~~~~~~~~~~~~~~~~~~~~~

For air gapped environments, Continuum ships the entire Anaconda 
product suite on portable storage medium or as a downloadable TAR archive. 

Continuum also provides a set of air gapped TAR archives for
those environments needing only certain platform architectures,
such as 64-Bit Linux, 32-Bit Linux, etc. 

These platform-based archives include all of the available packages for that platform. The 64-Bit Linux archive contains 64-Bit Linux packages PLUS packages necessary to install Anaconda Repository.

Additional platforms may be added by downloading the corresponding TAR archive
and importing it to the local Anaconda Repository. To prepare your environment before starting the Anaconda Repository Installation, see the section below entitled "Optional: Installing from Platform-based Archives."  

The air gap media must be available on the target server at `$INSTALLER_PATH` where the software is being installed. 

The air gap installation assets may be obtained two different ways:

#. Via USB drive sent to client via express delivery, or

#. Client may download the latest archive tarball or component tarballs and expands the archive to ``/installer``. 

.. _installer_path:

Set installer path
^^^^^^^^^^^^^^^^^^^

After obtaining the assets, the air gap media must be available on the target server at `$INSTALLER_PATH` where the software is being installed. The ``$INSTALLER_PATH`` variable must be set to the location of the air-gap media as displayed below. 

#. For air-gap USB drive media mounted on ``/installer``:

   .. code-block:: bash
   
       export INSTALLER_PATH=/installer

NOTE: Change the ``$INSTALLER_PATH`` to the parent directory to the ``anaconda-suite`` directory. 

Expand installer
^^^^^^^^^^^^^^^^

If the full anaconda installer is downloaded and expanded, say the `oct-2016` archive: `anaconda-full-2016-09-30.tar`:

   .. code-block:: bash
   
       tar xvf anaconda-full-2016-09-30.tar -C /installer/
       export INSTALLER_PATH=/installer/anaconda-full-2016-09-30

The `anaconda-full-2016-09-30.tar` is roughly 140GB. 

NOTE: If only a subset of components are required, please refer to :ref:`comp-install`.


Air gapped full installer contents 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

File `anaconda-full-2016-%m-%d.tar`

NOTE: Substitute ``anaconda-full-2016`` with the exact name of the file you receive.

.. code-block:: bash

  ls $INSTALLER_PATH
  anaconda-adam/
  anaconda-cluster/
  anaconda-server/
  anaconda-suite/
  mongodb-org-2.6.8-1.x86_64.rpm
  mongodb-org-mongos-2.6.8-1.x86_64.rpm
  mongodb-org-server-2.6.8-1.x86_64.rpm
  mongodb-org-shell-2.6.8-1.x86_64.rpm
  mongodb-org-tools-2.6.8-1.x86_64.rpm
  r/
  wakari/

.. _comp-install:

Optional: Air gapped platform based archives (Linux)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To install Anaconda Repository and mirror only packages for a subset of
platforms such as Linux-64, download a component based TAR archive.  Using the
**64-Bit Linux** platform-based TAR archive to install Anaconda Repo is almost
identical to the full install, after we have created the same file structure in
`$INSTALLER_PATH`. Some notes about platform based archives:

- The installer contains 64-Bit Linux packages **ONLY**. If support for additional platforms is necessary, archives for those platforms should be requested or downloaded as well.
- The installer does NOT contain packages for Anaconda Notebook, Anaconda Scale or R for 64-Bit Linux. The full TAR archive is required if these packages are needed.
- Each component has an MD5 value and list file which are small and included for convenience. 

The following table summarizes various components required for installing only AE-Repo and mirroring linux-64 packages. 

NOTE: The top-level directory for all archives is: `anaconda-full-`date +%Y-%m-%d`/`


+---------------------------------------+---------------------------------------------+--------+
| Tarball                               | Contents                                    | Size   |
+=======================================+=============================================+========+
| anaconda-full-`date +%Y-%m-%d`.tar    | All AE components and dependencies:         |  140 GB|
|                                       |                                             |        |
|                                       | - AE-N installers + dependencies            |        |
|                                       | - latest miniconda version (all platforms)  |        |
|                                       | - packages for all platforms                |        |
+---------------------------------------+---------------------------------------------+--------+
| ae-repo-linux-64-`date +%Y-%m-%d`.tar | - packages for linux-64                     |   40 GB|
|                                       | - including channels for AE-Repo packages   |        |
+---------------------------------------+---------------------------------------------+--------+
| win-64-`date +%Y-%m-%d`.tar           | - packages for win-64                       |   24 GB|
+---------------------------------------+---------------------------------------------+--------+
| osx-64-`date +%Y-%m-%d`.tar           | - packages for osx-64                       |   25 GB|
+---------------------------------------+---------------------------------------------+--------+

As an example, if you need only AE-Repo, linux-64 and win-64 packages, download ae-repo-linux-64-`date +%Y-%m-%d`.tar and win-64-`date +%Y-%m-%d`.tar. Also download the associated md5 files to check integrity of downloaded data. 

TIP: To run in background and continue download after logout, use nohup. 

After downloading, expand the tarballs. It will take some time to expand the archives. See example below:

.. code-block:: bash

   tar xf *.tar -C /installer
   export INSTALLER_PATH=/installer/anaconda-full-`date +%Y-%m-%d`/


System-wide MongoDB installation 
--------------------------------

NOTE: This step requires ``sudo``

Download MongoDB packages
~~~~~~~~~~~~~~~~~~~~~~~~~

-  **Air gapped installation:** Skip this step.

-  **Regular installation:**

   ::
   
      RPM_CDN="https://820451f3d8380952ce65-4cc6343b423784e82fd202bb87cf87cf.ssl.cf1.rackcdn.com"
      curl -O $RPM_CDN/mongodb-org-tools-2.6.8-1.x86_64.rpm
      curl -O $RPM_CDN/mongodb-org-shell-2.6.8-1.x86_64.rpm
      curl -O $RPM_CDN/mongodb-org-server-2.6.8-1.x86_64.rpm
      curl -O $RPM_CDN/mongodb-org-mongos-2.6.8-1.x86_64.rpm
      curl -O $RPM_CDN/mongodb-org-2.6.8-1.x86_64.rpm

Install MongoDB packages
~~~~~~~~~~~~~~~~~~~~~~~~

-  **Air gapped installation:**

   ::
   
       sudo yum install -y $INSTALLER_PATH/mongodb-org*

-  **Regular installation:**

   ::
   
       sudo yum install -y mongodb-org*


Start MongoDB
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    sudo service mongod start

Verify Mongod is running
~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    sudo service mongod status
    mongod (pid 1234) is running...

NOTE: Additional MongoDB installation information can be found `here <https://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat/>`__.


Configure Anaconda Repository
------------------------------

Before installing Anaconda Repository components, the following must be done by someone with `sudo` privileges.

Create Anaconda Repository administrator account
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In a terminal window, create a new user account named ``anaconda-server`` for the administrator of Anaconda Repo:

::

    sudo useradd -m anaconda-server

NOTE: The account named ``anaconda-server`` may be configured to any other service account name.

Create Anaconda Repository directories
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    sudo mkdir -m 0770 /etc/anaconda-server
    sudo mkdir -m 0770 /var/log/anaconda-server
    sudo mkdir -m 0770 -p /opt/anaconda-server/package-storage
    sudo mkdir -m 0770 /etc/anaconda-server/mirrors

Give the anaconda-server user ownership of directories
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    sudo chown -R anaconda-server. /etc/anaconda-server
    sudo chown -R anaconda-server. /var/log/anaconda-server
    sudo chown -R anaconda-server. /opt/anaconda-server/package-storage
    sudo chown -R anaconda-server. /etc/anaconda-server/mirrors

Switch to the Anaconda Repository administrator account
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Switch account, and set `$INSTALLER_PATH` environment variable correctly for your system as described above in installer_path_.

::

    sudo su - anaconda-server
    INSTALLER_PATH=<set-to-path>

NOTE: Substitute <set-to-path> for the actual path.

Install Miniconda bootstrap version
-----------------------------------

Fetch the download script using curl
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **Air gapped installation:** Skip this step.

-  **Regular installation:**

   ::
   
       curl 'http://repo.continuum.io/miniconda/Miniconda2-latest-Linux-x86_64.sh' > Miniconda.sh

Run the Miniconda.sh installer script
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-  **Air gapped Installation:**

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
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Do you wish the installer to prepend the Miniconda install location to
PATH in your /home/anaconda-server/.bashrc ?

::

    [yes|no] yes

For the new path changes to take effect, “source” your .bashrc
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    source ~/.bashrc


Install Anaconda Repository Enterprise Packages
------------------------------------------------
The following sections detail the steps required to install Anaconda Repo.


Add the defaults, binstar anaconda-server channels to Conda
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **Air gapped Installation:** Add the channels from local files.

   ::

       conda config --add channels  file://$INSTALLER_PATH/anaconda-suite/pkgs/
       conda config --add channels  file://$INSTALLER_PATH/anaconda-server/pkgs/
       conda config --remove channels defaults --force

   .. note:: The packages from `binstar` channel are included with the `anaconda-server` channel.


-  **Regular Installation:** Add the channels from Anaconda Cloud.

   ::

       export BINSTAR_TOKEN=<your binstar token>
       export ANACONDA_TOKEN=<your anaconda-server token>
       conda config --add channels https://conda.anaconda.org/t/$BINSTAR_TOKEN/binstar/
       conda config --add channels https://conda.anaconda.org/t/$ANACONDA_TOKEN/anaconda-server/

   .. note:: You should have received **two** tokens from Continuum Support, one for each channel. If you haven't, please contact support@continuum.io. Tokens are not required for air gapped installs.


Install AE-Repository packages via conda And Setup Config Files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Install packages for running AE-Repo server

   ::
  
      conda install anaconda-client binstar-server binstar-static cas-mirror


#. Initialize the web server for Anaconda Repository

   ::

      anaconda-server-config --init --config-file /etc/anaconda-server/config.yaml

#. Set the Anaconda Repository package storage location

   ::

      anaconda-server-config --set fs_storage_root /opt/anaconda-server/package-storage \
                           --config-file /etc/anaconda-server/config.yaml


Set up automatic restart on reboot, fail or error
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Configure Supervisord**

::

    anaconda-server-install-supervisord-config.sh


This step:

#. writes a config file for supervisord in `~/miniconda2/etc/supervisord.conf`

#. creates the following entry in the anaconda-server user’s crontab:

   ``@reboot /home/anaconda-server/miniconda2/bin/supervisord``

#. generates the ``/home/anaconda-server/miniconda2/etc/supervisord.conf`` file

#. verify the server is running:

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


Continue Server Configuration - requires `mongo` 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Create an initial "superuser" account for Anaconda Repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
::

    anaconda-server-create-user --username "superuser" --password "yourpassword" \
                                --email "your@email.com" --superuser

.. note:: To ensure the bash shell does not process any of the
  characters in this password, limit the password to lower case letters,
  upper case letters and numbers, with no punctuation. After setup the
  password can be changed with the web interface.

Initialize the Anaconda Repository database
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    anaconda-server-db-setup --execute


Install Anaconda Repository License
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Visit **http://your.anaconda.server:8080**. Follow the onscreen
instructions and upload your license file. Log in with the superuser
user and password configured above. After submitting, you should see the
login page.

.. note:: Contact your sales representative or support representative if you cannot find or have questions about your license.

Setup Mirrors
--------------

Mirror Installers for Miniconda 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Miniconda installers can be served by Anaconda Repository via the **static**
directory located at
**/home/anaconda-server/miniconda2/lib/python2.7/site-packages/binstar/static/extras**.
This is **required** for Anaconda Cluster integration. To serve up the
latest Miniconda installers for each platform, download them and copy
them to the **extras** directory.

Users will then be able to download installers at a URL that looks like the
following: http://<your host>:8080/static/extras/Miniconda3-latest-Linux-x86_64.sh

#. Set the URL variable correctly for AirGap vs Regular installs:

   **Air gapped Installation:**
   
   ::
   
       URL="file://$INSTALLER_PATH/anaconda-suite/miniconda"
   
   **Regular Installation:**
   
   ::
   
       URL="https://repo.continuum.io/miniconda"

#. Move the latest installers to static directory

   .. code-block:: bash

       mkdir -p /tmp/extras
       pushd /tmp/extras

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
           curl -O $URL/$installer
       done
       
       # Move installers into static directory
       popd
       cp -a /tmp/extras \
         /home/anaconda-server/miniconda2/lib/python2.7/site-packages/binstar/static 


Mirror Anaconda Repo
~~~~~~~~~~~~~~~~~~~~~~~~

Now that Anaconda Repository is installed, we want to mirror packages into our
local repository. If mirroring from Anaconda Cloud, the process will
take hours or longer, depending on the available internet bandwidth. Use
the ``anaconda-server-sync-conda`` command to mirror all Anaconda
packages locally under the "anaconda" user account.

.. note:: Ignore any license warnings. Additional mirror filtering/whitelisting/blacklisting options can be found `here <https://docs.continuum.io/anaconda-repository/mirrors-sync-configuration>`_.

**Air gapped installation:** Since we're mirroring from a local filesystem, some additional configuration is necessary.

#. Create a mirror config file:


   ::

        echo "channels:" > /etc/anaconda-server/mirrors/conda.yaml
        echo "  - file://$INSTALLER_PATH/anaconda-suite/pkgs" >> \
                  /etc/anaconda-server/mirrors/conda.yaml

#. (Optional) If mirroring packages for subset of platforms (eg. linux-64 only as shown in :ref:`comp-install`), or
   mirroring packages for a subset of python versions, append following:
   
   ::

        echo "platforms:" >> /etc/anaconda-server/mirrors/conda.yaml
        echo "  - linux-64" >> /etc/anaconda-server/mirrors/conda.yaml

#. Mirror the Anaconda packages:

   ::

       anaconda-server-sync-conda --mirror-config /etc/anaconda-server/mirrors/conda.yaml



**Regular Installation:** If no customization is required, there is no need to define a config file.

::

    anaconda-server-sync-conda


.. note:: Depending on the type of installation, this process may take hours.

To verify the local Anaconda Repository repo has been populated, visit
**http://your.anaconda.server:8080/anaconda** in a browser.

Optional: Mirror the R channel
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Air gapped installation:**

#. Create a mirror config file:
   ::

        echo "channels:" > /etc/anaconda-server/mirrors/r-channel.yaml
        echo "  - file://$INSTALLER_PATH/r/pkgs" >> /etc/anaconda-server/mirrors/r-channel.yaml

#. (Optional) If mirroring packages for subset of platforms (eg. linux-64 only as shown in :ref:`comp-install`), append following:
   
   ::

        echo "platforms:" >> /etc/anaconda-server/mirrors/r-channel.yaml
        echo "  - linux-64" >> /etc/anaconda-server/mirrors/r-channel.yaml


#. Mirror the r-packages::

       anaconda-server-sync-conda --mirror-config \
           /etc/anaconda-server/mirrors/r-channel.yaml --account=r-channel

**Regular Installation:**

#. Create a mirror config file::

       vi /etc/anaconda-server/mirrors/r-channel.yaml

#. Add the following::

       channels:
         - https://conda.anaconda.org/r

#. Mirror the R packages::

       anaconda-server-sync-conda --mirror-config \
           /etc/anaconda-server/mirrors/r-channel.yaml --account=r-channel

Mirror the Anaconda Enterprise Notebooks Channel
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: If AEN is not setup and no packages from wakari channel are needed then this is an **optional** mirror. If you have an Anaconda Enterprise Notebooks server which will be using this Repo Server, then this channel must be mirrored.

If the local Anaconda Repository will be used by Anaconda Enterprise Notebooks
the recommended method is to mirror using the “wakari” user.

To mirror the Anaconda Enterprise Notebooks repo, create the mirror config
YAML file below:

**Air gapped installation:**

#. Create a mirror config file
   ::

        echo "channels:" > /etc/anaconda-server/mirrors/wakari.yaml
        echo "  - file://$INSTALLER_PATH/wakari/pkgs" >> /etc/anaconda-server/mirrors/wakari.yaml


#. Mirror the Anaconda Enteprise Notebooks packages:

   ::

       anaconda-server-sync-conda --mirror-config \
           /etc/anaconda-server/mirrors/wakari.yaml --account=wakari

**Regular Installation:**

#. Create a mirror config file:

   ::

       vi /etc/anaconda-server/mirrors/wakari.yaml

#. Add the following:

   ::

       channels:
         - https://conda.anaconda.org/t/<TOKEN>/anaconda-nb-extensions
         - https://conda.anaconda.org/wakari

#. Mirror the Anaconda Enterprise Notebooks packages:

   ::

       anaconda-server-sync-conda --mirror-config \
         /etc/anaconda-server/mirrors/wakari.yaml --account=wakari

Where ``TOKEN`` is the Anaconda NB Extensions token you should
have received from Continuum Support.

Optional: Mirror the Anaconda Cluster channel
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To mirror the anaconda-cluster packages for managing a cluster, create the mirror config YAML file as below: 

**Air gapped installation:**

#. Create a mirror config file:

   ::

       echo "channels:" > /etc/anaconda-server/mirrors/anaconda-cluster.yaml
       echo "  - file://$INSTALLER_PATH/anaconda-cluster/pkgs" >> \
            /etc/anaconda-server/mirrors/anaconda-cluster.yaml


#. (Optional) If mirroring packages for subset of platforms (eg. linux-64 only as shown in :ref:`comp-install`), append following:
   
   ::

        echo "platforms:" >> /etc/anaconda-server/mirrors/anaconda-cluster.yaml
        echo "  - linux-64" >> /etc/anaconda-server/mirrors/anaconda-cluster.yaml


#. Mirror the Anaconda Cluster Management packages:

   ::

       anaconda-server-sync-conda --mirror-config \
          /etc/anaconda-server/mirrors/anaconda-cluster.yaml \
          --account=anaconda-cluster

**Regular Installation:**

#. Create a mirror config file:

   ::

       vi /etc/anaconda-server/mirrors/anaconda-cluster.yaml

#. Add the following:

   ::

       channels:
         - https://conda.anaconda.org/anaconda-cluster

#. Mirror the Anaconda Cluster packages:

   ::

       anaconda-server-sync-conda --mirror-config \
          /etc/anaconda-server/mirrors/anaconda-cluster.yaml \
          --account=anaconda-cluster


Optional: Adjust iptables to accept requests on port 80
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The easiest way to enable clients to access an Anaconda Repository on standard
ports is to configure the server to redirect traffic received on
standard HTTP port 80 to the standard Anaconda Repository HTTP port 8080.

.. note:: These commands assume the default state of iptables on CentOS 6.7 which is “on” and allowing inbound SSH access on port 22. Take caution; mistakes with iptables rules can render a remote machine inaccessible.

**Allow inbound access to tcp port 80:**

::

    sudo iptables -I INPUT -i eth0 -p tcp --dport 80 -m comment \
                  --comment "# Anaconda Repository #" -j ACCEPT

**Allow inbound access to tcp port 8080:**

::

    sudo iptables -I INPUT -i eth0 -p tcp --dport 8080 -m comment \
                  --comment "# Anaconda Repository #" -j ACCEPT

**Redirect inbound requests to port 80 to port 8080:**

::

    sudo iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 80 -m comment \
                  --comment "# Anaconda Repository #" -j REDIRECT --to-port 8080

**Display the current iptables rules:**

::

    sudo iptables -L -n
    Chain INPUT (policy ACCEPT)
    target     prot opt source     destination
    ACCEPT     tcp  --  0.0.0.0/0  0.0.0.0/0   tcp dpt:8080 # Anaconda Repository #
    ACCEPT     tcp  --  0.0.0.0/0  0.0.0.0/0   tcp dpt:80 # Anaconda Repository #
    ACCEPT     all  --  0.0.0.0/0  0.0.0.0/0   state RELATED,ESTABLISHED
    ACCEPT     icmp --  0.0.0.0/0  0.0.0.0/0
    ACCEPT     all  --  0.0.0.0/0  0.0.0.0/0
    ACCEPT     tcp  --  0.0.0.0/0  0.0.0.0/0   state NEW tcp dpt:22
    REJECT     all  --  0.0.0.0/0  0.0.0.0/0   reject-with icmp-host-prohibited

    Chain FORWARD (policy ACCEPT)
    target     prot opt source     destination
    REJECT     all  --  0.0.0.0/0  0.0.0.0/0   reject-with icmp-host-prohibited

    Chain OUTPUT (policy ACCEPT)
    target     prot opt source     destination

.. note:: the PREROUTING (nat) iptables chain is not displayed by default; to show it, use:

::

    sudo iptables -L -n -t nat
    Chain PREROUTING (policy ACCEPT)
    target     prot opt source     destination
    REDIRECT   tcp  --  0.0.0.0/0  0.0.0.0/0   tcp dpt:80 # Anaconda Repository # redir ports 8080

    Chain POSTROUTING (policy ACCEPT)
    target     prot opt source     destination

    Chain OUTPUT (policy ACCEPT)
    target     prot opt source     destination

Write the running iptables configuration to **/etc/sysconfig/iptables:**

::

    sudo service iptables save

