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