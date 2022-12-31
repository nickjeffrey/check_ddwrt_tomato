# check_ddwrt_tomato
nagios check for routers running DD-WRT or Tomato firmware

This is a nagios script that will SSH into a router # running DD-WRT or Tomato firmware and perform the following checks: 
- name resolution exists for router
- remote (via WAN port) HTTP management is disabled
- remote (via WAN port) SSH  management is enabled
- cron daemon is enabled 
- a daily reboot is scheduled between midnight-8am
- telnet daemon is disabled
- ssh    daemon is enabled 
- syslog daemon is enabled and logging to a remote host
- ntp daemon is enabled and syncing time against a remote host
- snmp daemon is enabled (so we can track traffic levels through ethernet interfaces)
- CPU load
- memory utilization
- type of wireless encryption in use
- number of connected wireless clients
- WAN-side IP address and default gateway exists
- WAN-side default gateway is pingable (means we have an Internet connection to upstream router)
- primary and secondary DNS servers are defined
- name resolution tests against primary and secondary DNS servers


# Requirements
perl, SSH key pair authentication between nagios server and router

# SSH key pair setup on nagios server
It is assumed you already have SSH public/private keys created for the nagios userid on the nagios server.  If not, create with:
```
   su - nagios
   ssh-keygen -t rsa
```
# SSH key pair setup on DD-WRT routers
- click the "Services" tab
- click the "Services" sub-tab
- scroll down to the "Secure Shell" section
- paste the contents of $HOME/.ssh/id_dsa.pub into the "Authorized Keys" section
- click "Apply Settings"
- confirm you can ssh from the nagios server into the router without a password:  ssh root@router1.example.com

# SSH key pair setup on Tomato routers
- click the "Admin" tab
- scroll down to the "Secure Shell" section
- paste the contents of $HOME/.ssh/id_dsa.pub into the "Authorized Keys" section
- click "Save"
- confirm you can ssh from the nagios server into the router without a password: ssh root@router1.example.com


# Usage
Add a section to the services.cfg file on the nagios server similar to the following:
```
    define service {
       use                             generic-service
       host_name                       router1.example.com,router2.example.com
       service_description             DD-WRT / Tomato checks
       check_command                   check_ddwrt_tomato!22
       }
```

Add a section to the commands.cfg file on the nagios server similar to the following:
```
    # 'check_ddwrt_tomato' command definition
    define command {
       command_name    check_ddwrt_tomato
       command_line    $USER1$/check_ddwrt_tomato $HOSTADDRESS$ $ARG1$
       }
```


# Output
```
DD-WRT/Tomato checks OK. cpuload=1% memused=18% crond=enabled snmpd=enabled telnetd=disabled sshd=enabled sshd_port=2222 sshd_wanport=2222 wireless_clients=0 encryption=AES wan_ipaddr=198.18.18.45 wan_gateway=198.18.18.46 DNS=192.168.99.41,192.168.99.42 syslog_host=192.168.99.9 ntp_server=0.north-america.pool.ntp.org 1.north-america.pool.ntp.org 2.north-america.pool.ntp.org 
```

