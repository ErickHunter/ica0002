# Backup Services Service Level Agreement (SLA)

## 1. Introduction
This SLA delineates the service level and the approach employed for safeguarding the data and configuration of the provided services.

## Services 
- **Web Services**: Nginx, Agama
- **Databases**: MySQL, InfluxDB
- **DNS Services**: Bind9
- **Monitoring Services**: Grafana, Prometheus, Pinger, Telegraf, and all metric exporters.
- **Backup Services**: Backup scripts and Duplicity
- **Load Balancing Services**: HAproxy and Keepalived
- **Containerization Services**: Docker
- **Project Repository**: Ansible playbook and roles to configure all services

## 2. Backup Coverage
- The Backup Services will encompass databases.
- Temporary files, cache files, or any other non-specified services are excluded from the backup services.
- Services not covered by the backup services are reinstated through the `infra.yaml` playbook in the project repository.

### Databases
- **MySQL**: The "AGAMA" database is covered, while all other databases are not.
- **InfluxDB**: All databases, except for the "latency" database, are backed up (with the determination that the value of the data is lower than the cost to back up).
- **Grafana**: The database required by Grafana for storing its configuration data is not backed up, as all configurations are provisioned via Ansible and mounted to the Grafana Docker container.

### Other Services and Project Repository
- All other services and their configurations are covered by an Ansible playbook in the project repository.
- The Ansible playbook for deployment and restoration is stored in the GitHub repo.
- A copy of this repository is downloaded every Thursday and Tuesday via GitHub's API for user or organization migrations and then stored in Azure Blob storage.

## 3. Recovery Point Objective (RPO)
- The Recovery Point Objective (RPO) is set at 24 hours. In the event of data loss, the service provider should restore data or infrastructure up to 24 hours before the failure occurred.

## 4. Versioning and Retention
- Backup retention time is one month, with the oldest backups deleted on the first Tuesday of each month at 21:00 (UTC)/23:00 (UTC+2 Tallinn).
- Only three backup versions are retained. The oldest backup is deleted on the first Tuesday of each month, 24 hours after the first full backup of the month.
- A full backup is made every Monday at 21:00 (UTC)/23:00 (UTC+2 Tallinn).
- An incremental backup is made every other day at 21:00 (UTC)/23:00 (UTC+2 Tallinn).
- These times were chosen for their alignment with low-traffic periods in our service region. Some backups may set the databases to read-only mode.
- There is a 24-hour period where four backups are kept, intentionally done in case of backup failure. There should never be fewer than three versions at any time.

## 5. Usability Checks
- The service provider will conduct regular usability checks to ensure that the backed-up data can be successfully restored within the RPO.
- Restoration tests will be conducted on a monthly basis.

## 6. Restoration Criteria
- Restoration is only initiated in the event of confirmed data loss or service loss for which other methods of recovery do not exist.

## 7. Recovery Time Objective (RTO)
- The Recovery Time Objective (RTO) is set at 8 hours. In the event of data loss, the service provider should restore the data within 8 hours of the loss being reported.
