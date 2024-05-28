---

copyright:
  years: 2018, 2023
lastupdated: "2023-04-11"

keywords: deployment, crn, task, gui, api endpoint, connection strings, recent tasks, observability

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# The Dashboard overview
{: #dashboard-overview}

The _Overview_ page shows you information about your {{site.data.keyword.databases-for-redis_full}} deployment. The overview includes essential identifying information.

## Overview
{: #dashboard-overview-overview}

### Type
{: #dashboard-overview-type}

The type of database that is offered by the service, and the database version that your service uses.

### ID
{: #dashboard-overview-type}

The ID is a [CRN (Cloud Resource Name)](/docs/account?topic=account-crn) which uniquely identifies the database deployment. The CRN is used to refer to the database in the API and can be used with the CLI. _Overview_ shows details of your service.

### Recent Tasks
{: #dashboard-overview-recent-tasks}

Every time that you make administrative changes to your service (such as scaling, or taking a manual backup), a task starts up. _Recent Tasks_ shows the task name and progress bar for any running tasks, and a list of the most recent completed tasks. Depending on how busy your deployment is, successful tasks can be shown for 24-48 hours. Unsuccessful tasks can show for 7-8 days. Tasks can also be retrieved from the [Cloud Databases API](https://cloud.ibm.com/apidocs/cloud-databases-api#get-currently-running-tasks-on-a-deployment) and [CLI plug-in](https://cloud.ibm.com/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-tasks-list). A historical record of tasks from any time period is available through the [{{site.data.keyword.at_full}} integration](/docs/databases-for-elasticsearch?topic=databases-for-elasticsearch-activity-tracker).

### Endpoints
{: #dashboard-overview-endpoints}

_Endpoints_ within the _Overview_ contains connection strings for your deployment. Each tab contains connection information that is tailored to the type of connection or the protocol that uses it. Basic information includes _hostname_, _port_, the TLS self-signed certificate, TLS/SSL parameters, and the default database of your deployment.

Reference tables for the different connection types are available on the [Getting Credentials and Connection Strings](/docs/databases-for-redis?topic=databases-for-redis-connection-strings) page.

Connection strings reflect whether your deployment uses public endpoints, private endpoints or both. You can configure which endpoints are available on your deployment. For more information, see [Service Endpoints Integration](/docs/databases-for-elasticsearch?topic=databases-for-elasticsearch-service-endpoints).

You can manage your {{site.data.keyword.databases-for-redis}} service through the {{site.data.keyword.databases-for}} API. For more information, see [API reference](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#introduction).

## Resources
{: #dashboard-overview-resources}

The resources tab contains information and configuration options on the size and resource usage of your deployment. You can 
- [Scale disk, memory, and CPU](/docs/databases-for-redis?topic=databases-for-redis-resources-scaling)
- [Configure Autoscaling](/docs/databases-for-redis?topic=databases-for-redis-autoscaling)

## Backups
{: #dashboard-overview-backups}

The _Backups_ tab is the UI for managing your deployments backups. All of the available backups are listed with their timestamps. Click a backup to grab its ID or to restore it into a new deployment. For more information, see [Managing Backups](/docs/databases-for-elasticsearch?topic=databases-for-elasticsearch-dashboard-backups).

## Observability
{: #dashboard-overview-observability}

The _Observability_ tab provides access to the IBM Cloud monitoring, logging, and event tracking integrations available for your deployment. 
- [{{site.data.keyword.at_full}}](/docs/databases-for-elasticsearch?topic=databases-for-elasticsearch-activity-tracker)
- [{{site.data.keyword.la_full}}](/docs/databases-for-elasticsearch?topic=databases-for-elasticsearch-logging)
- [{{site.data.keyword.monitoringfull}}](/docs/databases-for-elasticsearch?topic=databases-for-elasticsearch-monitoring)

## Settings
{: #dashboard-overview-settings}

The _Settings_ tab contains the UI for many of the tunable settings for your deployment. You can:
- view encryption details. Encryption at rest is enabled for all {{site.data.keyword.databases-for-redis}} deployments. If you brought your own encryption key from [Key Protect](/docs/databases-for-elasticsearch?topic=databases-for-elasticsearch-key-protect), the panel provides a link to your Key Protect instance and the _Encryption Key_ field has the name of the key.
- [Change the admin password](/docs/databases-for-redis?topic=databases-for-redis-user-management&interface=ui#user-management-set-admin-password-ui)
- [Implement or modify an IP allowlist](/docs/databases-for-elasticsearch?topic=databases-for-elasticsearch-allowlisting)

## Service Credentials
{: #dashboard-overview-service-cred}

You can generate a new set of credentials for cases where you want to manually [connect an app](/docs/databases-for-redis?topic=databases-for-redis-ibmcloud-app) or [external consumer](/docs/databases-for-redis?topic=databases-for-redis-external-app) to an IBM Cloud service. For more information, see [Adding and viewing credentials](/docs/account?topic=account-service_credentials).

## Connections
{: #dashboard-overview-connections}

Shows connected resources. You can use the `Create connection` button to bind this service to another resource.

## View docs
{: #dashboard-overview-view-docs}

The _View docs_ link from the `Actions` drop list opens the main documentation page for {{site.data.keyword.databases-for-redis}} in a new tab.
