---
copyright:
  years: 2021, 2024
lastupdated: "2024-04-07"

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
| {{site.data.keyword.databases-for-redis}} V5  |	-> {{site.data.keyword.databases-for-redis}} V6.2 |
| {{site.data.keyword.databases-for-redis}} V6 |	-> {{site.data.keyword.databases-for-redis}} V6.2 |
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

## Backing up and restoring: example
{: #backup_restore}
{: ui}

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

## Forced upgrade
{: #forced_upgrade}

If you choose not to upgrade using restore and upgrade, {{site.data.keyword.databases-for-redis}} will be force upgraded to the next major version at the version end of life. 
{: .note}


## If you require data migration
{: #upgrading-req-data-migration}

For guidance about migration, see [Migrating to {{site.data.keyword.databases-for-redis}}](/docs/databases-for-redis?topic=databases-for-redis-migrating).
