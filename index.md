---

Copyright:
  years: 2018
lastupdated: "2018-08-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# About {{site.data.keyword.databases-for-redis_full_notm}}
{: #about-databases-for-redis}

{{site.data.keyword.databases-for-redis_full}} is a managed Redis service that is hosted in the {{site.data.keyword.cloud_notm}} and integrated with other {{site.data.keyword.cloud_notm}} services.

## Creating a {{site.data.keyword.databases-for-redis}} service
{: #creating-databases-for-redis-service}

You can create a {{site.data.keyword.databases-for-redis}} service from the {{site.data.keyword.cloud_notm}} catalog.

Choose a service name, and a region, organization, and space to provision the service in. You can use the _Select a database version_ field to choose from the available database versions and the _Select an initial memory allocation_ to set the initial amount of RAM for your service. Click **Create** to start the provisioning. You are taken back to your {{site.data.keyword.cloud_notm}} dashboard to monitor its progress.
 
### Creating a service via the {{site.data.keyword.cloud_notm}} CLI

You can create a service through the {{site.data.keyword.cloud_notm}} CLI using the `resource service-instance-create` command.
```
ibmcloud resource service-instance-create SERVICE_INSTANCE_NAME databases-for-redis standard REGION
```
`SERVICE_INSTANCE_NAME` is the name for your new service instance, and REGION is where you want the service to be located.

## Managing {{site.data.keyword.databases-for-redis}}

{{site.data.keyword.databases-for-redis}} is an Identity and Access Management (IAM) integrated service. Access is governed by the roles and attributes that are consistent across IAM-integrated services in {{site.data.keyword.cloud_notm}}. Get started with managing your users on the [IAM Getting Started tutorial](https://console.{DomainName}/docs/iam/quickstart.html#getstarted). For more information on IAM, see the [What is IAM?](https://console.{DomainName}/docs/iam/index.html#iamoverview) documentation.

More information on IAM roles and actions for {{site.data.keyword.databases-for-redis}} is available on the [Identity and Access Management](./reference-access-management.html) reference page.

### Accessing the UI

You can manage your service by selecting _Manage_ from the left sidebar and opening the management panel from your service. Here you can find information about your {{site.data.keyword.databases-for-redis}} database.  Database settings are available in the [Settings](./dashboard-settings.html) tab and backups are available through the [Backups](./dashboard-backups.html) tab.

### Using the CLI

You can manage your service through the {{site.data.keyword.cloud_notm}} CLI. If you haven't already downloaded and installed it, get it [here](https://console.{DomainName}/docs/cli/index.html#overview). 

Once you have the {{site.data.keyword.cloud_notm}} CLI, there is an {{site.data.keyword.cloud_notm}} databases plug-in available. Install it with the command `ibmcloud plugin install cloud-databases`.

If not available, add the IBM staging repository for plugins with `ibmcloud plugin repo-add stage1 https://plugins.stage1.ng.bluemix.net/` and then install the plugin with `ibmcloud plugin install cloud-databases -r stage1`.

Once you have it installed, run `ibmcloud cdb help` for other commands and usage information. 

### Using the API

{{site.data.keyword.databases-for-redis}} can be configured and managed with the {{site.data.keyword.cloud_notm}} databases API. Authentication is IAM-based, so use your {{site.data.keyword.cloud_notm}} account's platform API keys when accessing the API. More information on API keys is in the [IAM documentation](https://console.{DomainName}/docs/iam/apikeys.html#platform-api-keys). The API foundation endpoint for your service is on the _Overview_ page when you open the _Manage_ panel of your service. *The full API reference is available on [GitHub](https://pages.github.ibm.com/compose/apidocs/).*

## Database Administration and Connecting to Redis

Access to the Redis databases is provided by [setting up the admin user password](./administering-password.html) on your {{site.data.keyword.databases-for-redis}} service.

You can connect to your deployment by using the connection strings and command line information that are provided upon provision of your service.

### Connecting an {{site.data.keyword.cloud_notm}} application to {{site.data.keyword.databases-for-redis}}

To connect an {{site.data.keyword.cloud_notm}} application to your service, use credentials that are created in the _Service Credentials_ panel. You can find information on how to connect an {{site.data.keyword.cloud_notm}} application to a {{site.data.keyword.databases-for-redis}} service in [Connecting an {{site.data.keyword.cloud_notm}} Application](./connecting-ibmcloud-app.html).

### Connecting to {{site.data.keyword.databases-for-redis}} from outside {{site.data.keyword.cloud_notm}}

If you want to connect to {{site.data.keyword.databases-for-redis}} from outside {{site.data.keyword.cloud_notm}}, you can use the provided connection strings or command line. You can find information on how to connect in [Connecting an external application](./connecting-external.html).


