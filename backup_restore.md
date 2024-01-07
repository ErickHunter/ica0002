# Applicaion and Services Restoration

All services can be restored with the ansible playbook. "infra.yaml"<br>
The playbook can be run with Asible version 2.13.11 from the control node.
To check your version, run ```ansible --version```. The output should include ```ansible [core 2.13.11]```.

To re-deploy the infrastructure with Ansible, form the control node run:

   ``` ansible-playbook infra.yaml ```
  

If you have identified only one service that needs to be restored, you can reference "infra.yaml" and use its tag.
When using this method, always include the tag "init" to gather facts.

``` ansible-playbook infra.yaml --tags 'tag1, tag2'  ```

Here are some tags that can be used.
``` agama, webservers, internal_vm, backup, mysql, ```

# Database restoration

All databases can be restored via the user ```backup```. This user exists on all managed nodes.

### MYSQL Agama Database
To download the backup from the backup server, run this on the managed node where the MySQL database needs to be restored.
Run commands as "ubuntu" user.
```sudo -u backup duplicity --no-encryption restore rsync://ErickHunter@backup.bogmonster.eh/mysql /home/backup/restore/mysql```

Then run this command to restore the "agama" database.
```mysql agama < /home/backup/restore/agama.sql```

If you get permission denied to read the file. do
```sudo chmod +rx /home/backup/
sudo chmod +rx /home/backup/restore/
sudo chmod +rx /home/backup/restore/mysql/```
Then maybe
```sudo chmod 707 /home/backup/restore/mysql/agama.sql```
This is becuase "ubuntu" user needs access to the file and and parent dir.


You can check the "agama" database.
```mysql -e 'SELECT * FROM agama.item';```
OR, you can go to the agama web app and check there if you wish.


### Influxdb Metrics and Logs Database
To download the backup from the backup server, run this on the Influxdb host.
```sudo -u backup duplicity --no-encryption restore rsync://ErickHunter@backup.bogmonster.eh/influxdb /home/backup/restore/influxdb```

You must stop telegraf and dump the "telegraf" influx database before restoring.<br>
```
service telegraf stop
influx -execute 'DROP DATABASE telegraf'
```
Then you can restore the database with.<br>
```
influxd restore -portable -database telegraf /home/backup/restore/influxdb
```
Then run the playbook on the control node to restart the telegraf service agents<br>
```
ansible-playbook infra.yaml
```

### Known Issues
Errors like this are a known bug and can be ignored.
``` error updating meta: DB metadata not changed. Database may already exist
restore: DB metadata not changed. Database may already exist