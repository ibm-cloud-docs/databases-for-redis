---
copyright:
  years: 2018, 2025
lastupdated: "2025-06-05"

keywords: redis, getting started redis, deploy redis, redli

subcollection: databases-for-redis

content-type: tutorial
account-plan: paid
completion-time: 30m

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with {{site.data.keyword.databases-for-redis}}
{: #getting-started}
{: toc-content-type="tutorial"}
{: toc-completion-time="30m"}

This tutorial guides you through the steps to quickly start using {{site.data.keyword.databases-for-redis_full}} deployment.

Follow these steps to complete the tutorial: {: ui}

* [Before you begin](#prereqs)
* [Step 1: Provision through the console](#provision_instance_ui)
* [Step 2: Set your Admin password](#admin_pw)
* [Step 3: Connect to your instance](#redis_connect)
* [Step 4: Use Redis](#using-redis)
* [Next steps](#next_steps)
* [Things to remember](#remember_important)
{: ui}

Follow these steps to complete the tutorial: {: cli}

* [Before you begin](#prereqs)
* [Step 1: Provision through the CLI](#provision_instance_cli)
* [Step 2: Set your Admin password](#admin_pw)
* [Step 3: Connect to your instance](#redis_connect)
* [Step 4: Use Redis](#using-redis)
* [Next steps](#next_steps)
* [Things to remember](#remember_important)
{: cli}

## Before you begin
{: #prereqs}
{: ui}

- You need an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com/registration){: external}. 

- Download and install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli) and the [Cloud Databases CLI plugin](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference). Use the {{site.data.keyword.cloud_notm}} CLI tool to communicate with {{site.data.keyword.cloud_notm}} from your terminal or command line, and the plugin contains communication commands for your database deployments.
- Download and install [Redli](#downloading-installing-redli){: external}.

## Before you begin
{: #prereqs}
{: cli}

- You need an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com/registration){: external}. 
- Download and install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli) and the [Cloud Databases CLI Plugin](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference). Use the {{site.data.keyword.cloud_notm}} CLI tool to communicate with {{site.data.keyword.cloud_notm}} from your terminal or command line, and the plugin contains communication commands for your database deployments.
- Download and install [Redli](#downloading-installing-redli){: external}.

## Download and install Redli
{: #downloading-installing-redli}

Redli is a Go-based alternative to the official Redis-cli application.

Connect, Send and Receive data with Redis, by connecting with a CLI client. All {{site.data.keyword.cloud_notm}} Databases use TLS/SSL secured connections, so use a Redis CLI client that supports TLS/SSL, like [Redli](https://github.com/IBM-Cloud/redli){: .external}.

To install Redli, [download](https://github.com/IBM-Cloud/redli/releases){: .external} and install the package for your system. Uncompress the files, give the binary executable permissions, and move it to your path. For example, to install it on a Mac, download the `redli....darwin_amd64.tar.gz` file and run a command like this one:

```sh
tar zxvf <redli_version>.tar
example: tar zxvf redli_0.13.0_darwin_amd64.tar

chmod +x <extracted redli filename>
sudo cp <extracted redli filename> /usr/local/bin
```
{: pre}

## Step 1: Provision through the console
{: #provision_instance_ui}
{: ui}

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/login){: external}.
1. Click the [{{site.data.keyword.databases-for-redis_full}} service](https://cloud.ibm.com/databases/databases-for-redis/create){: external} in the **Catalog**.
1. Follow [these steps](/docs/databases-for-redis?topic=databases-for-redis-provisioning&interface=ui) to provision a {{site.data.keyword.databases-for-redis_full}} instance.
1. When your instance is provisioned, click on the instance name to view more information.

## Step 1: Provision through the CLI
{: #provision_instance_cli}
{: cli}

1. You can provision a {{site.data.keyword.databases-for-redis_full}} instance by using the CLI. If you don't already have it, you need to install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-getting-started){: external}.
1. Follow [these steps](/docs/databases-for-redis?topic=databases-for-redis-provisioning&interface=cli) to provision a {{site.data.keyword.databases-for-redis_full}} instance.

## Step 2: Set your Admin password
{: #admin_pw}

### The admin user
{: #admin_pw_admin_user}

When you provision a {{site.data.keyword.databases-for-redis_full}} deployment, an `admin` user is automatically created.

Set the `admin` password before using it to connect to {{site.data.keyword.databases-for-redis_full}}.
{: important}

### Set the admin password through the UI
{: #admin_pw_set_ui}
{: ui}

Set your `admin` password through the UI by selecting your instance from the [{{site.data.keyword.cloud_notm}} Resource list](https://cloud.ibm.com/resources){: external}. Then, select **Settings**. Next, select **Change Database Admin Password**.

Refer [Managing Users and Roles](https://cloud.ibm.com/docs/databases-for-redis?topic=databases-for-redis-user-management&interface=ui#user-management-set-admin-password-ui){: external} to know more.

### Set the admin password through the CLI
{: #admin_pw_set_cli}
{: cli}

Use the `cdb user-password` command from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to set the `admin` password. For example, use the following command:

```sh
ibmcloud cdb user-password <INSTANCE_NAME_OR_CRN> admin <NEWPASSWORD>
```
{: pre}

Refer [Managing users and roles](https://cloud.ibm.com/docs/databases-for-redis?topic=databases-for-redis-user-management&interface=cli#user-management-set-admin-password-cli){: external} to know more.

## Step 3: Connect to your instance
{: #redis_connect}

You can easily connect to Redis through the {{site.data.keyword.cloud_notm}} CLI using Redli to store and retrieve data with your {{site.data.keyword.databases-for-redis_full}} deployment.

### Connect through the {{site.data.keyword.cloud_notm}} CLI
{: #connecting-ibm-cloud-cli}

Now that you've installed and set up Redli, the {{site.data.keyword.cloud_notm}} CLI tool, and the {{site.data.keyword.cloud_notm}} Databases plugin, connect to your deployment. Log in with `ibmcloud login`, and connect with `ibmcloud cdb cxn -s`, for example:

```sh
ibmcloud cdb cxn -s <INSTANCE_NAME_OR_CRN>
```
{: pre}

Provide `admin` password when prompted and it uses _REDLI_ to connect.

You can explore other options to connect to {{site.data.keyword.databases-for-redis_full}} using [Connecting through the command-line interface (CLI)](/docs/databases-for-redis?topic=databases-for-redis-connecting-cli-client){: external}.

## Step 4: Use Redis
{: #using-redis}

Your connection is now open, which looks like:

```text
>
```
{: pre}

You can start storing and retrieving data.

```sh
> set foo bar
OK
> get foo
"bar"
>
```
{: pre}

## Next steps
{: #next_steps}

* If you are using Redis for the first time, read the [official Redis documentation](https://redis.io/documentation){: .external}, [an introduction to Redis data types and abstractions](https://redis.io/topics/data-types-intro){: .external}, and a [Command reference](https://redis.io/commands/){: .external} to learn about Redis.

* Implement {{site.data.keyword.databases-for-redis_full}} best practices, read [Best practices for Redis on the IBM Cloud](/docs/databases-for-redis?topic=databases-for-redis-best-practices){: .external}.

* To use {{site.data.keyword.databases-for-redis_full}} with your applications, see [Connecting an external application](/docs/databases-for-redis?topic=databases-for-redis-external-app) and [Connecting an IBM Cloud application](/docs/databases-for-redis?topic=databases-for-redis-ibmcloud-app).

* To ensure the stability of your applications and your database, see [High-availability](/docs/databases-for-redis?topic=databases-for-redis-redis-ha-dr) and [Performance](/docs/databases-for-redis?topic=databases-for-redis-performance).

* For more information on migrating your existing data to {{site.data.keyword.databases-for-redis}}, see [A how-to for migrating Redis to IBM Cloud Databases for Redis](/docs/databases-for-redis?topic=databases-for-redis-migrating&interface=ui).

## Things to remember
{: #remember_important}

* {{site.data.keyword.databases-for-redis_full}} deployments are set by default as [**persistence**](/docs/databases-for-redis?topic=databases-for-redis-redis-ha-dr#ha-feature), however, you can change it as [**Cache**](/docs/databases-for-redis?topic=databases-for-redis-redis-cache&interface=cli).

* Integrate {{site.data.keyword.databases-for-redis_full}} with [{{site.data.keyword.monitoringfull}}](/docs/databases-for-redis?topic=databases-for-redis-monitoring) and [{{site.data.keyword.logs_routing_full}}](/docs/logs-router?topic=logs-router-about) services to observe trends in your usage and right size your instance.
