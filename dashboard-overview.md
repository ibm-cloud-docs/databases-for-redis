---

Copyright:
  years: 2018
lastupdated: "2018-08-20"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Service Overview

The _Overview_ page shows you information about your {{site.data.keyword.databases-for-redis_full}} database. The overview includes essential identifying information.

## Deployment Details

The _Deployment Details_ panel shows details of your service.

### Type

The type of database that is offered by the service, and the database version that your service uses. In this case, it is `redis`.

### ID

The ID is a [CRN (Cloud Resource Name)](https://console.bluemix.net/docs/overview/crn.html) which uniquely identified the database deployment. The CRN is used when referring to the database in the API and can be used with the CLI.

## Recent Tasks

Every time that you make administrative changes to your service (such as scaling, or taking a manual backup), a task starts up. The _Recent Tasks_ panel shows the task name and progress bar for any running tasks, and a list of the most recent completed tasks.

## Instance Administration API

You can manage your {{site.data.keyword.databases-for-redis}} service through the {{site.data.keyword.cloud_notm}} databases API. This panel provides the essential information for any user wishing to make use of this capability.

### Foundation Endpoint

The foundation endpoint is the opening stanza of the URL to be used to send API requests to. It combines the region your service resides in and the base {{site.data.keyword.cloud_notm}} databases API endpoint. 

### Deployment ID

Many calls to the API require that the database deployment be identified. The database deployment's ID/CRN shown here is the same as the ID/CRN shown in the Deployment Details, in a click-to-copy field to make it simpler to use. Note that this ID will need to be URL encoded to be used in an API call, because by design it includes a "/".

### {{site.data.keyword.cloud_notm}} databases API Reference

For more information on using the {{site.data.keyword.cloud_notm}} databases API, see the [API reference page](https://console.stage1.bluemix.net/apidocs/cloud-databases-api).