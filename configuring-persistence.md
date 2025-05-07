---
copyright:
  years: 2025
lastupdated: "2025-05-07"

subcollection: databases-for-redis

---

# Configuring as Persistence
{: #configuring-persistence}

Redis is recognized for its high-performance key-value database, notable for storing all data in RAM to avoid slow disk access. 
Nonetheless, this RAM-centric approach poses a risk of data loss in the event of a Redis process or host incident, given the volatile nature of RAM. 
To address this concern, Redis offers mechanisms for persisting data on disk.

## Persistence modes in Redis
{: #persistence-modes}

There are two primary persistence modes available: RDB (snapshot mode) and AOF (strong persistence). Each mode entails distinct trade-offs in terms of performance and reliability. 
Hence, selecting the appropriate persistence mode in Redis necessitates a strategic decision.

### RDB snapshot
{: #rdb-snapshot}

Redis saves snapshots of the dataset on disk in a binary file called dump.rdb. The dataset is saved every N seconds if there are at least M changes.

save 3600 1: Every hour if at least one key has changed.

save 300 100: Every 5 minutes if at least 100 keys have changed.

save 60 10000: Every minute if at least 10,000 keys have changed.

### AOF (Append only File)
{: #aof}

With AOF enabled, Redis persists data by logging every write operation received by the service. AOF is configured by fsync policy, ensuring data durability.

always: Safest but with lowest performance.

everysec (Default): Safe with better performance.

no: Typically relies on the operating system to decide when to perform fsync, which is usually around 30 seconds (unsafe but provides the best performance).

For more details on persistence, read Redis Persistence.

## How to set up persistence in IBM Cloud Databases for Redis

In IBM Cloud Databases for Redis deployment, both RDB snapshot and AppendOnly is enabled by default on provisioning and your data is written to disk. However, users can disable appendonly to use Database for Redis as a cache, which alleviates the IOPS load, resulting in better performance.

Note: IBM Cloud Databases for Redis operates in high-availability, wherein RDB snapshots cannot be disabled.

Steps to provision a Redis Instance in IBM Cloud. 

How to check the Persistence setting?

Users can verify Databases for Redis  configuration  by accessing the ICD Redis instance using Redis CLI.

Default Configuration:



 When appendonly is set to yes, Databases for Redis is configured to take AOF persistence with fsync every second along with RDB snapshots. 

Customizing Persistence Settings:

Appendonly can be turned off if you want to use Redis as a cache. This can also increase database availability, as the Redis process doesn’t have to replay the transaction logs in case of a failover. To set ICD Redis as a cache, read here as explained in detail.

How to re-configure a Databases for Redis as a Persistent setting:

To configure changes to a Databases for Redis instance, users must utilize either the IBM Cloud CLI or API for configuring persistence, as exemplified below.

 

Users need to adjust the following settings:

Set `appendonly` to "yes" to enable AOF persistence.

Ensure `maxmemory-policy` is set to “noeviction” to prevent key expiration.

Set `stop-writes-on-bgsave-error` to "yes" to halt writes in case of backup errors.

CLI

ibmcloud cdb deployment-configuration '<deployment name or CRN>' '{"configuration":{"maxmemory-policy":" noeviction", "appendonly":"yes", "stop-writes-on-bgsave-error":"yes"}}'

API

curl -X PATCH 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/configuration/schema' -H "Authorization: Bearer $APIKEY" -H "Content-Type: application/json" -d '{"configuration":{ "maxmemory-policy":""noeviction, "appendonly":"yes", "stop-writes-on-bgsave-error":"yes" } }'

