# Kodecloud Engineer
This document is designed to provide a comprehensive overview of the commands and tasks that will be performed during the Kodecloud engineer program.


## System Administrator, Nautilus, xFusionCorp

 1. Create a Linux User with non-interactive shell :

    To create a Linux user with a non-interactive shell, you can use the following steps:

    Open a terminal and log in as the root user or use sudo to run the following commands.

    Run the following command to add a new user:

    ```
    useradd -m -s /bin/false USERNAME
    ```

    Replace ***`USERNAME`*** with the desired username. The ***`-m`*** option creates a home directory for the user, and the ***`-s`*** option sets the default shell to ***`/bin/false`***, which is a non-interactive shell.

 2. Give execution permission to script. Make sure that every users are able to use it :

    ```
    chmod 755 /usr/local/script.sh
    ```

 3. During the daily standup, it was pointed out that the timezone across Nautilus Application Servers in Stratos Datacenter doesn't match with that of the local datacenter's
    timezone, which is America/Martinique. Correct the mismatch. :

    
    ```
    sudo timedatectl set-timezone America/Martinique
    ```

4. Some users of the monitoring app have reported issues with xFusionCorp Industries mail server. They have a mail server in Stork DC where they are using postfix mail transfer agent. Postfix service seems to fail. Try to identify the root cause and fix it.

   
   [root@stmail01 postfix]# service postfix status -l
   Redirecting to /bin/systemctl status  -l postfix.service
