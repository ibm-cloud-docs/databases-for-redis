---

copyright:
  years: 2024
lastupdated: "2024-04-30"

keywords: troubleshooting for Redis, common errors

subcollection: databases-for-redis

content-type: troubleshoot

---

{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:support: data-reuse='support'}
{:codeblock: .codeblock}
{:pre: .pre}
{:note:.deprecated}
{:troubleshoot: data-hd-content-type='troubleshoot'}


# How can I avoid common {{site.data.keyword.databases-for-redis}} errors?
{: #troubleshoot-common-errors}
{: troubleshoot}
{: support}


{{site.data.keyword.databases-for-redis}} is an in-memory database. It differs from traditional persistent databases in terms of how it accepts, processes, and stores data. The following information highlights common errors and a few reasons for those errors that you might experience, and the solutions that you can apply.

## Error 1: Connection to master lost message

{{site.data.keyword.databases-for-redis}} works with 2 member (Master+replica) and 3 Sentinels. There could be various reasons for `connection to master lost` message.  Some of those reasons are as follows:

a. Because of lower IOPS, master is busy and does not respond to Sentinels, which returns this error message in LogDNA.

b. Because of network latency, Sentinels are unable to communicate with master.

c. Scheduled maintenance activities

d. Minor version upgardes

### Solutions:
{: tsResolve}

a. Increase timeout to > 30 sec. Or make it user configurable at {{site.data.keyword.IBM}} {{site.data.keyword.databases-for}}.

b. Increase disk size. 1GB equals 10 IOPS.

c. Move to dedicated cores so that noisy neighbours issue is not there.

d. Increase RAM size.

e. RETRY logic is a must.

f. The replica will be promoted to master automatically after few seconds.

{{site.data.keyword.IBM}} {{site.data.keyword.databases-for}} does not perform any activity on MASTER node. If needed, all activities are taken on replica node, which is then promoted to master.
{: note}

Databases are set to wait for 30 seconds to find the master node, before replica promotion occurs.

## Error 2: Asynchronous AOF fsync is taking too long (disk is busy?)

AOF is Append Only File. This is the log written to Redis persistent disk when persistent setting is on. Note: Redis use AOF and RDB files to write its log and RDB for snapshotting.

a. Because of insufficient IOPS, Redis is unable to write to disk.

b. There could be our backup running and then customers batch running at the same time.

c. Your application might be putting unknown API calls to the Redis instance. 

Except Psync which is used for metrics, we don’t request any other API in customer’s instance.
{: note}

### Solutions:
{: tsResolve}

a. Increase disk size. Ballpark 30% extra than expected volume.

b. If Redis is at max capacity, then split single Redis instance to multiple instances and adjust your application architecture.

c. Turn off persistence if it is not required. Change to CACHE mode.

Disk cannot be scaled down. Memory can be scaled down. As Redis is in-memory data store, we recommend that you evaluate your ongoing data size before downsizing memory of your instance as significant reduction can result in error. This is because 
{: note}

## Error 3: Scaling an instance is stuck or taking longer

With {{site.data.keyword.Bluemix_notm}}, you can scale or resize your instance as your data needs grows. We offer auto-scaling and manual scaling from UI, CLI, and APIs. However, you should be careful as you resize your instance. If your scaling is taking longer, it could be for one of the following reasons:

a. Your instance is already in the largest instance (cores*rams), and there is no bigger cluster available to move their workload at the moment. In this case, a new cluster is formed and their instance is moved, which can take upto 90 minutes.  — Shashank: Not sure if we shall write this? Seems weakness at our side at our side. Perhaps a rephrase?

b. Customer has reduced its memory drastically (eg: from 16GB RAM to 10GB RAM), however, their data store size (disk) is higher eg: 11GB. In these cases, there is not sufficient space in RAM to read data from disk, and formation might get stuck.

### Solutions:
{: tsResolve}

a. Redis is single threaded. Customers are expected to use 3-5 threads at max. This will increase the probability of a cluster availability. It is rare to have redis cluster or high Cores. 

b. Ask customers to increase their RAM and DISK based on their IO needs.

c. Customers should not reduce their RAM drastically. Gradual decrement is advised.

There should be watermark memory left for Redis to perform its inherent processes.
{: note} 

## Error 4: READONLY You can't write against a read only slave

{{site.data.keyword.databases-for-redis}} has a two nodes, master and replica. Users can connect only to master node and replica is used for ensuring high-availability, which is generally unaccessible to customers. However, because of certain reasons, as it can be with any remote connections, a switchover can occur where the replica is promoted to master. Users can experience a momentary blip in service, and no other impact is expected if they are configured correctly.

a. Because of lower IOPS, master is busy and does not respond to Sentinels, which returns this error message  error in LogDNA.

b. Because of network latency, Sentinels are unable to communicate with master.

c. Scheduled maintenance.

d. Minor version upgrades.

### Solutions:
{: tsResolve}

a. Include retry and reconnect logic in your application design. You can use libraries like ioredis and noderedis. For more details, see [error detection and handling with Redis blog post](https://developer.ibm.com/articles/error-detection-and-handling-with-redis/).
b. Certain clients have retry and reconnect logic built in. You can leverage these clients as well.
c. Retry and reconnect logic is strongly recommended for any cloud services.

 






------------
If you encounter errors when connecting to your {{site.data.keyword.databases-for-redis_full}} deployment, review these common causes and resolutions.
{: shortdesc}

You receive an error message or fail to connect to a {{site.data.keyword.databases-for-redis}} deployment.  If reviewing application logs, you might see errors that mention intermittent connection timeouts or unable to connect.
{: tsSymptoms}

Review the following information to troubleshoot and resolve common connectivity problems:{: .tsResolve}
* An unsecured connection is a common cause of connectivity errors.  All {{site.data.keyword.databases-for-redis}} connections use TLS/SSL encryption; {{site.data.keyword.databases-for-redis}} rejects unsecured connections.  To avoid errors, make sure you configured a secure connection.  Refer to [Getting Started](/docs/databases-for-redis?topic=databases-for-redis-getting-started) for an example of a secure connection.
* If you are using a private endpoint, make sure that you specify connection strings that contain the private endpoint (see [Credentials for Private Endpoints](/docs/databases-for-redis?topic=cloud-databases-service-endpoints#credentials-for-private-endpoints)) and that you followed the steps in [Connecting through Private Endpoints](/docs/databases-for-redis?topic=cloud-databases-service-endpoints#private-endpoint-connections).
* If your application log captures a short connection interruption, that behavior is expected as a normal part of operations for this managed service. You want to design your applications to retry connections when errors are caused by a temporary loss in connectivity to your deployment or to {{site.data.keyword.Bluemix_notm}}. However, if you experience several minutes of connection interruption check the Cloud Status for the service. For more information, see [Application-level High-Availability](/docs/databases-for-redis?topic=databases-for-redis-high-availability#ha-for-your-application).

