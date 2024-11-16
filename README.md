# devops-assessment

# MariaDB and phpMyAdmin Helm Chart

This Helm chart deploys MariaDB with phpMyAdmin on a Kubernetes cluster, allowing easy management of the MariaDB database via a web-based phpMyAdmin interface.

## Prerequisites
- Helm 3.x
- A Kubernetes cluster
- An Ingress controller (if enabling Ingress for phpMyAdmin)
- [cert-manager](https://cert-manager.io/docs/) (optional, for TLS support)
- [metric-server](https://github.com/kubernetes-sigs/metrics-server#deployment) (optional, use with HPA)


## Installation
```bash
git clone https://github.com/nhatsangvn/devops-assessment.git
cd devops-assessment
helm upgrade -i  mariadb-phpmyadmin mariadb-phpmyadmin/
```


## Chart Values
The `values.yaml` file is configured with the following sections:


### MariaDB Configuration
| Parameter                   | Description                                   | Default          |
|-----------------------------|-----------------------------------------------|------------------|
| `mariadb.enabled`           | Enables or disables MariaDB deployment       | `true`           |
| `mariadb.image.repository`  | Docker image for MariaDB                     | `mariadb`        |
| `mariadb.image.tag`         | MariaDB image tag                            | `latest`         |
| `mariadb.rootPassword`      | Root password for MariaDB                    | `veryS3c3u!@#`   |
| `mariadb.database`          | Name of the initial database                 | `sang-test`      |
| `mariadb.user`              | Database user                                | `sangnn`         |
| `mariadb.password`          | Password for the specified user              | `sangnn@123`     |
| `mariadb.config`            | Custom MariaDB configurations (ConfigMap)    | `{}`             |
| `mariadb.config_files`      | Custom `.cnf` files for MariaDB (ConfigMap)  | `{}`             |
| `mariadb.backup.enabled`                                  | Enables or disables automatic backups                     | `false`               |
| `mariadb.backup.schedule`                                 | Cron schedule for the backup job                          | `"* * * * *"`         |
| `mariadb.backup.backup_mariadb_user`                      | MariaDB user for the backup process                       | `"mariadb-backup-user"` |
| `mariadb.backup.backup_mariadb_password`                  | Password for the backup MariaDB user                      | `"mariadb-backup-password"` |
| `mariadb.backup.storage.type`                             | Storage type for backups                                  | `"s3"`                |
| `mariadb.backup.storage.s3.access_id`                     | Access key ID for S3                                      | `"your-access-id"`    |
| `mariadb.backup.storage.s3.secret_key`                    | Secret access key for S3                                  | `"your-secret-key"`   |
| `mariadb.backup.storage.s3.region`                        | S3 region where the bucket is located                     | `"ap-southeast-1"`    |
| `mariadb.backup.storage.s3.bucket`                        | S3 bucket name for storing backups                        | `"my-backup-bucket"`  |
| `mariadb.backup.storage.s3.path`                          | Path within the bucket to store backups                   | `"backup-directory"`  |
| `mariadb.backup.storage.s3.endpoint_override`             | Custom endpoint for non-AWS S3-compatible storage         | `""`                  |

### phpMyAdmin Configuration
| Parameter                             | Description                                         | Default               |
|---------------------------------------|-----------------------------------------------------|-----------------------|
| `phpmyadmin.enabled`                  | Enables or disables phpMyAdmin deployment           | `true`                |
| `phpmyadmin.image.repository`         | Docker image for phpMyAdmin                         | `phpmyadmin`          |
| `phpmyadmin.image.tag`                | phpMyAdmin image tag                                | `latest`              |
| `phpmyadmin.service.type`             | Kubernetes service type                             | `ClusterIP`           |
| `phpmyadmin.ingress.enabled`          | Enables or disables Ingress                         | `true`                |
| `phpmyadmin.ingress.hostname`         | Hostname for phpMyAdmin Ingress                     | `phpmyadmin.example.com` |
| `phpmyadmin.ingress.annotations`      | Annotations for the Ingress resource                | `{}`                  |
| `phpmyadmin.ingress.tls.enabled`      | Enables TLS for Ingress                             | `true`                |
| `phpmyadmin.ingress.tls.secretName`   | Secret name for TLS certificate                     | `phpmyadmin-tls`      |
| `phpmyadmin.resources.requests.cpu`   | CPU request for phpMyAdmin                          | `50m`                 |
| `phpmyadmin.resources.requests.memory`| Memory request for phpMyAdmin                       | `50Mi`                |
| `phpmyadmin.resources.limits.cpu`     | CPU limit for phpMyAdmin                            | `200m`                |
| `phpmyadmin.resources.limits.memory`  | Memory limit for phpMyAdmin                         | `200Mi`               |
| `phpmyadmin.hpa.enabled`              | Enables Horizontal Pod Autoscaler                   | `false`                |
| `phpmyadmin.hpa.minReplicas`          | Minimum replicas for phpMyAdmin                     | `2`                   |
| `phpmyadmin.hpa.maxReplicas`          | Maximum replicas for phpMyAdmin                     | `5`                   |
| `phpmyadmin.hpa.averageUtilizationCPU`| Target CPU utilization for autoscaling              | `50`                  |
| `phpmyadmin.hpa.averageUtilizationMemory` | Target memory utilization for autoscaling        | `80`                  |


## Usage
Once deployed, you can access phpMyAdmin using the configured Ingress hostname. If TLS is enabled, ensure that cert-manager is configured to issue the certificate specified in `phpmyadmin.ingress.tls.secretName`.


### Access phpMyAdmin
```plaintext
https://<phpmyadmin-hostname>/
```

Use the MariaDB credentials set in `values.yaml` to log into phpMyAdmin.


## Upgrading the Chart
To upgrade the chart:

```bash
vim mariadb-phpmyadmin/values.yaml
helm upgrade -i  mariadb-phpmyadmin mariadb-phpmyadmin/
```

This will apply any updated configuration or version changes.

## Uninstallation
To uninstall the `mariadb-phpmyadmin` release:

```bash
helm uninstall mariadb-phpmyadmin
```

This will remove all resources associated with the release.