●  postfix.service - Postfix Mail Transport Agent
   Loaded: loaded (/usr/lib/systemd/system/postfix.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Thu 2023-02-16 11:24:34 UTC; 6s ago
   Process: 712 ExecStart=/usr/sbin/postfix start (code=exited, status=1/FAILURE)
   Process: 711 ExecStartPre=/usr/libexec/postfix/chroot-update (code=exited, status=0/SUCCESS)
   Process: 708 ExecStartPre=/usr/libexec/postfix/aliasesdb (code=exited, status=75)

   Feb 16 11:24:33 stmail01.stratos.xfusioncorp.com postfix[712]: fatal: parameter inet_interfaces: no local interface found for ::1
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: Child 712 belongs to postfix.service
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: postfix.service: control process exited, code=exited status=1
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: postfix.service got final SIGCHLD for state start
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: postfix.service changed start -> failed
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: Job postfix.service/start finished, result=failed
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: Failed to start Postfix Mail Transport Agent.
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: Unit postfix.service entered failed state.
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: postfix.service failed.
   Feb 16 11:24:34 stmail01.stratos.xfusioncorp.com systemd[1]: postfix.service: cgroup is empty
   [root@stmail01 postfix]#
   

   As we can see from the ouput above, the error "fatal: parameter inet_interfaces: no local interface found for ::1" is refering 
   to the entry in /etc/hosts file which had "::1" defined for 127.0.0.1

   To solve the issue the "::1" was commented from /etc/hosts file and the postfix service was restarted.

5. One of the Nautilus developers has copied confidential data on the jump host in Stratos DC. That data must be copied to one of the app servers. Because developers do not have access to app servers, they asked the system admins team to accomplish the task for them.
Copy /tmp/nautilus.txt.gpg file from jump server to App Server 1 at location /home/app.

   thor@jump_host ~$ scp /tmp/nautilus.txt.gpg tony@172.16.238.10:/home/app
   The authenticity of host '172.16.238.10 (172.16.238.10)' can't be established.
   ECDSA key fingerprint is SHA256:SySamszyWhhLGFiybhGBqfrr8g55wS/3e37ZpBOvICs.
   ECDSA key fingerprint is MD5:6d:31:18:2a:f9:07:f3:29:dd:0a:d3:1f:6e:04:0a:db.
   Are you sure you want to continue connecting (yes/no)? yes
   Warning: Permanently added '172.16.238.10' (ECDSA) to the list of known hosts.
   tony@172.16.238.10's password: 
   nautilus.txt.gpg                                                               100%   74   374.3KB/s   00:00

6. There was some users data copied on Nautilus App Server 2 at /home/usersdata location by the Nautilus production support team in Stratos DC. Later they found that they mistakenly mixed up different user data there. Now they want to filter out some user data and copy it to another location. Find the details below:
On App Server 2 find all files (not directories) owned by user jim inside /home/usersdata directory and copy them all while keeping the folder structure (preserve the directories path) to /blog directory.

      To find all files owned by user "jim" inside the "/home/usersdata" directory, you can use the find command with the -type f and -user options.

   The -type f option ensures that only files are found, and the -user jim option will find files owned by the user "jim".

   The cp command can then be used to copy the files to the "/blog" directory while preserving the directory structure. To preserve the directory structure, you can use the --parents option with the cp command.

   Here's the command you can use:

   ```
   find /home/usersdata -type f -user jim -exec cp --parents {} /blog \;
   ```

   This will find all the files owned by user "jim" inside the "/home/usersdata" directory and its subdirectories, and copy them to the "/blog" directory while preserving the directory structure. The -exec option is used to execute the cp command for each file found.

   Note that the --parents option with cp is used to create the same directory structure as the original file in the destination directory.

7. A developer james has been assigned Nautilus project temporarily as a backup resource. As a temporary resource for this project, we need a temporary user for james. It’s a good idea to create a user with a set expiration date so that the user won't be able to access servers beyond that point.
Therefore, create a user named james on the App Server 3. Set expiry date to 2021-04-15 in Stratos Datacenter. Make sure the user is created as per standard and is in lowercase

   Use the following command to create a new user named "james":

   ```
   sudo adduser james
   ```

   Once the user is created, you can use the chage command to set the expiry date. The chage command is used to modify the user password and account expiry information. Use the following command to set the expiry date to April 15, 2021:

   ```
   sudo chage -E 2021-04-15 james
   ```

8. Install and configure ntp server on the App server 01. Make sure that it uses the following ntp server '2.cn.pool.ntp.org'

   ```
   yum install -y ntp
   ```


   Ensure that the NTP service starts automatically when the server boots up, run the command :


   ```
   systemctl enable ntpd

   ```


   To add the NTP server '2.cn.pool.ntp.org' in the /etc/ntp.conf file on your CentOS server, add the following line :

   ```
   server 2.cn.pool.ntp.org
   ```

9. During the monthly compliance meeting, it was pointed out that several servers in the Stratos DC do not have a valid banner. The security team has provided serveral approved templates which should be applied to the servers to maintain compliance. These will be displayed to the user upon a successful login.
Update the message of the day on all application and db servers for Nautilus. Make use of the approved template located at /home/thor/nautilus_banner on jump host


   To update the motd on all app and db servers, update the file /etc/motd on the concerned servers with the content of the approved template located at /home/thor/nautilus_banner.

10. The Nautilus system admins team has prepared scripts to automate several day-to-day tasks. They want them to be deployed on all app servers in Stratos DC on a set schedule. Before that they need to test similar functionality with a sample cron job. Therefore, perform the steps below:
a. Install cronie package on all Nautilus app servers and start crond service.
b. Add a cron */5 * * * * echo hello > /tmp/cron_text for root user.

      ```
      yum install -y chronie
      ```

      ```
      crontab -e -u root
      ```

      ```
      */5 * * * * echo hello > /tmp/cron_text
      ```

11. The system admins team of xFusionCorp Industries has noticed intermittent issues with DNS resolution in several apps . App Server 1 in Stratos Datacenter is having some DNS resolution issues, so we want to add some additional DNS nameservers on this server. As a temporary fix we have decided to go with Google public DNS (ipv4). Please make appropriate changes on this server.

      Open the /etc/resolv.conf file in a text editor and add the following lines at the top of the file.


      ```
      nameserver 8.8.8.8
      nameserver 8.8.4.4
      ```

12. New tools have been installed on the app server in Stratos Datacenter. Some of these tools can only be managed from the graphical user interface. Therefore, there are requirements for these app servers. On all App servers in Stratos Datacenter change the default runlevel so that they can boot in GUI (graphical user interface) by default. Please do not try to reboot these servers.

      Check current default run level :

      ```
      systemctl get-default
      ```

      Set graphical user interface as default run level :
      
      ```
      systemctl set-default graphical.target
      ```

13. After doing some security audits of servers, xFusionCorp Industries security team has implemented some new security policies. One of them is to disable direct root login through SSH.
    Disable direct SSH root login on all app servers in Stratos Datacenter

      Open a file editor and modify  /etc/ssh/sshd_config file.
      Uncomment "#PermitRootLogin yes"

      ```
      PermitRootLogin no
      ```

      Then restart sshd service so that the changes take effect

      ```
      systemctl restart sshd
      ```

14. There is some data on Nautilus App Server 1 in Stratos DC. Data needs to be altered in several of the files. On Nautilus App Server 1, alter the /home/BSD.txt file as per details given below:

      a. Delete all lines containing word following and save results in /home/BSD_DELETE.txt file. (Please be aware of case sensitivity)
      
      b. Replace all occurrence of word and to is and save results in /home/BSD_REPLACE.txt file.
      
      Note: Let's say you are asked to replace word to with from. In that case, make sure not to alter any words containing this string; for example upto, contributor etc.

      For a 

      ```
      sed '/\<following\>/d' /home/BSD.txt > /home/BSD_DELETE.txt
      ```

      For b

      ```
      sed 's/\band\b/is/g' BSD.txt > BSD_REPLACE.txt
      ```

15. The backup server in the Stratos DC contains several template XML files used by the Nautilus application. However, these template XML files must be populated with valid data before they can be used. One of the daily tasks of a system admin working in the xFusionCorp industries is to apply string and file manipulation commands!

      Replace all occurances of the string About to Sonar on the XML file /root/nautilus.xml located in the backup server.


      ```
      sed -i.bak 's/\bAbout\b/Sonar/g' nautilus.xml
      ```

16. As per details shared by the development team, the new application release has some dependencies on the back end. There are some packages/services that need to be installed on all app servers under Stratos Datacenter. As per requirements please perform the following steps:


      a. Install nscd package on all the application servers.

      b. Once installed, make sure it is enabled to start during boot.

      ```
      yum install -y nscd
      ```

      ```
      systemctl start nscd && systemctl enable nscd
      ```

17. The system admins team of xFusionCorp Industries has set up a new tool on all app servers, as they have a requirement to create a service user account that will be used by that tool. They are finished with all apps except for App Server 1 in Stratos Datacenter.
Create a user named rose in App Server 1 without a home directory.


      ```
      sudo useradd --no-create-home rose
      ```


18. The system admins team of xFusionCorp Industries has set up some scripts on jump host that run on regular intervals and perform operations on all app servers in Stratos Datacenter. To make these scripts work properly we need to make sure the thor user on jump host has password-less SSH access to all app servers through their respective sudo users (i.e tony for app server 1). Based on the requirements, perform the following:
Set up a password-less authentication from user thor on jump host to all app servers through their respective sudo users.

      ```
      ssh-keygen
      ```

      ```
      ssh-copy-id
      ```


19. The production support team of xFusionCorp Industries is working on developing some bash scripts to automate different day to day tasks. One is to create a bash script for taking websites backup. They have a static website running on App Server 3 in Stratos Datacenter, and they need to create a bash script named blog_backup.sh which should accomplish the following tasks. (Also remember to place the script under /scripts directory on App Server 3)

      a. Create a zip archive named xfusioncorp_blog.zip of /var/www/html/blog directory.

      b. Save the archive in /backup/ on App Server 3. This is a temporary storage, as backups from this location will be clean on weekly basis. Therefore, we also need to save this backup archive on Nautilus Backup Server.

      c. Copy the created archive to Nautilus Backup Server server in /backup/ location.

      d. Please make sure script won't ask for password while copying the archive file. Additionally, the respective server user (for example, tony in case of App Server 1) must be able to run it.



      ```
      #!bin/bash

      zip -r /backup/xfusioncorp_blog.zip /var/www/html/blog
      scp /backup/xfusioncorp_blog.zip clint@stbkp01:/backup/
      ```

      ```
         [banner@stapp03 ~]$ ssh-keygen
         Generating public/private rsa key pair.
         Enter file in which to save the key (/home/banner/.ssh/id_rsa): 
         Enter passphrase (empty for no passphrase): 
         Enter same passphrase again: 
         Your identification has been saved in /home/banner/.ssh/id_rsa.
         Your public key has been saved in /home/banner/.ssh/id_rsa.pub.
         The key fingerprint is:
         SHA256:jvziE5IdZ2dOumUedxv47Pz6BdvXj3wVlQXEW0rnne8 banner@stapp03.stratos.xfusioncorp.com
         The key's randomart image is:
         +---[RSA 2048]----+
         |             oo.+|
         |              o.+|
         |             . *+|
         |      . o +   ooo|
         |     o +S*   .. o|
         |    o.oo. = o o++|
         |     .o..= o +.+*|
         |      o.. .  o+.E|
         |     ..o.    .*=+|
         +----[SHA256]-----+
         [banner@stapp03 ~]$
      ```

      ```
      [banner@stapp03 ~]$ ssh-copy-id clint@stbkp01
      /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/banner/.ssh/id_rsa.pub"
      The authenticity of host 'stbkp01 (172.16.238.16)' can't be established.
      ECDSA key fingerprint is SHA256:4BxsdumkCB1lr6ItcFDwzXHvQcXGS6bX06sZi566hso.
      ECDSA key fingerprint is MD5:a3:90:67:b3:fc:81:3a:3b:9f:24:1d:6e:ce:ce:27:b8.
      Are you sure you want to continue connecting (yes/no)? yes
      /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
      /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
      clint@stbkp01's password: 

      Number of key(s) added: 1

      Now try logging into the machine, with:   "ssh 'clint@stbkp01'"
      and check to make sure that only the key(s) you wanted were added.

      [banner@stapp03 ~]$
      ```

      ```
      [banner@stapp03 ~]$ sudo chmod +x /scripts/blog_backup.sh

      We trust you have received the usual lecture from the local System
      Administrator. It usually boils down to these three things:

      #1) Respect the privacy of others.
      #2) Think before you type.
      #3) With great power comes great responsibility.

      [sudo] password for banner: 
      [banner@stapp03 ~]$
      ```

20. The Nautilus production support team and security team had a meeting last month in which they decided to use local yum repositories for maintaing packages needed for their servers. For now they have decided to configure a local yum repo on Nautilus Backup Server. This is one of the pending items from last month, so please configure a local yum repository on Nautilus Backup Server as per details given below.


      a. We have some packages already present at location /packages/downloaded_rpms/ on Nautilus Backup Server.

      b. Create a yum repo named localyum and make sure to set Repository ID to localyum. Configure it to use package's location /packages/downloaded_rpms/.

      c. Install package wget from this newly created repo.


      ```
      [root@stbkp01 yum.repos.d]# cat localyum.repo 
      [localyum]
      name=Local Yum Repository
      baseurl=file:///packages/downloaded_rpms/
      enabled=1
      gpgcheck=0

      [root@stbkp01 yum.repos.d]#
      ```

      ```
      yum clean metadata
      ``` 

      ```
      yum repolist
      ```

      ```
      yum install wget --enablerepo=localyum
      ```

21. During a recent security audit, the application security team of xFusionCorp Industries found security issues with the Apache web server on Nautilus App Server 2 server in Stratos DC. They have listed several security issues that need to be fixed on this server. Please apply the security settings below:


      a. On Nautilus App Server 2 it was identified that the Apache web server is exposing the version number. Ensure this server has the appropriate settings to hide the version number of the Apache web server.

      b. There is a website hosted under /var/www/html/official on App Server 2. It was detected that the directory /official lists all of its contents while browsing the URL. Disable the directory browser listing in Apache config.

      c. Also make sure to restart the Apache service after making the changes.

      Add "ServerTokens Prod" to /etc/httpd/conf/httpd.conf


      ```
         <Directory "/var/www/html">
            #
            # Possible values for the Options directive are "None", "All",
            # or any combination of:
            #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
            #
            # Note that "MultiViews" must be named *explicitly* --- "Options All"
            # doesn't give it to you.
            #
            # The Options directive is both complicated and important.  Please see
            # http://httpd.apache.org/docs/2.4/mod/core.html#options
            # for more information.
            #
            Options -Indexes 

            #
            # AllowOverride controls what directives may be placed in .htaccess files.
            # It can be "All", "None", or any combination of the keywords:
            #   Options FileInfo AuthConfig Limit
            #
            AllowOverride None

            #
            # Controls who can get stuff from this server.
            #
            Require all granted
         </Directory>
      ```