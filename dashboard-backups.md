---

copyright:
  years: 2017,2018
lastupdated: "2018-08-03"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Backups
{: #backups}

{{site.data.keyword.databases-for-redis_full}} backups are accessible from the _Backups_ tab of your service dashboard. Daily and on-demand backups are available for 30 days. Each backup is labeled with its type, and when the backup was taken. Click the backup to expand the options for any available backup.

## Restoring a Backup

To restore a backup to a new service instance, click in the corresponding row to expand the options for the backup you want to download. Click on the **Restore** button. A message is displayed to let you know that a restore has been initiated. The new service instance will automatically be named "redis-restore-[timestamp]", and appears on your {{site.data.keyword.cloud_notm}} dashboard when provisioning starts.

You can also use the {{site.data.keyword.cloud_notm}} CLI to restore a backup.

```
ibmcloud resource service-instance-create SERVICE_INSTANCE_NAME databases-for-redis standard REGION -p '{"backup_id":"{backup_id"}
```

**Note:** The change the value of `SERVICE_INSTANCE_NAME` to the name you want for your new service and REGION is where you want the service to be located.

A pre-formatted command for a specific backup is available in detailed view of the backup on the _Backups_ tab of the service dashboard.
{: .tip}

## Managing Backups via the API

Use the `https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/backups` endpoint to view and manage the available backups for your deployment. Send a `GET` request to return a list of all the backups, including each backup's ID. Send a `POST` request to initiate an on-demand backup of your deployment.

### Listing backups

```
curl -X GET -H "Authorization: Bearer $APIKEY" -H "Content-Type: application/json" "https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/backups"
```
{: codeblock}

### Starting an on-demand backup

```
curl -X POST -H "Authorization: Bearer $APIKEY" -H "Content-Type: application/json" "https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/groups"
```
{: codeblock}

### Retrieving information about a backup

To get information about a specific backup, send a `GET` request to the endpoint `https://api.{region}.databases.cloud.ibm.com/v4/ibm/backups/{backup_id}`.

```
curl -X GET -H "Authorization: Bearer $APIKEY" -H "Content-Type: application/json" "https://api.{region}.databases.cloud.ibm.com/v4/ibm/backups/{backup_id}"
```
{: codeblock}

For more examples, see the [API Reference](https://pages.github.ibm.com/compose/apidocs/cloud-databases-api-static.html#tag/Backups).

## Managing Backups via the {{site.data.keyword.cloud_notm}} CLI cloud databases plugin

Use the `cdb deployment-backups-list` command to view the list of all available backups for your deployment. To get the details about a specific backup use `cdb backup-show` command.

For example, to view the backups for a deployment named "example-deployment", use the following command.

```
ibmcloud cdb deployment-backups-list example-deployment
```
{: codeblock}

To see the details of one of the backups from the list:

```
ibmcloud cdb backup-show crn:v1:staging:public:databases-for-postgresql:us-south:a/6284014dd5b487c87a716f48aeeaf99f:3b4537bf-a585-4594-8262-2b1e24e2701e:backup:a3364821-d061-413f-a0df-6ba0e2951566
```
{: codeblock}
