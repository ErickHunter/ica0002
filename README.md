# ica0002-2023
## Introduction

##### Table of Contents  
[MySQL](##MySQL)  

## Lab 3 testing services that should be in started state and enabled.

Test if Ansible will actually start the a service if that was stopped, first stop the service
manually -- run these commands on the managed host:

    sudo service uwsgi stop
    service uwsgi status

The second command output should indicate that the service is stopped, example:

    ● uwsgi.service - LSB: Start/stop uWSGI server instance(s)
         Loaded: loaded (/etc/init.d/uwsgi; generated)
         Active: inactive (dead) since Sun 2023-09-17 11:13:16 UTC; 14ms ago
                 ^^^^^^^^^^^^^^^


Then, run the Ansible on the controller node:

    ansible-playbook infra.yaml

Ansible logs should indicate the change of the task that manages the service:

    TASK [uwsgi : uWSGI service] ********
    changed: [elvis-1]

Run this command on the managed host to ensure that the service was started:

    service uwsgi status

This should now indicate that the service is running, example:

    ● uwsgi.service - LSB: Start/stop uWSGI server instance(s)
         Loaded: loaded (/etc/init.d/uwsgi; generated)
         Active: active (running) since Sun 2023-09-17 11:15:21 UTC; 55s ago
                 ^^^^^^^^^^^^^^^

Procedure to test if Ansible will enable the uWSGI service to start on the system boot is similar.
First, disable the service and reboot the instance --
**make sure to run this on the managed host, not elsewhere**:

    sudo systemctl disable uwsgi
    sudo reboot

Wait until the virtual machine reboots (usually 1..2 minutes), connect via SSH and verify that the
uWSGI service is _not_ running:

    service uwsgi status

Run the Ansible on the controller node -- this should start the service on the managed host, and
also re-enable the service to run on the next system boot:

    ansible-playbook infra.yaml

On the managed host, ensure that the service was started; if it was, reboot the machine again:

    service uwsgi status
    sudo reboot

Wait until virtual machine reboots, connect via SSH and verify that the uWSGI service is running:

    service uwsgi status

**Note: from now on, verify every managed service this way at least once!**
Checked Services
- [ ] nginx
- [ ] agama *is this even a service?*
- [ ] uwsgi
- [ ] MySql
- [ ] bind9
- [ ] prometheus
- [ ] node exporter




## MySQL
If you ever get the error ModuleNotFoundError: No module named 'MySQLdb' check lab 4 readme and search the error.

## DNS and BIND
Some useful things for trobleshooting DNS.
```nslookup domain.com``` =Tells you what dns server you useing and what the ip of the lookup is. Also checks if your resolv.conf is setup to "search" in a patucular domain. IE ping vm-1 will be translated to ping vm-1.bogmoster.eh. 
```tail -f /var/log/syslog``` BIND logs
```named-checkconf``` config validity check
```named-checkzone``` zone file check, be used to used fqdn like me.bogmonster.eh.
```rndc reload``` to reload when zone file is chagned, but somethimes a full ```sudo service bind9 restart``` is needed.
 ```sudo service bind9 restart``` is needed when a config is changed as usual. 
FYI end result at lab 5 should be DNS server is installed and configured on one VM and DNS settings on all VMs are set to use local DNS server on that first vm.
