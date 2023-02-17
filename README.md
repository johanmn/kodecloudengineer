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

   ```
   [root@stmail01 postfix]# service postfix status -l
Redirecting to /bin/systemctl status  -l postfix.service
● postfix.service - Postfix Mail Transport Agent
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
```

As we can see from the ouput above, the error "fatal: parameter inet_interfaces: no local interface found for ::1" is refering 
to the entry in /etc/hosts file which had "::1" defined for 127.0.0.1

To solve the issue the "::1" was commented from /etc/hosts file and the postfix service was restarted.

   