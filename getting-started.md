---
copyright:
  years: 2018, 2023
lastupdated: "2023-04-27"

keywords: redis, getting started redis, deploy redis, redli

subcollection: databases-for-redis

content-type: tutorial
account-plan: paid
completion-time: 30m

---

{{site.data.keyword.attribute-definition-list}}

# Getting Started
{: #getting-started}
{: toc-content-type="tutorial"}
{: toc-completion-time="30m"}

This tutorial is a short introduction to using an {{site.data.keyword.databases-for-redis_full}} deployment.

## Before you begin
{: #before-begin}

- You need to have an [{{site.data.keyword.cloud_notm}} account](https://ibm.biz/databases-for-redis-signup){: external}.

- You also need a {{site.data.keyword.databases-for-redis}} deployment, which can be provisioned from the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog/databases-for-redis/). Give your deployment a memorable name that appears in your account's Resource List.

- Download and install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli) and the [Cloud Databases CLI Plugin](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference). Use the {{site.data.keyword.cloud_notm}} CLI tool to communicate with {{site.data.keyword.cloud_notm}} from your terminal or command line, and the plugin contains communication commands for your database deployments.

- Before connecting, set your admin password. For more information, see [Setting the Admin Password](/docs/databases-for-redis?topic=databases-for-redis-user-management&interface=ui#user-management-set-admin-password-ui).

For more information, see [`Getting to production`](/docs/cloud-databases?topic=cloud-databases-best-practices).

## Download and Install Redli
{: #downloading-installing-redli}
{: step}

To get a feel for connecting, sending, and retrieving data with Redis, start by connecting with a CLI client. All {{site.data.keyword.cloud_notm}} Databases use TLS/SSL secured connections, so use a Redis CLI client that supports TLS/SSL, like [Redli](https://github.com/IBM-Cloud/redli){: .external}.

To install Redli, [download](https://github.com/IBM-Cloud/redli/releases){: .external} and install the package for your system. Uncompress the files, give the binary executable permissions, and move it to your path. For example, to install it on a Mac, download the `redli....darwin_amd64.tar.gz` file and run a command like this one: 

```sh
tar zxvf redli_0.4.4_darwin_amd64.tar.gz
chmod +x redli
sudo cp redli /usr/local/bin
```
{: pre}

Unfortunately, the native Redis CLI client does not support TLS/SSL connections and deployments require secure connections. To use Redis CLI, set up something like [Stunnel](https://www.stunnel.org/index.html){: .external} to handle the TLS/SSL connection. For more information, see [Connecting with a CLI client](/docs/databases-for-redis?topic=databases-for-redis-connecting-cli-client#connecting-with-redis-cli).
{: .tip}

## Connect to Redis
{: #connecting-redis}
{: step}

You can connect to Redis through the {{site.data.keyword.cloud_notm}} CLI or through Redli.

### Connect through the {{site.data.keyword.cloud_notm}} CLI
{: #connecting-ibm-cloud-cli}

Now that you've installed and set up Redli, the {{site.data.keyword.cloud_notm}} CLI tool, and the {{site.data.keyword.cloud_notm}} Databases plugin, connect to your deployment. Log in with `ibmcloud login`, and connect with `ibmcloud cdb cxn -s`, for example: 

```sh
ibmcloud cdb cxn -s <deployment-name>
```
{: pre}

When prompted for the admin password, use Redli to connect. You can now store and retrieve data with your {{site.data.keyword.databases-for-redis_full}} deployment. 

### Connect through Redli
{: #connecting-redli}

You can also connect to your deployment through Redli, without using the {{site.data.keyword.cloud_notm}} CLI.

Connection strings for your deployment are displayed in the _Dashboard Overview_, in the _Endpoints_ panel. The _CLI_ tab contains information that a CLI client uses to connect to your deployment. To connect, you need the self-signed certificate, the certificate name, and the connection string in the _CLI endpoint_ field. 

![CLI Endpoints panel](images/cli-endpoints-pane.png){: caption="Figure 1. CLI Endpoints panel" caption-side="bottom"}

Download or save the _contents_ of the TLS certificate to a file and name the file with the TLS certificate _name_. Remember where the file is saved.

The connection string in the _CLI endpoint_ field is the fully-formatted command to make a CLI connection to your deployment using Redli. Navigate to where you have saved the certificate on your system, or provide the full path to the certificate to the `REDIS_CERTFILE` environment variable. Set the admin user name and password in the environment as `USERNAME` and `PASSWORD`. Use the CLI connection string to start Redli, for example:

```sh
REDIS_CERTFILE=101afa63-91d0-11e9-a88d-5a059876d90f redli -u rediss://$USERNAME:$PASSWORD@da4103eb-f1ef-4f2d-8b41-0bfd98cb65bc.8117147f814b4b2ea643610826cd2046.databases.appdomain.cloud:30174/0
```

## Use Redis
{: #using-redis}
{: step}

Your connection is now open, which looks like: 

```text
Connected to 4.0.10
>
```
you can start storing and retrieving data.
```sh
Connected to 4.0.10
> set foo bar
OK
> get foo
"bar"
>
```

## Next Steps
{: #redis-next-steps}

The Redis documentation has [an introduction to Redis data types and abstractions](https://redis.io/topics/data-types-intro){: .external}, and a [Command Reference](https://redis.io/commands/){: .external}.

For information on best practices, see [Best Practices for Redis on the IBM Cloud](https://www.ibm.com/cloud/blog/best-practices-for-redis-on-the-ibm-cloud){: .external}.

If you are using Redis for the first time, see the [official Redis documentation](https://redis.io/documentation){: .external}. 

To use {{site.data.keyword.databases-for-redis_full}} with your applications, see [Connecting an external application](/docs/databases-for-redis?topic=databases-for-redis-external-app) and [Connecting an IBM Cloud application](/docs/databases-for-redis?topic=databases-for-redis-ibmcloud-app).

To ensure the stability of your applications and your database, see [High-Availability](/docs/databases-for-redis?topic=databases-for-redis-high-availability) and [Performance](/docs/databases-for-redis?topic=databases-for-redis-performance)

For more information on migrating your existing data to {{site.data.keyword.databases-for-redis}}, see [A How-To for Migrating Redis to IBM Cloud Databases for Redis](https://www.ibm.com/cloud/blog/a-how-to-for-migrating-redis-to-ibm-cloud-databases-for-redis). 
