---

copyright:
  years: 2019, 2024
lastupdated: "2024-12-02"

keywords: redis, databases, ha, dr

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# High availability
{: #high-availability}

{{site.data.keyword.databases-for-redis_full}} is a managed cloud database service that is fully integrated into the {{site.data.keyword.cloud_notm}} environment. The database, storage, and supporting infrastructure all run in {{site.data.keyword.cloud_notm}}.

## Database high-availability features
{: #high-availability-features}

{{site.data.keyword.databases-for-redis}} provides replication, fail-over, and high-availability features to protect your databases and data from infrastructure maintenance, upgrades, and failures. Deployments contain a cluster with two data members in a primary plus replica configuration, and kept in sync by using asynchronous replication. High-availability is monitored and managed with three [Redis sentinels](https://redis.io/topics/sentinel){: external}.

By default, data persistence is enabled on all deployments and your data is written to disk. The data persistence model uses [preamble snapshots and AOF (Append Only File)](https://redis.io/topics/persistence){: external}. The interval for Redis to write to disk (`fsync`) is set to [once every second](https://redis.io/topics/persistence#how-durable-is-the-append-only-file){: external}.

You can turn off data persistence, which is useful for [configuring Redis as a cache](/docs/databases-for-redis?topic=databases-for-redis-redis-cache).

## High availability for your application
{: #high-availability-application}

You want to design your applications to handle a temporary loss in connectivity to your deployment or to {{site.data.keyword.cloud_notm}}.

Many Redis clients have features for error checking and handling, see the following examples:

- The Python client [`Redis.py`](https://github.com/andymccurdy/redis-py#connections){: external} produces a `ConnectionError` when a connection fails and another command is run. The client can also issue health checks periodically or before commands.
- The Java client [`Jedis`](https://github.com/xetorthio/jedis/wiki){: external} produces a `JedisConnectionException` when it can't reach the database, and it tries to reconnect. However, it doesn't automatically retry a command, as not every command is safe to retry or run multiple times.
- The C# driver [`StackExchange.Redis`](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options){: external} has adjustable parameters for client-side timeout, reconnect, and retry logic. You can tune these parameters to suit your application requirements.

A good example of cloud-ready application development that uses the Node.js client `ioredis` is in the IBM Developer article [Error detection and handling with Redis](https://developer.ibm.com/articles/error-detection-and-handling-with-redis/){: external}.

For more information on connecting your applications to {{site.data.keyword.databases-for-redis}}, see the following documentation pages:

- [Connecting to an IBM Cloud application](/docs/databases-for-redis?topic=databases-for-redis-ibmcloud-app),
- [Connecting an external application](/docs/databases-for-redis?topic=databases-for-redis-external-app) (including [TLS and self-signed certificate support](/docs/databases-for-redis?topic=databases-for-redis-external-app#tls-cert-support)), and
- [Connecting with a command line client](/docs/databases-for-redis?topic=databases-for-redis-connecting-cli-client).

In all cases, you must review your client's documentation to help you design a stable application that checks for connectivity, reconnects when disconnected, and retries commands when it makes sense for your application to do so.
{: .tip}

Several minutes of database unavailability or connection interruptions are not expected. Open a [support ticket](/docs/account?topic=account-using-avatar) with details if you have time periods longer than a minute with no connectivity.

## Monitoring the state of your databases
{: #high-availability-monitor-databases}

{{site.data.keyword.databases-for-redis}} has [{{site.data.keyword.logs_full}} integration](/docs/databases-for-redis?topic=databases-for-redis-logging) so that you can view live and historical logs.

Checking your deployment's logs helps you monitor the state of HA and replication for your deployment. If you are having persistent issues with your applications, logs can also provide insight to what is happening on your databases when you are experiencing connection failures or other disruptions.

## High availability, disaster recovery, and SLA resources
{: #high-availability-dr-sla}

{{site.data.keyword.databases-for-redis}} deployments conform to the {{site.data.keyword.cloud_notm}} Databases [HA, DR, and SLA](/docs/cloud-databases?topic=cloud-databases-ha-dr) information and terms.
