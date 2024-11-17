# devops-assessment

# MariaDB and phpMyAdmin Helm Chart

This Helm chart deploys MariaDB with phpMyAdmin on a Kubernetes cluster, allowing easy management of the MariaDB database via a web-based phpMyAdmin interface.

## Prerequisites
- Helm 3.x
- A Kubernetes cluster
- An Ingress controller (if enabling Ingress for phpMyAdmin)
- [cert-manager](https://cert-manager.io/docs/) (optional, for TLS support)
- [metric-server](https://github.com/kubernetes-sigs/metrics-server#deployment) (optional, use with HPA)
- If enable backup: need a readonly user and an service account for S3 backup storage
- If enable monitoring: need a Prometheus and Grafana installed with proper configuration

## Installation
```bash
helm upgrade -i mariadb-phpmyadmin oci://ghcr.io/nhatsangvn/mariadb-phpmyadmin -n mariadb --create-namespace
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
| `exporter.enabled`       | Boolean | `false`                 | Set to `true` to enable the MySQL exporter.                                                                             |
| `exporter.image.repository` | String  | `prom/mysqld-exporter`  | The Docker repository for the MySQL exporter image.                                                                     |
| `exporter.image.tag`     | String  | `latest`                | The tag/version of the MySQL exporter image.                                                                            |
| `exporter.resources.requests` | Object  | `{cpu: "50m", memory: "50Mi"}` | CPU and memory resource requests for the MySQL exporter container.                                                      |
| `exporter.resources.limits` | Object  | `{cpu: "200m", memory: "200Mi"}` | CPU and memory resource limits for the MySQL exporter container.                                                        |
| `exporter.metricsPort`   | Integer | `9104`                  | The port on which the MySQL exporter exposes metrics.                                                                   |
| `exporter.initUser`      | Boolean | `true`                  | Automatically creates an exporter user (`mysqlUser`) during MariaDB container startup. Set to `false` to use a custom user. |
| `exporter.mysqlUser`     | String  | `exporter`              | The username for the MySQL exporter. If you specify a custom user, ensure `initUser` is set to `false`.                 |
| `exporter.mysqlPassword` | String  | `Aa@1234567`            | The password for the MySQL exporter. If you specify a custom password, ensure `initUser` is set to `false`.             |

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

### Enable Mariadb auto-backup:
Before proceeding the backup, we need two credentials:
1. A readonly service user in Mariadb, which is able to dump all the databases
2. A access_id and secret_key for any S3 compatible platforms (currently only S3 is supported)
We can enable auto backup all database via these configuration in values.yaml:
```
mariadb:
  ...
  backup:
    enabled: true
```
The other fields below `backup` section are straigh-forward. For example I'm using S3 with backup.storage.s3.path is "sangnn-test", then the result will look like this:
![Backup via S3](/static/s3_backup_sangnn.png)

> [!TIP]
> If you are not using S3 but another S3-compatible platform, you need to specify the endpoint explicitly via mariadb.backup.storage.s3.endpoint_override

### Enable Mariadb monitoring:
In order to enable monitoring our Mariadb instance, we need to enable the mariadb.expoter.enabled key in the values.yaml:
```
mariadb:
  ...
  exporter:
    enabled: true
    metricsPort: 9104
    ...
    initUser: true              # will help create exporter user at start of the Mariadb container
    mysqlUser: exporter         # if you specify yours, set `initUser: false`
    mysqlPassword: Aa@1234567   # same as above
```
> [!IMPORTANT]  
> The exporter user should have adequate permissions. Below is the reference SQL commands to create the exporter user:
>```
>CREATE USER 'my_exporter_user'@'%' IDENTIFIED BY 'xxx' WITH MAX_USER_CONNECTIONS 3;
>GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'my_exporter_user'@'%';
>GRANT SLAVE MONITOR ON *.* TO 'my_exporter_user'@'%';
>```


By default, this exporter works with Prometheus. If you didn't have any Prometheus source and Grafana, can use the below script for quickly setup these instances:
```
### Setup basic Prometheus with service auto-discovery
cat > /tmp/prometheus.yaml <<EOF
server:
  global:
    scrape_interval: 15s
  persistentVolume:
    enabled: false
  extraScrapeConfigs:
    - job_name: "kubernetes-service-endpoints"
      kubernetes_sd_configs:
        - role: endpoints
      relabel_configs:
        - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_port]
          action: replace
          target_label: __address__
          regex: (.+)
          replacement: $1

alertmanager:
  enabled: false

pushgateway
  enabled: false
EOF

helm upgrade -i prometheus prometheus-community/prometheus \
  --namespace monitoring \
  --create-namespace \
  -f /tmp/prometheus.yaml


### Setup basic Grafana
cat > /tmp/grafana.yaml <<EOF
adminUser: admin
adminPassword: admin@grafana

datasources:
  datasources.yaml:
    apiVersion: 1
    datasources:
      - name: Prometheus
        type: prometheus
        access: proxy
        url: http://prometheus-server.monitoring
        isDefault: true
EOF

helm install grafana grafana/grafana \
  --namespace monitoring \
  --create-namespace \
  -f /tmp/grafana.yaml

kubectl port-forward -n monitoring --address localhost svc/grafana 8085:80 
```
After created them, you can now go to browser http://localhost:8085, then enter user/pass:  `admin/admin@grafana` in order to login to Grafana. On the default Grafana homepage click **Dashboards** > **New** > **Import**, then you would see something like this:
![Grafana Import](/static/grafana_import.png)
Copy the content in this [json file](/static/mysql-overview.json) and Paste to the 
import field. Click **Load** > **Import**.
> [!NOTE]  
> This is a default dashboard with lots of field that we may not need, we need to tune it gradually.


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