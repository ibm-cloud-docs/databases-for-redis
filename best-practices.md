---

copyright:
  years: 2026
lastupdated: "2026-01-22"

keywords: redis, databases, best practices

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Best practices for {{site.data.keyword.databases-for-redis}}
{: #best-practices}

Take time to review the following best practices that are recommended if you're using {{site.data.keyword.databases-for-redis}}.

## What is {{site.data.keyword.databases-for-redis}}?
{: #what-is-redis}

{{site.data.keyword.databases-for-redis}} is a managed Redis OSS service offered on {{site.data.keyword.Bluemix_notm}}. It is an in-memory _data structure store_ used as a database, cache, message broker, and streaming engine. Unlike traditional databases, which store data on disk, Redis stores data in memory (RAM) enabling low latency. The data is _ephemeral_, which enables {{site.data.keyword.databases-for-redis}} to offer top performance and high throughput. You can also configure Redis to store data persistently on disk by trading off performance with data availability, because this is achieved by enabling AOF (Append Only File) sync with RDB snapshots.

RDB snapshots are enabled for backup and high availability, even if persistence is disabled.
{: note}

## Best practices for instance capacity planning
{: #capacity-planning}

You must plan {{site.data.keyword.databases-for-redis}} instance capacity based on your application and architectural design, understand hardware requirements, and prepare for the increase or decrease in demand. The following recommendations can help you to optimize the size your instance.

Understand your data
:   You are expected to know the data types, size, and lifetime of your data. This helps you understand the duration of data available in-memory before it gets deleted or moved.

Estimate memory requirements
:   It is important to calculate your memory requirements. Remember to factor in not only your data, but also the memory required for replication, client connections, max-memory buffers, and Redis metadata.

Understand the read/write loads
:   Identifying your read/write loads helps you prepare for auto-scaling needs, time your application requests, and maintain master-follower sync.

Understand your IOPS needs
:   Input/output operations per second is a key factor that you should consider in instance capacity planning. {{site.data.keyword.databases-for-redis}} takes RDB snapshots periodically in keeping with the default Redis configuration, which engages disk even if your instance is set up for cache. It's possible for very busy databases to exceed the IOPS for the disk size, and increasing disk size can alleviate a performance bottleneck.

Plan for redundancy and reconnects
:   There are multiple components that are involved in cloud computation, which can cause momentary failures. However, at {{site.data.keyword.IBM}} {{site.data.keyword.databases-for}}, we offer 99.99% high availability, and encourage you to plan for connection blips in your application design using retry and reconnect logic.

Consider network bandwidth
:   Your network bandwidth might significantly impact your {{site.data.keyword.databases-for-redis}} performance. Ensure that you have sufficient network bandwidth to handle your database loads.

Monitoring and adjustments
:   Our recommendation is to monitor your {{site.data.keyword.databases-for-redis}} instance performance and usage using {{site.data.keyword.monitoringlong}} to determine the evolving usage pattern of your database instance and resize as needed.

## Best practices for performance
{: #best-practice-performance}

Disable persistence
:   By default, {{site.data.keyword.databases-for-redis}} has persistence enabled. This writes AOF sync and increases IOPS load. If your application doesn’t need to persist data, disable this using the command `Set appendonly = no`. For more information, see [Setting an example cache](/docs/databases-for-redis?topic=databases-for-redis-redis-cache&interface=cli#redis-cache-example-cache).

RAM vs cores
:   Redis is a single-threaded, in-memory database. Inherently, it needs more RAM than CORES, unlike other persistent databases. Even though it is single-threaded, it uses ‘multiplexing’ to process the requests, but all the requests are processed by a thread. However, other cores are needed to maintain database integrity and stability for its internal processes. You are encouraged to focus more on RAM and disk (for IOPS) for {{site.data.keyword.databases-for-redis}}. For more information, see [Best practices for  instance capacity planning](/docs/databases-for-redis?topic=databases-for-redis-best-practices#capacity-planning).

Downsizing memory
:   You are advised to take care when you reduce the memory of your {{site.data.keyword.databases-for-redis}} instance. Because Redis is an in-memory database, its memory holds your data for storage, processing, and retrieval purposes. Drastically reducing the memory can temporarily stop your instance returning an error, because there is not sufficient space available to complete the operations. For example, consider 20 GB of data trying to load in 15 GB of memory, this case is bound to return an error.

Avoid expensive commands
:   Certain commands in Redis are expensive to run. For example, the KEYS command, which is used frequently, but should be avoided. Instead, use the SCAN command, which spreads the iteration over many calls and does not tie up your whole server at one time.

Choose an eviction policy
:   You should choose an eviction policy that works for your application. By default, deployments are configured with a `noeviction` policy. Use eviction policies like `allkeys-lru`, `volatile-lru`, `allkeys-random`, `volatile-random`, `volatile-ttl`. For more information, see [memory policy](/docs/databases-for-redis?topic=databases-for-redis-redis-cache#redis-cache-maxmemory).

Set maxmemory values
:   You can adjust the `maxmemory` value. However, set a reasonable limit, otherwise your data can consume all the available memory and your deployment can run out of resources. By default, we set it at 80% of the data node’s available memory.

Set TTL (Time-To-Live) policies
:   TTL is a great feature where keys are deleted from the database after a defined time. This is extremely helpful if you are using Redis a cache. However, be careful about setting a very short or a very long value because a very short value can create recomputation of values and a very long value can create unnecessary memory use. For more information, see [TTL command](https://redis.io/docs/latest/commands/ttl/){: external}.

## Best practices for high availability
{: #best-practice-high-availability}

Retry and reconnect logic
:   Systems are prone to disruptions. You are highly encouraged to implement retry and reconnect logic into your application architecture to avoid disruptions. Use IOREDIS, NODEREDIS or any other package of your choice to ensure continuity of your application.

For more information, see [Error detection and handling with Redis blog post](https://developer.ibm.com/articles/error-detection-and-handling-with-redis/){: external}.

## Best practices for monitoring
{: #monitoring}

You must monitor your {{site.data.keyword.logs_full}} for the following common errors and take corrective measures:

* AOF sync
* Read-only available
* Connection to master lost
* For guidance about preventing errors, see [How can I avoid common {{site.data.keyword.databases-for-redis}} errors?](/docs/databases-for-redis?topic=databases-for-redis-troubleshoot-common-errors).

## General best practices
{: #best-practice-general}

Connection pooling
:   Creating or closing connections is costly. Managing connections efficiently is important and connection pooling is helpful to minimize the overhead associated with opening and closing connections. For more information, see [connection pooling](/docs/databases-for-redis?topic=databases-for-redis-managing-redis-connections&interface=cli#managing-redis-connection-pooling).

Connection limits
:   Utilize connections efficiently. Overloading {{site.data.keyword.databases-for-redis}} with too many connections will return errors and you will face application disruptions. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. You are encouraged to use connection pooling. For more information, see [connection limits](/docs/databases-for-redis?topic=databases-for-redis-managing-redis-connections&interface=cli#managing-redis-connection-limits).

Connection timeouts
:   Setting appropriate timeout values for your connections is also important to prevent resources being tied up indefinitely. However, be careful with setting short timeouts because this can lead to connection churn and increased latency. Align timeouts with the operational expectations of your application.

Use the Redis pipeline feature
:   Redis pipelining is a technique for improving performance by issuing multiple commands at once without waiting for the response to each individual command. For more information, see [Redis pipelining](https://redis.io/docs/latest/develop/use/pipelining/){: external}.

Use the Redis Streams feature
:   Redis Streams is a data type that provides a super fast in-memory abstraction of an append-only log.

Split large data
:   You are recommended to split large datasets into smaller chunks with more keys, that is, split your data over multiple keys.

Batch schedule
:   {{site.data.keyword.databases-for-redis}} is scheduled to create automated backups everyday at a scheduled time. During this time, your databases IOPS are utilized. It is recommended that you should not run your own batch jobs at this time.

Set up notification channels
:   We recommend that {{site.data.keyword.databases-for-redis}} you set up email IDs in IBM Accounts to receive periodic updates about version changes, end-of-life, or maintenance schedules. You can also monitor your IBM Account notification icon to receive these updates.

For more information, see [Best practices for Redis on the {{site.data.keyword.cloud_notm}} blog post](https://www.ibm.com/blog/best-practices-for-redis-on-the-ibm-cloud/){: external}.
