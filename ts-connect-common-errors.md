---

copyright:
  years: 2024
lastupdated: "2024-05-09"

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


{{site.data.keyword.databases-for-redis}} is an in-memory database. It differs from traditional persistent databases in how it accepts, processes, and stores data. The following information highlights common errors that you might experience, a few reasons for those errors, and the solutions that you can apply.

## Error: `Connection to master lost` message

{{site.data.keyword.databases-for-redis}} works with two members (master+replica) and three sentinels. There are various reasons for `connection to master lost` message.  Some of those reasons are as follows:

a. Because of lower input/output operations per second (IOPS), master is busy and does not respond to sentinels, which returns this error message in LogDNA. 

b. Because of network latency, sentinels are unable to communicate with master.

c. Scheduled maintenance activities.

d. Minor version upgrades.


a. Increase the timeout to over 30 seconds or make it user configurable at {{site.data.keyword.IBM}} {{site.data.keyword.databases-for}}.

b. Increase the disk size. 1 GB is equal to 10 IOPS.

c. Move to dedicated cores so that the noisy neighbours issue is eliminated.

d. Increase RAM size.

e. RETRY logic is required, so ensure this is implemented.

f. The replica will be promoted to master automatically after few seconds.
{: tsResolve}

{{site.data.keyword.IBM}} {{site.data.keyword.databases-for}} does not perform any activity on the master node. If needed, all activities are completed on the replica node, which is then promoted to master. Databases are set to wait for 30 seconds to find the master node before replica promotion occurs.
{: note}

## Error: Asynchronous AOF fsync is taking too long (disk is busy?)

AOF is Append Only File. This is the log written to Redis persistent disk when the persistent setting is on. 

Redis uses AOF and RDB files to write its log and RDB for snapshotting.
{: note}

Some of the reasons why you might see this error are as follows:

a. Because of insufficient IOPS, Redis is unable to write to disk.

b. There could a {{site.data.keyword.databases-for-redis}} backup running at the same time as your batch is running.

c. Your application might be putting unknown API calls to the Redis instance. 

Except Psync which is used for metrics, we don’t request any other API in your instance.
{: note}

### Solutions:
{: tsResolve}

a. Increase disk size. A ballpark recommendation is 30% extra than the expected volume.

b. If Redis is at maximum capacity, split a single Redis instance into multiple instances and adjust your application architecture.

c. Turn off persistence if it is not required. Change to cache mode.

Disk size cannot be scaled down but memory can be scaled down. Because Redis is an in-memory data store, we recommend that you evaluate your ongoing data size before downsizing the memory of your instance because significant reduction can result in error. **This is because**
{: note}

## Error: Scaling an instance is stuck or taking longer

With {{site.data.keyword.Bluemix_notm}}, you can scale or resize your instance as your data needs grow. We offer auto-scaling and manual scaling from the UI, CLI, and APIs. However, you should be careful as you resize your instance. If scaling is taking longer than expected, it could be for one of the following reasons:

a. Your instance is already in the largest instance (cores*rams), and there is no bigger cluster available to move your workload to currently. In this case, a new cluster is formed and your instance is moved, which can take up to 90 minutes.  
** Shashank: Not sure if we shall write this? Seems weakness at our side at our side. Perhaps a rephrase? **

b. You have reduced its memory drastically (for example, from 16 GB RAM to 10 GB RAM). However, your data store size (disk) is higher, for example, 11 GB. In these cases, there is not sufficient space in RAM to read data from disk, and formation might get stuck.

### Solutions:
{: tsResolve}

a. Redis is single threaded. You are expected to use a maximum of 3-5 threads. This increases the probability of a cluster's availability. It is rare to have Redis cluster or high Cores. 

b. Increase your RAM and DISK based on your I/O needs.

c. Do not reduce your RAM drastically. You are advised to decrement gradually.

Ensure there is watermark memory left for Redis to perform its inherent processes.
{: note} 

## Error: READONLY You can't write against a read-only slave

{{site.data.keyword.databases-for-redis}} has a two nodes, master and replica. Users can connect only to master node and the replica node is used for ensuring high-availability, which is generally inaccessible to users. However, as with any remote connection, a switchover can occur where the replica is promoted to master. You can experience a momentary blip in service, and no other impact is expected if they are configured correctly.

a. Because of lower IOPS, master is busy and does not respond to sentinels, which returns this error message in LogDNA.

b. Because of network latency, sentinels are unable to communicate with master.

c. Scheduled maintenance.

d. Minor version upgrades.

### Solutions:
{: tsResolve}

a. Include retry and reconnect logic in your application design. You can use libraries like `ioredis` and `noderedis`. For more details, see [error detection and handling with Redis blog post](https://developer.ibm.com/articles/error-detection-and-handling-with-redis/).

b. Certain clients have retry and reconnect logic built in. You can leverage these clients as well.

c. Retry and reconnect logic is strongly recommended for any cloud services.

 






