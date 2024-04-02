---
copyright:
  years: 2021, 2024
lastupdated: "2024-04-02"

keyowrds: redis, databases, upgrading, major versions, changing versions

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Upgrading to a new major version
{: #upgrading}

When a major version of a database is at its end of life (EOL), upgrade to the next available major version. Find the available versions of Redis in the [{{site.data.keyword.databases-for-redis_full}} catalog](https://cloud.ibm.com/catalog/databases-for-redis){: external}, with the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployables-show){: external}, or through the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#listdeployables-permissions){: external}. 

## Simple upgrade path
{: #upgrading-simple}

Because most uses of {{site.data.keyword.databases-for-redis}} serve as a cache where the data tends to be transient, it is best to create a new deployment. In this simple method, provision a new deployment using the latest version of {{site.data.keyword.databases-for-redis}} and then point your application to the new deployment. This builds up the cache directly through initial use.

## How to upgrade to a new major version
{: #upgrading_major}

Prepare to run on, and then migrate to, the latest version before the EOL date. For more information, see [Versioning Policy](/docs/cloud-databases?topic=cloud-databases-versioning-policy){: external}.

Rolling back versions is not supported.
{: .note}

Upgrade to the latest version of Redis available to {{site.data.keyword.databases-for-redis}}. Find the latest version from the catalog page, from the {{site.data.keyword.databases-for}} CLI plug-in command [`ibmcloud cdb deployables-show`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployables-show){: external}, or from the {{site.data.keyword.databases-for}} API [`/deployables`](https://cloud.ibm.com/apidocs/cloud-databases-api#get-all-deployable-databases){: external} endpoint.

Upgrading is handled by [restoring a backup](/docs/cloud-databases?topic=cloud-databases-dashboard-backups){: external} of your data into a new deployment. Restoring from a backup has various advantages:

- The original database stays running and production work can be uninterrupted.
- You can test the new database out of production and act on any application incompatibilities.
- The entire process can be rerun at any point.
- A fresh restoration reduces the likelihood that unneeded artifacts of the older version of the database are carried over to the new database.

### Upgrade paths
{: #upgrading-paths}

| Current Version |	Major Version Upgrade Path |
| ---- | ----- |
| {{site.data.keyword.databases-for-redis}} ??  |	-> {{site.data.keyword.databases-for-redis}} ?? |
| {{site.data.keyword.databases-for-redis}} ?? |	-> {{site.data.keyword.databases-for-redis}} ?? |
| {{site.data.keyword.databases-for-redis}} ?? |	-> Latest version |
{: caption="Table 1. Major version upgrade paths" caption-side="top"}

### Upgrading using the UI
{: #upgrading-ui}
{: ui}

You can upgrade to a new version by [restoring a backup](/docs/cloud-databases?topic=cloud-databases-dashboard-backups&interface=ui#restore-backup) from the _Backups_ tab of your _Deployment Overview_. Click **Restore** on a backup to bring up a dialog box where you can change some options for the new deployment. One of them is the database version, which is auto-populated with the versions available for you to upgrade to. Select a version and click **Restore** to start the provision and restore process.

### Upgrading using the CLI
{: #upgrading-cli}
{: cli}

When you upgrade and restore from backup through the {{site.data.keyword.cloud_notm}} CLI, use the provisioning command from the resource controller.

```sh
ibmcloud resource service-instance-create <service-name> <service-id> <service-plan-id> <region>
```
{: pre}

The parameters `service-name`, `service-id`, `service-plan-id`, and `region` are all required. You also supply the `-p` with the version and backup ID parameters in a JSON object. The new deployment is automatically sized with the same disk and memory as the source deployment at the time of the backup.

```sh
ibmcloud resource service-instance-create example-upgrade databases-for-redis standard us-south \
-p \ '{
  "backup_id": "crn:v1:bluemix:public:databases-for-redis:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
  "version":"5.0"
}'
```
{: pre}

### Upgrading using the API
{: #upgrading-api}
{: api}

Similar to provisioning through the API, you need to complete [the required steps to use the resource controller API](/docs/databases-for-redis?topic=databases-for-redis-provisioning){: external} before you can use it to upgrade from a backup. Then, send the API a POST request. The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required. You also supply the version and backup ID. The new deployment has the same memory and disk allocation as the source deployment at the time of the backup.

```sh
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "my-instance",
    "target": "bluemix-us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-redis-standard",
    "backup_id": "crn:v1:bluemix:public:databases-for-redis:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
    "version":"5.0"
  }'
```
{: pre}


## Managing {{site.data.keyword.databases-for}} backups 
{: #dashboard-backups}

Backups for {{site.data.keyword.databases-for}} instances are accessible from the _Backups_ tab of your instance's dashboard. Here is some additional general information about backups:

- Automatic backups are performed daily and kept with a simple retention schedule of 30 days.
- Backups cannot be deleted.
- If you delete your instance, its backups are deleted automatically.
- Daily backup scheduling is not configurable.
- Backups are restorable to other regions, except for `eu-de`, `eu-es`, and `par-01`, which can restore backups only between each other. For example, `par-01` backups can be restored to and between `eu-de` and `eu-es`.
- Backup storage is encrypted. To manage the encryption keys, see [Key Protect integration](/docs/cloud-databases?topic=cloud-databases-key-protect#byok-for-backups). Otherwise, backups are encrypted with a key that is automatically generated for your instance.
- Backups are restorable across accounts, but only through the API and only if the user that is running the restore has access to both the source and destination accounts.
- {{site.data.keyword.databases-for}} backups are not downloadable. If you need a local backup, use the appropriate software. For example, [pg_dump](https://www.postgresql.org/docs/9.6/static/backup-dump.html){: .external} is an effective tool for managing PostgreSQL backups.
- {{site.data.keyword.databases-for-cassandra_full}} does not support reenablement. After an instance is disabled, that instance must be restored from a backup.

For information on taking an on-demand backup, see [Taking an on-demand backup](/docs/cloud-databases?topic=cloud-databases-dashboard-backups&interface=cli#ondemand-backup).
{: tip}

### Backups in the UI
{: #backup-ui}
{: ui}

The backup types have their respective tabs, either _On-demand_ or _Automatic_. Each backup is listed with its type and when the backup was taken. Click the timestamp to change its format between elapsed time, local time, and Coordinated Universal Time.

![List of backups on the Backups tab](images/backups-list.png){: caption="Figure 1. List of backups on the Backups tab" caption-side="bottom"}

Click the backup to reveal information for that specific backup, including its full ID. A **Restore** button, or pre-formatted CLI command, is there for restore options.

### Backups in the CLI
{: #backup-ui-cli}
{: cli}

You can access the list of backups and individual backup information from the {{site.data.keyword.databases-for}} CLI plug-in and the {{site.data.keyword.databases-for}} API.

Use the [`cdb deployment-backups-list`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-backups-list) command to view the list of all available backups for your instance. To get details about a specific backup, use the [`cdb backup-show`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#backup-show) command.

For example, to view the backups for an instance named "example-instance", use the following command:

```sh
ibmcloud cdb deployment-backups-list example-instance
```
{: .pre}

To see the details of one of the backups from the list, take the ID from the `ID` field of the `deployment-backups-list` response and use it with the `backup-show` command:

```sh
ibmcloud cdb backup-show crn:v1:staging:public:cloud-databases:us-south:a/6284014dd5b487c87a716f48aeeaf99f:3b4537bf-a585-4594-8262-2b1e24e2701e:backup:a3364821-d061-413f-a0df-6ba0e2951566
```
{: .pre}

### Backups in the {{site.data.keyword.databases-for}} API
{: #backup-ui-api}
{: api}

For backups information in the {{site.data.keyword.databases-for}} API, use the [`/deployments/{id}/backups`](https://cloud.ibm.com/apidocs/cloud-databases-api#get-currently-available-backups-from-a-deployment) endpoint to list the instance's backups. To get information about a specific backup, use the [`/backups/{backup_id}`](https://{DomainName}/apidocs/cloud-databases-api#get-information-about-a-backup) endpoint.

### Taking an on-demand backup
{: #ondemand-backup}
{: cli}

If you plan to make major changes to your instance, like scaling or removing databases, tables, collections, on-demand backups are useful. It can also be useful if you need to back up on a schedule. On-demand backups are kept for 30 days.

Instances come with backup storage equal to their total disk space at no cost. If your backup storage usage is greater than total disk space, each gigabyte is charged at an overage of $0.03/month. Backups are compressed, so even if you use on-demand backups, most instances do not exceed the allotted credit.
{: .tip}

To create a manual backup in the UI, go to the _Backups_ tab of your instance then click **Back up now**. A message is displayed that a backup is in progress, and an on-demand backup is added to the list of available backups.

In the CLI, an on-demand backup is triggered with the [`cdb deployment-backup-now`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-backup-now) command.
```sh
ibmcloud cdb deployment-backup-now example-deployment
```
{: .pre}

In the API, sending a POST to the [`/deployments/{id}/backups`](https://cloud.ibm.com/apidocs/cloud-databases-api#initiate-an-on-demand-backup) endpoint triggers an on-demand backup.

### Restoring a backup
{: #restore-backup}

Backups are restored to a new instance. After the new instance finishes provisioning, your data in the backup file is restored into the new instance.

By default, the new instance is auto-sized to the same disk and memory allocation as the source instance at the time of the backup from which you are restoring. To adjust the resources that are allocated to the new instance, use the optional fields in the UI, CLI, or API to resize the new instance. Be sure to allocate enough for your data and workload; if the instance is not given enough resources, the restore fails.

Do not delete the source instance while the backup is restoring. Before you delete the old instance, wait until the new instance is provisioned and the backup is restored. Deleting an instance also deletes its backups.
{: .tip}

#### Restoring a backup in the UI
{: #restore-backup-ui}
{: ui}

To restore a backup to a new service instance,

1. Click in the corresponding row to expand the options for the backup that you want to restore.
2. Click **Restore**.
3. Use the dialog box to select from some available options.
    - The new instance is automatically named `<name>-restore-[timestamp]`, but you can rename it.
    - You can also select the region where the new instance is located. Cross-region restores are supported, except for restoring into or out of the `eu-de` region.
    - You can choose the initial resource allocation, either to expand or shrink the resources on the new instance. You can also enable or disable dedicated cores.
4. Click **Restore**. A "restore from backup started" message appears. Clicking **Your new instance is available now.** takes you to your _Resources List_.

#### Restoring a backup in the CLI
{: #restore-backup-cli}
{: cli}

The Resource Controller supports provisioning of database instances, and provisioning and restoring are the responsibility of the Resource Controller CLI. Use the [`resource service-instance-create`](/docs/cli?topic=cli-ibmcloud_commands_resource#ibmcloud_resource_service_instance_create) command.

```sh
ibmcloud resource service-instance-create <SERVICE_INSTANCE_NAME> <service-id> standard <region> -p '{"backup_id":"BACKUP_ID"}'
```
{: .pre}

Change the value of `SERVICE_INSTANCE_NAME` to the name that you want for your new instance. The `service-id` is the type of instance, such as `databases-for-postgresql` or `messages-for-rabbitmq`. The `region` is where you want the new instance to be located, which can be a different region from the source instance. Cross-region restores are supported, except for restoring to or from `eu-de` by using another region. `BACKUP_ID` is the backup that you want to restore.

Optional parameters are available using the CLI. Use them if you need to customize resources, or use a Key Protect key for BYOK encryption on the new instance.
```sh
ibmcloud resource service-instance-create <SERVICE_INSTANCE_NAME> <service-id> standard <region> -p
'{"backup_id":"BACKUP_ID","key_protect_key":"KEY_PROTECT_KEY_CRN", "members_disk_allocation_mb":"DESIRED_DISK_IN_MB", "members_memory_allocation_mb":"DESIRED_MEMORY_IN_MB", "members_cpu_allocation_count":"NUMBER_OF_CORES"}'
```
{: .pre}

A pre-formatted command for a specific backup is available in detailed view of the backup on the _Backups_ tab of your instance's dashboard.
{: .tip}

#### Restoring a backup using the API
{: #restore-backup-api}
{: api}

The [Resource Controller API](https://cloud.ibm.com/apidocs/resource-controller/resource-controller#create-resource-instance){: external} supports provisioning and restoring database instances. The create request is a `POST` to the [`/resource_instances`](https://{DomainName}/apidocs/resource-controller#create-provision-a-new-resource-instance) endpoint.

```sh
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "<SERVICE_INSTANCE_NAME>",
    "target": "<region>",
    "resource_group": "<your-resource-group>",
    "resource_plan_id": "<service-id>",
    "parameters":{
      "backup_id": "<backup_id>"
    }
  }'
```
{: .pre}

The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required, and `backup_id` is the backup that you want to restore.
{: important}

The `target` is the region where you want the new instance to be located, which can be a different region from the source instance. Cross-region restores are supported, except for restoring into or out of the `eu-de` region.

If you need to adjust resources or use a Key Protect key, add any of the optional parameters `key_protect_key`, `members_disk_allocation_mb`, `members_memory_allocation_mb`, and `members_cpu_allocation_count`, and their preferred values to the body of the request.


#### Restoring a backup using Terraform
{: #restore-backup-tf}
{: terraform}

Use Terraform to restore to a backup from an older version to a new version.

1. Set your `backup_id`. For more information, see [`backup_id`](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database#backup_id){: external}.
1. Set your `version` in the version attribute. For more information, see [`version`](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database#backup_id){: external}.

The code looks like:

```tf
resource "ibm_database" "<your-instance>" {
  name                                 = "<your_database_name>"
  service                              = "<service>"
  plan                                 = "<plan>"
  location                             = "<region>"
  version                              = "<version>"
  backup_id                            = "<backup_id>"
}
```
{: codeblock}

For more information, see the [{{site.data.keyword.databases-for}} Terraform Registry](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database#backup_id){: external}.

### Backups and restoration
{: #backup-restoration}

* {{site.data.keyword.databases-for}} are not responsible for restoration, timeliness, or validity of said backups.
* Actions that you take as a user can compromise the integrity of backups, such as under-allocating memory and disk. Users can monitor that backups are successful by using the API, and periodically restore a backup to ensure validity and integrity. Users can retrieve the most recent-scheduled backup details from the [{{site.data.keyword.databases-for}} CLI plug-in](#backups-in-the-cli) and the [{{site.data.keyword.databases-for}} API](#restoring-a-backup-through-the-api).
* As a managed service, {{site.data.keyword.databases-for}} monitors the state of your backups and can attempt to remediate when possible. If you encounter issues from which you cannot recover, contact support for more help.

### Backup locations
{: #backup-locations}

Backup location differs per database region. Ensure that the backup region location matches your data location requirements.

| Instance Region | Backup Region |
|----------|---------|
| Dallas | US Cross Regional Object Storage |
| Washington D.C. | US Cross Regional Object Storage |
| London |	EU Cross Regional Object Storage |
| Frankfurt |	EU Cross Regional Object Storage |
| Tokyo	| AP Cross Regional Object Storage |
| Osaka	| AP Cross Regional Object Storage |
| Sydney	| AP Cross Regional Object Storage |
| Toronto |	Montreal Object Storage |
| Chennai |	Chennai Object Storage |
| Sao Paolo | Sao Paolo Object Storage |
| Madrid | EU Cross Regional Object Storage |
{: caption="Table 1. Instance and Backup Regions" caption-side="bottom"}

For more details about {{site.data.keyword.databases-for}} Object Storage locations, review the location's [documentation](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#endpoints-geo).

### Business continuity and disaster
{: #backup-locations}

{{site.data.keyword.databases-for}} provides mechanisms to protect your data and restore service functions. For more information (including [Backup Storage Regions](/docs/cloud-databases?topic=cloud-databases-bc-dr#bc-dr-single-region-backups){: external}, see [Understanding business continuity and disaster recovery for {{site.data.keyword.databases-for}}](/docs/cloud-databases?topic=cloud-databases-bc-dr){: external}.

### Point-in-time recovery
{: #pitr-recovery-options}

With Point-in-Time Recovery (PITR), the instance continuously backs up incrementally and can replay transactions to bring a new instance that is restored from a backup to any point in the last 7 days. {{site.data.keyword.databases-for}} offers Point-In-Time Recovery (PITR) for the following services:

- [{{site.data.keyword.databases-for-mysql_full}}](/docs/databases-for-mysql?topic=databases-for-mysql-pitr)
- [{{site.data.keyword.databases-for-postgresql_full}}](/docs/databases-for-postgresql?topic=databases-for-postgresql-pitr)
- [{{site.data.keyword.databases-for-enterprisedb_full}}](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-pitr&interface=ui)

### Backups FAQ
{: #backup-faq-reference}

For frequently asked questions about backups, check out [Backups FAQ](/docs/cloud-databases?topic=cloud-databases-faq-backups){: external}.


## Backing up and restoring an upgrade
{: #backup-restore}

Upgrade your database version by [restoring a backup](/docs/databases-for-redis?topic=databases-for-redis-dashboard-backups&interface=ui#restore-backup) of your data into a new deployment that is running the new database version.

### Upgrading using the UI
{: #upgrading-ui}
{: ui}

Upgrade to a new version when [restoring a backup](/docs/databases-for-redis?topic=databases-for-redis-dashboard-backups&interface=ui#restore-backup-ui) from the _Backups_ menu of your _Deployment dashboard_. Click **Restore** on a backup to change some options for the new deployment. One of the options is the database version, which is auto-populated with the versions available for you to upgrade to. Select a version and click **Restore** to start the provision and restore process.

### Upgrading using the CLI
{: #upgrading-cli}
{: cli}

To upgrade and restore from a backup using the [{{site.data.keyword.cloud_notm}} CLI](https://cloud.ibm.com/docs/cli?topic=cli-getting-started){: external}, use the provisioning command from the resource controller.
```sh
ibmcloud resource service-instance-create <service-name> <service-id> <service-plan-id> <region>
```
{: pre}

The parameters `service-name`, `service-id`, `service-plan-id`, and `region` are all required. You also supply the `-p` with the version and backup ID parameters in a JSON object. The new deployment is automatically sized with the same disk and memory as the source deployment at the time of the backup.

This command looks like:

```sh
ibmcloud resource service-instance-create example-upgrade databases-for-redis standard us-south \
-p \ '{
  "backup_id": "crn:v1:bluemix:public:databases-for-redis:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
  "version":6.2
}'
```
{: pre}

### Upgrading using the API
{: #upgrading-api}
{: api}

Complete the required steps to use the [Resource Controller API](/docs/databases-for-redis?topic=databases-for-redis-provisioning&interface=api#provision-controller-api) before you use it to upgrade from a backup. Then, send the API a `POST` request. The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required. You also supply the version and backup ID. The new deployment has the same memory and disk allocation as the source deployment at the time of the backup. 

This command looks like:
```sh
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "my-instance",
    "target": "bluemix-us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-redis-standard",
    "backup_id": "crn:v1:bluemix:public:databases-for-redis:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
    "version":6.2
  }'
```
{: pre}

## If you require data migration
{: #upgrading-req-data-migration}

For guidance about migration, see [Migrating to {{site.data.keyword.databases-for-redis}}](/docs/databases-for-redis?topic=databases-for-redis-migrating).

## Backing up and restoring: example
{: #backup_restore}

Complete the following on-demand backup and restore steps to upgrade. This example sets out the steps to upgrade from V5 to V6.2.

To create a manual backup in the UI:

1. Go to the **Backups and restore** tab of your {{site.data.keyword.databases-for-redis}} instance
2. Click **Create Backup**    
    A message is displayed that a backup is in progress, and an on-demand backup is added to the list of available backups [The on-demand backup can be seen in the overview page]

To restore a backup to a new service instance:

1. Go to the **Backups and restore** tab
2. Click in the corresponding row to expand the options for the on-demand backup that you want to restore
3. Click **restore backup** (this will re-direct to the restore instance page).
   In the **Restore** page, you can modify the new instance service name, region and resource allocation values. By default, the new instance is auto-sized to the same disk and memory allocation as the source instance at the time of the backup from which you are restoring.
    Under **Service Configuration**, select ‘6.2’ as the Database Version 
4. Click **Restore Backup**.

Backups are restored to a new instance. After the new instance finishes provisioning, your data in the backup file is restored into the new instance. The new upgraded 6.2 instance can be accessed from **Resource List**.

Do not delete the source instance while the backup is restoring. Before you delete the old instance, wait until the new instance is provisioned and the backup is restored. Deleting an instance also deletes its backups.
{: .note}