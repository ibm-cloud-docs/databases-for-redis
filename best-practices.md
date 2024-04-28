---

copyright:
  years: 2024
lastupdated: "2024-04-25"

keywords: redis, databases, best practices

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Best practices for {{site.data.keyword.databases-for-redis}}
{: #best-practices}

Take time to review the suggested practices to reduce the security risks for all production, staging, and test servers in your cloud infrastructure. This list is an excellent starting point to increase the security of your cloud infrastructure.

Review the following suggested guidelines for implementing best practices around your secrets management with IBM Cloud® Secrets Manager.

Follow our best practices to learn about IBM Cloud® billing options and tools that you can use to track your usage and manage invoicing and payments.

After you set up your IBM Cloud® account, you're ready to start planning how you want to organize resources and assign access to identities in your account. These best practices provide you with the basic building blocks to enable successful and secure app development in IBM Cloud.

Below are some of the best practices that are recommended for our customers using Databases for Redis.

--------

## What is Databases for Redis?
{: #what-is-redis}

Databases for Redis is a managed service of Redis OSS offered on IBM Cloud. It is in-memory data structure store used as a database, cache, message broker, and streaming engine. Unlike traditional databases which stores data in disk, Redis stores data in memory(RAM) enabling low latency. The data is ephemeral enabling Databases for Redis for top performance and high throughput. Users can also configure to store data as persistent store on disk by trading-off performance with data availability, as this is achieved by enabling AOF sync with RBD snapshots.

Note: RBD snapshots are enabled for backup and high-availability, even if persistence is disabled.

## Best practices for an instance capacity planning
{: #capacity-planning}

Customers must plan the Databases for Redis instance capacity based on their application & architectural design, understand hardware requirements and prepare for the increase or decrease in demand. Below are some recommendations that could help you to right-size your instance

a. Understanding your data: Customers are expected to know the data types, size and lifetime of their data. This will help you understand the duration of data available in-memory before it gets deleted or moved.

b. Estimate the memory requirement: It is important to calculate the memory requirement. Remember to factor in not only for your data, but also for replication, client connections, max-memory buffers, Redis metadata.

c. Understand the Read/Write loads: Identifying your read-write loads will help you prepare for auto-scaling needs, time your application requests and maintain master-follower sync

d. Understand your IOPS needs: Input-Output per second is a key factor that should be considered in instance capacity planning. Databases for Redis takes RDB snapshots periodically as per the default Redis configuration, which will engage disk even if your instance is setup for cache. It's possible for very busy databases to exceed the IOPS for the disk size, and increasing disk can alleviate a performance bottleneck.

e. Plan for redundancy and reconnects: There are multiple components that are involved in cloud computation, which brings variability of momentary failures. However, at IBM Cloud Databases, we offer 99.99% high-availability, and encourage our customers to plan for connection blips into their application design using retry/reconnect logic.

f. Consider network bandwidth: Your network bandwidth might significantly impact your Databases for Redis performance. Ensure you have sufficient network bandwidth to handle your database loads.

g. Monitoring and adjustments: Our recommendation is to monitor your Databases for Redis instance performance and usage to determine the evolving usage pattern of your database instance and resize as needed.


## Best practices for performance
{: #performance}

a. Disable persistence: By default, Databases for Redis has persistance enabled. This write AOF sync and increases IOPS load. If your application doesn’t need to persist data, disable this using the command.

b. RAM vs Cores: Redis is single-threaded, in-memory database. Inherently, it needs more RAM than CORES, unlike other persistent databases. Even though it is single-threaded, it uses ‘multiplexing’ to process the requests, but all of them are processed by a thread. However, other cores are needed to maintain the sanctity and stability of databases for its internal processes. We encourage our users to emphasis more on RAM and Disk (for IOPS) for Database for Redis. Read Best practices for an instance capacity planning. 

c. Downsizing memory: We advise to our customers to be careful when they reduce memory of their Databases for Redis instance. Since Redis is in-memory databases, its memory holds your data for storage, processing and retrieval purposes. Drastically reducing the memory can temporarily stop your instance returning an error, as there will not be sufficient space available to perform the operations. Think of it as loading 20GB of data trying to load in 15GB of memory. It is bound to return error.

d. Avoid expensive commands: Certain commands in Redis are expensive to execute. KEYS is one such commands which is used frequently, and should be avoided. Instead, use SCAN command, which spreads the iteration over many calls, not tying up your whole server at one time.

e. Choose an eviction policy: Customers should choose an eviction policy that works for your application. By default, deployments are configured with a noeviction policy. Use eviction policies like allkeys-lru, volatile-lru, allkeys-random, volatile-random, volatile-ttl. Refer memory policy for mode details

f. Set maxmemory values: Customers can adjust maxmemory value. However, set a reasonable limit, otherwise, your data can take all the available memory and your deployment runs out of resources. By default, we set it as 80% of data node’s available memory.

## Best practices for high-availability
{: #high-availability}

a. Retry/Reconnect logic: Systems are prone to disruptions. We highly encourage our customers to implement Retry/Reconnect logic into their application architecture to avoid disruptions. Use IOREDIS, NODEREDIS or any other package of your choice to ensure continuity of your application.

We have written a blog that you can refer Error detection and reconnect Redis

## Best practices for monitoring
{: #monitoring}

Customer must monitor their LogDNA for common errors and take corrective measures

AOF Sync

Read Only available

Connection to master lost

read other Box note on common errors

## General best practices 
{: #best-practice-general}

a. Connection pooling: Creating or closing connections is costly. Managing connection efficiently is important and connection pooling will be helpful to minimize the overhead associated with opening and closing connections.

b. Connection Limits: Customers must utilise the connections efficiently. Overloading Databases for Redis with too many connections will return error and you will face application disruptions. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. We encourage you to use connection pooling

c. Connection timeouts: Setting appropriate timeout values for your connections is also important to prevent resources being tied up indefinitely. However, be careful with setting short timeouts as this can lead to connection churn and increased latency. Align timeouts with the operational expectations of your application.

d. Use Redis Pipeline feature: Redis pipelining is a technique for improving performance by issuing multiple commands at once without waiting for the response to each individual command. Refer this link for details

e. Use Redis Streams feature: Redis Streams is a data type that provides a super fast in-memory abstraction of an append only log

g. Split large data: It is recommended that large datasets should be split into smaller chunks with more keys, ie split your data over multiple keys

h. Batch schedule: Databases for Redis is scheduled to create automated backups everyday at a scheduled time. During this time, your databases IOPS are utilized. It is recommended that customers should not run their own batch jobs at this time.

g. Setup notifications channels: We recommend Databases for Redis customers to setup email ids in IBM Accounts to receive periodic updates on version changes, end-of-life or maintenance schedules. They can also monitor their IBM Account notification icon to receive these updates.

We have also written a blog which lists further details on the best practices. Refer link for details





------------
The Data modeling document is the first best practice document in the series. It shows you the following best practices: 

- What you need to know about your APIs.
- How to model your data.
- What size documents you must use.
- What to avoid.
- How to configure your databases.
{: shortdesc}

For more information, see [Indexing and querying](/docs/Cloudant?topic=Cloudant-indexing-and-querying) or [{{site.data.keyword.cloudant_short_notm}} in practice](/docs/Cloudant?topic=Cloudant-cloudant-in-practice).

The content in this document was originally written by Stefan Kruger as a [*Best and worst practice*](https://blog.cloudant.com/2019/11/21/Best-and-Worst-Practices.html) blog post on 21 November 2019.

## Understand the API that you are targeting
{: #understand-api-you-target}

You can use [Java&trade;](https://github.com/IBM/cloudant-java-sdk){: external}, [Python](https://github.com/IBM/cloudant-python-sdk){: external}, [Go](https://github.com/IBM/cloudant-go-sdk){: external}, or [Node.js](https://github.com/IBM/cloudant-node-sdk){: external} or some other use-case-specific language or platform. One of these languages most likely comes with convenient client-side libraries that integrate {{site.data.keyword.cloudant_short_notm}} access nicely, following the conventions that you expect for your tools. These languages are great for programmer efficiency, but they also hide the API from view.

This abstraction is what you want, the whole reason for using a client library is to save yourself repeated, tedious boiler-plating. However, you must understand the underlying API is vital when you troubleshoot and report problems. When you report a suspected problem to {{site.data.keyword.cloudant_short_notm}}, it helps us help you if you can provide a way for us to reproduce the problem.

This request does not mean cutting and pasting a hefty chunk of your application’s Java&trade; source verbatim into a support ticket, as we’re probably not able to build it. Also, your client-side code introduces uncertainties as to where the problem might be, your side or our side?

Instead, {{site.data.keyword.cloudant_short_notm}}’s support teams usually requests the set of API calls, ideally as a set of [curl](https://curl.se/){: external} commands that they can run, that demonstrates the issue. Adopting this approach to troubleshooting as a rule also makes it easier for you to pinpoint where issues are failing. If your code is behaving unexpectedly, try to reproduce the problem by using only direct access to the API.

If you can’t, the problem isn’t with the {{site.data.keyword.cloudant_short_notm}} service itself.

If you’re investigating a performance issue, do consult the logs that are provided by {{site.data.keyword.cloud}}. If the logs show that your requests are handled quickly by {{site.data.keyword.cloudant_short_notm}}, but your application is slow, the root of that problem lies with your client-side application code. See the rule about [logging and monitoring](/docs/Cloudant?topic=Cloudant-cloudant-in-practice#see-whats-going-on-with-logs).

If you suspect that a problem lies with an officially supported client library, then try to construct a small, self-contained code example that demonstrates the issue. In this self-contained code example, use as few other dependencies as possible. If you’re using Java&trade;, it is helpful to us if you can use a minimal [test harness](https://github.com/mikerhodes/java-cloudant-minimal){: external} to highlight library issues.

Occasionally, {{site.data.keyword.cloudant_short_notm}} receives support tickets that state that “{{site.data.keyword.cloudant_short_notm}} is broken because my application is slow” without much in terms of supporting evidence. Nearly always this case can be traced back to issues in the application code on the client side, or misconceptions about how {{site.data.keyword.cloudant_short_notm}} works.

Not always, but nearly always.

By understanding the API better, you also gain experience in how {{site.data.keyword.cloudant_short_notm}} behaves, especially in terms of performance. If you’re using a client library, you must aim to at least know how to find out which HTTP requests are generated by a specific function call. For more information, see the following websites:

- {{site.data.keyword.cloudant_short_notm}} [API docs](/apidocs/cloudant){: external}
- Logging [integration](/docs/Cloudant?topic=Cloudant-log-analysis-integration){: external}
- Blog post on [logging](https://blog.cloudant.com/2019/09/16/Cloudant-Logging-with-LogDNA.html){: external}

## Documents must group data that mostly changes together
{: #documents-group-data-change}

When you start to model your data, sooner or later, you run into the issue of how your documents might be structured. Now you know that {{site.data.keyword.cloudant_short_notm}} doesn’t enforce any normalization and that it has no transactions of the type you’re used to from, say, [Postgres](https://www.postgresql.org/){: external}. The temptation can be to cram as much as possible into each document, which would also save on HTTP usage.

This practice is often a bad idea.

If your model groups information that doesn’t change together, you’re more likely to suffer from update conflicts.

Consider a situation where you have users, each with a set of orders associated with them. One way might be to represent the orders as an array in the user document:

```json
{ // DON'T DO THIS
    "customer_id": 65522389,
    "orders": [ {
      "order_id": 887865,
      "items": [ {
          "item_id": 9982,
          "item_name": "Iron sprocket",
          "cost": 53.0
        }, {
          "item_id": 2932,
          "item_name": "Rubber wedge",
          "cost": 3.0
        }
      ]
    }
  ]
}
```
{: codeblock}

To add an order, I need to fetch the complete document, unmarshal the JSON, add the item, marshal the new JSON, and send it back as an update. If I’m the only one doing so, it might work for a while. If the document is being updated concurrently, or being replicated, we might likely see update conflicts.

Instead, keep orders separate as their own document type, referencing the customer ID. Now the model is immutable. To add an order, I create a new order document in the database, which cannot generate conflicts.

To be able to retrieve all orders for a specific customer, we can employ a view, which we cover later.

Avoid constructs that rely on updates to parts of existing documents, where possible. Bad data models are often hard to change after you’re in production.

The previous pattern can be solved efficiently by using partitioned databases, which are covered in greater detailed later.

For more information, see the following documentation:

- {{site.data.keyword.cloudant_short_notm}} guide to [data modeling](/docs/Cloudant?topic=Cloudant-five-tips-for-modeling-your-data-to-scale-faq)
- [Database partitions](/docs/Cloudant?topic=Cloudant-database-partitioning){: external}

## Keep documents small
{: #keep-documents-small}

{{site.data.keyword.cloudant_short_notm}} imposes a max doc size of 1 MB. This limit does not mean that a close-to-1-MB document size is a good idea. On the contrary, if you find you are creating documents that exceed single-digit KB, you probably need to revisit your model. Several things in {{site.data.keyword.cloudant_short_notm}} become less performant as documents grow. JSON decoding is costly, for example.

Let's look at the following sections: *Documents must group data that mostly changes together* and *Keep documents small*. It’s worth stressing that models that rely on updates have a maximum volume limit of 1 MB, the cut-off for document size. This size isn’t what you want.

## Avoid using attachments
{: #avoid-using-attachments}

{{site.data.keyword.cloudant_short_notm}} has support for storing attachments alongside documents, a long-standing feature it inherits from CouchDB. If you use {{site.data.keyword.cloudant_short_notm}} as a backend for a web application, you can also store small icons and other static assets such as CSS and JavaScript files with the data.

You must consider a few things before you use attachments in {{site.data.keyword.cloudant_short_notm}} today, especially if you’re looking at larger assets such as images and videos:

1. {{site.data.keyword.cloudant_short_notm}} is expensive as a block store. 
2. {{site.data.keyword.cloudant_short_notm}}’s internal implementation is not efficient in handling large amounts of binary data.

So, slow and expensive.

{{site.data.keyword.cloudant_short_notm}} is acceptable for small assets and occasional use. As a rule, if you need to store binary data alongside {{site.data.keyword.cloudant_short_notm}} documents, it’s better to use a separate solution more suited for this purpose. You need store only the attachment *metadata* in the {{site.data.keyword.cloudant_short_notm}} document. Yes, that means you need to write some extra code to upload the attachment to a suitable block store of your choice. Verify that it succeeded before you store the token or URL to the attachment in the {{site.data.keyword.cloudant_short_notm}} document.

Your databases are smaller, cheaper, faster, and easier to replicate. For more information, see the following websites:

- {{site.data.keyword.cloudant_short_notm}} docs on [attachments](/docs/Cloudant?topic=Cloudant-how-to-use-attachments)
- Detaching {{site.data.keyword.cloudant_short_notm}} attachments to [Object Storage](https://medium.com/codait/detaching-cloudant-attachments-to-object-storage-with-serverless-functions-99b8c3c77925){: external}

## Fewer databases are better than many
{: #fewer-dbs-better-than-many}

If you can, limit the number of databases per {{site.data.keyword.cloudant_short_notm}} account to 500 or fewer. While this particular number is not magic (IBM Cloudant can safely handle more), several use cases exist that are adversely affected by large numbers of databases in an account.

The replicator scheduler has a limited number of simultaneous replication jobs that it is prepared to run. As the number of databases grows, the replication latency is likely to increase if you try to replicate everything contained in an account.

The flip side of the same coin is the operational aspect: {{site.data.keyword.cloudant_short_notm}}’s operations team relies on replication, too, to move around accounts. By keeping down the number of databases, you help us help you if you need to shift your account from one location to another.

So when must you use a single database and distinguish between different document types by using views, and when must you use multiple databases to model your data? {{site.data.keyword.cloudant_short_notm}} can’t federate views across multiple databases. If you have unrelated data that can never be “joined” or queried together, then that data can be a candidate for splitting across multiple databases.

If you have an ever-growing data set (like a log, sensor readings, or other types of time-series), it’s also not a good idea to create a single, ever-growing, massive database. This kind of use case requires time-boxing, which we cover in more detail later.
{: note}

## Avoid the *database per user* anti-pattern like the plague
{: #avoid-db-per-user}

If you’re building a multi-user service atop {{site.data.keyword.cloudant_short_notm}}, it is tempting to allow each user store their data in a separate database under the application account. That works well, mostly, if the number of users is small.

Now add the need to derive cross-user analytics. The way that you do that is to replicate all the user databases into a single analytics database. All good. This app has suddenly become successful, and the number of users grew in the range of 150 - 20,000. You have 20,000 replications just to keep the analytics database current. If you also want to run in an active-active disaster recovery setup, add another 20,000 replications, and the system stops functioning.

Instead, multiplex user data into fewer databases, or shard users into a set of databases or accounts, or both. That way, you do not need to replicate to provide an analytics database, but authentication becomes more complicated as {{site.data.keyword.cloudant_short_notm}} provides only authentication at the database level.

It’s worth stating that the “database-per-user” approach is tempting because {{site.data.keyword.cloudant_short_notm}} permissions are “per database”, but it’s not really the users’ fault that this pattern emerged.

## Avoid writing custom JavaScript reduce functions
{: #avoid-custom-javascript-reduce-functions}

The MapReduce views in {{site.data.keyword.cloudant_short_notm}} are awesome. However, with great power comes great responsibility. The map part of a MapReduce view is built incrementally, so shoddy code in the map impacts only indexing time, not query time. The reduce-part, unfortunately, runs at query time. {{site.data.keyword.cloudant_short_notm}} provides a set of built-in reduce functions that are implemented internally in [Erlang](https://www.erlang.org/){: external}. These functions are performant at scale while your hand-crafted JavaScript reduces are not.

If you find yourself writing reduce functions, stop and consider whether you can reorganize your data so that writing reduce functions isn’t necessary. Or so that you’re able to rely on the built-in reducers. 

Views on partitioned databases do not support custom reduces, which is one factor that contributes to the significant speed-up queries only such views can offer.
{: note}

For more information, see {{site.data.keyword.cloudant_short_notm}} docs on [reduces](/docs/Cloudant?topic=Cloudant-creating-views-mapreduce#reduce-functions){: external}.

## Use time boxed databases for ever-growing data sets
{: #timebox-dbs-growing-data-sets}

It’s generally *not* a good idea to have an ever-growing database in {{site.data.keyword.cloudant_short_notm}}. Large databases can be difficult to back up, require “resharding” to maintain good performance as they grow, and suffer from long index build times.

One way of mitigating this problem is to have several smaller databases instead, with a common pattern that is *time-boxed databases*: a large data set is split into smaller databases, each representing a time window, for example, a month.

- `orders_2019_01`
- `orders_2019_02`
- `orders_2019_02`

New data is written to this month’s database and queries for historical data can be directed to previous months’ databases. When a month’s data is no longer of interest, it can be archived to Object Storage, the monthly {{site.data.keyword.cloudant_short_notm}} database is deleted, and the disk space recovered. For more information, see the following website.

- [Time-series Data Storage blog](https://blog.cloudant.com/2019/04/08/Time-series-data-storage.html){: external}
