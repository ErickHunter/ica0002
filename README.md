# ica0002-2023

# Lab Notes

## Introduction

## Table of Contents

## Lab 3 testing services that should be in started state and enabled.

Example task for uWSGI:

    name: uWSGI service
    ansible.builtin.service:
      name: uwsgi
      state: started
      enabled: true

**Note: every service you set up later on this course should have similar task in the role!**

Test if Ansible will actually start the uWSGI service if that was stopped, first stop the service
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




If you ever get the error ModuleNotFoundError: No module named 'MySQLdb' check lab 4 readme and search the error.