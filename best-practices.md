---

copyright:
  years: 2024
lastupdated: "2024-04-30"

keywords: redis, databases, best practices

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Best practices for {{site.data.keyword.databases-for-redis}}
{: #best-practices}

Take time to review the following best practices that are recommended for our customers using {{site.data.keyword.databases-for-redis}}.


## What is {{site.data.keyword.databases-for-redis}}?
{: #what-is-redis}

{{site.data.keyword.databases-for-redis}} is a managed service of Redis OSS offered on {{site.data.keyword.Bluemix_notm}}. It is in-memory _data structure store_ used as a database, cache, message broker, and streaming engine. Unlike traditional databases which stores data in disk, Redis stores data in memory(RAM) enabling low latency. The data is _ephemeral_ enabling {{site.data.keyword.databases-for-redis}} for top performance and high throughput. Users can also configure to store data as persistent store on disk by trading-off performance with data availability, as this is achieved by enabling AOF sync with RBD snapshots.

RBD snapshots are enabled for backup and high-availability, even if persistence is disabled.
{ :note}

## Best practices for instance capacity planning
{: #capacity-planning}

Customers must plan the {{site.data.keyword.databases-for-redis}} instance capacity based on their application and architectural design, understand hardware requirements, and prepare for the increase or decrease in demand. The following recommendations could help you to right-size your instance.

Understand your data
:   You are expected to know the data types, size, and lifetime of their data. This helps you understand the duration of data available in-memory before it gets deleted or moved.

Estimate memory requirements
:   It is important to calculate memory requirement. Remember to factor in not only for your data, but also for replication, client connections, max-memory buffers, and Redis metadata.

Understand the read/write loads
:   Identifying your read-write loads helps you prepare for auto-scaling needs, to time your application requests, and to maintain master-follower sync.

d. **Understand your IOPS needs**
Input-Output per second is a key factor that you should consider in instance capacity planning. {{site.data.keyword.databases-for-redis}} takes RDB snapshots periodically as per the default Redis configuration, which engages disk even if your instance is set up for cache. It's possible for very busy databases to exceed the IOPS for the disk size, and increasing disk can alleviate a performance bottleneck.

e. **Plan for redundancy and reconnects**
There are multiple components that are involved in cloud computation, which brings variability of momentary failures. However, at {{site.data.keyword.IBM}} {{site.data.keyword.databases-for}}, we offer 99.99% high-availability, and encourage customers to plan for connection blips in their application design using retry and reconnect logic.


f. **Consider network bandwidth**
Your network bandwidth might significantly impact your {{site.data.keyword.databases-for-redis}} performance. Ensure that you have sufficient network bandwidth to handle your database loads.

g. **Monitoring and adjustments**
Our recommendation is to monitor your {{site.data.keyword.databases-for-redis}} instance performance and usage to determine the evolving usage pattern of your database instance and resize as needed.


## Best practices for performance
{: #performance}

a. Disable persistence
By default, {{site.data.keyword.databases-for-redis}} has persistance enabled. This write AOF sync and increases IOPS load. If your application doesn’t need to persist data, disable this using the command.

b. RAM vs Cores
Redis is single-threaded, in-memory database. Inherently, it needs more RAM than CORES, unlike other persistent databases. Even though it is single-threaded, it uses ‘multiplexing’ to process the requests, but all of them are processed by a thread. However, other cores are needed to maintain the sanctity and stability of databases for its internal processes. You are encouraged to focus more on RAM and Disk (for IOPS) for {{site.data.keyword.databases-for-redis}}. For more information, see [Best practices for  instance capacity planning](/docs/databases-for-redis?topic=databases-for-redis-best-practices#capacity-planning). 

c. Downsizing memory
You are advised to be careful when you reduce the memory of their {{site.data.keyword.databases-for-redis}} instance. Because Redis is an in-memory database, its memory holds your data for storage, processing, and retrieval purposes. Drastically reducing the memory can temporarily stop your instance returning an error, because there will not be sufficient space available to perform the operations. Consider loading 20 GB of data trying to load in 15 GB of memory. It is bound to return an error.

d. Avoid expensive commands
Certain commands in Redis are expensive to run. For example, the KEYS command, which is used frequently, but should be avoided. Instead, use SCAN command, which spreads the iteration over many calls and does not tie up your whole server at one time.

e. Choose an eviction policy 
You should choose an eviction policy that works for your application. By default, deployments are configured with a noeviction policy. Use eviction policies like `allkeys-lru`, `volatile-lru`, `allkeys-random`, `volatile-random`, `volatile-ttl`. See [memory policy](/docs/databases-for-redis?topic=databases-for-redis-redis-cache#redis-cache-maxmemory) for mode details.

f. Set maxmemory values
You can adjust the `maxmemory` value. However, set a reasonable limit, otherwise, your data can take all the available memory and your deployment can run out of resources. By default, we set it as 80% of data node’s available memory.

## Best practices for high-availability
{: #high-availability}

Retry/Reconnect logic 
Systems are prone to disruptions. You are highly encouraged to implement retry and reconnect logic into your application architecture to avoid disruptions. Use IOREDIS, NODEREDIS or any other package of your choice to ensure continuity of your application.

We have written a blog that you can refer to [Error detection and handling with Redis](https://developer.ibm.com/articles/error-detection-and-handling-with-redis/).

## Best practices for monitoring
{: #monitoring}

You must monitor your LogDNA for the following common errors and take corrective measures:

* AOF Sync
* Read Only available
* Connection to master lost
* read other Box note on common errors

## General best practices 
{: #best-practice-general}

a. Connection pooling
Creating or closing connections is costly. Managing connections efficiently is important and connection pooling is helpful to minimize the overhead associated with opening and closing connections.

b. Connection limits 
Utilise connections efficiently. Overloading {{site.data.keyword.databases-for-redis}} with too many connections will return errors and you will face application disruptions. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. You are encouraged to use connection pooling.

c. Connection timeouts
Setting appropriate timeout values for your connections is also important to prevent resources being tied up indefinitely. However, be careful with setting short timeouts because this can lead to connection churn and increased latency. Align timeouts with the operational expectations of your application.

d. Use the Redis Pipeline feature
Redis pipelining is a technique for improving performance by issuing multiple commands at once without waiting for the response to each individual command. See [Redis pipelining](https://redis.io/docs/latest/develop/use/pipelining/) for more information.

e. Use the Redis Streams feature
Redis Streams is a data type that provides a super fast in-memory abstraction of an append only log.

g. Split large data
You are recommended to split large datasets into smaller chunks with more keys, that is, split your data over multiple keys.

h. Batch schedule
{{site.data.keyword.databases-for-redis}} is scheduled to create automated backups everyday at a scheduled time. During this time, your databases IOPS are utilized. It is recommended that you should not run your own batch jobs at this time.

g. Set up notifications channels
We recommend that {{site.data.keyword.databases-for-redis}} customers set up email IDs in IBM Accounts to receive periodic updates about version changes, end-of-life, or maintenance schedules. You can also monitor your IBM Account notification icon to receive these updates.

We have also written a blog which lists further details about best practices 
[Best Practices for Redis on the IBM Cloud](https://www.ibm.com/blog/best-practices-for-redis-on-the-ibm-cloud/)





