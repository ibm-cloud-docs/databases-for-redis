---

copyright:
  years: 2017, 2022
lastupdated: "2022-04-27"

keywords: redis, databases, connection limits, terminating connections, connection pooling

subcollection: databases-for-redis

---

{:external: .external target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Managing Redis Connections
{: #managing-redis-connections}

Connections to your {{site.data.keyword.databases-for-redis_full}} deployment use resources, so it is important to consider how many connections you need when tuning your deployment's performance. MySQL uses a `maxclients` setting to limit the number of connections (and resources that are consumed by connections) to prevent runaway connection behavior from overwhelming your deployment's resources.

You can check the value of `maxclients` with your [admin user](/docs/databases-for-redis?topic=databases-for-redis-user-management#the-admin-user).
```shell
code snippet needed here
```

## Redis Connection Limits 
{: #managing-mysql-connection-limits}

At provision, {{site.data.keyword.databases-for-redis_full}} sets the maximum number of connections to your Redis deployment to **10000**. We recommend leaving some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. 

We recommend that you use [connection pooling](#managing-redis-connection-pooling) to minimize the number of active connections against your deployment.
{: .tip}

After the connection limit has been reached, any attempts at starting a new connection result in an error. 

```shell
code snippet needed
```
Exceeding the connection limit for your deployment can cause your database to be unreachable by your applications.

You can access information about connections to your deployment with the admin user.
```shell
code snippet needed

```

If you need to figure out where the connections are going, you can break down the connections by database with the help of the `__________________` variable.
``` shell
code snippet needed;
```
{: pre}

To further investigate your connections, use the `___________` command.
```shell
code snippet needed;
```
{: pre}

## Terminating Connections
{: #managing-redis-connections-terminating}

Due to the single-threaded nature of Redis, it is not possible to kill a client connection while it is executing a command. From the client point of view, the connection can never be closed in the middle of the execution of a command. However, the client will notice the connection has been closed only when the next command is sent (and results in network error).

- `CLIENT KILL` command closes a given client connection. 

Check out the [Redis CLIENT KILL documentation](https://redis.io/commands/client-kill/) for more information on the `CLIENT KILL` statement.


### End Connections
{: #managing-redis-connections-end}

If your deployment reaches the connection limit or you are having trouble connecting to your deployment and suspect that a high number of connections is a problem, you can disconnect (or end) all of the connections to your deployment. 

In the UI, on the _Settings_ tab, there is a button to `End Connections` to your deployment. Use caution, as it disrupts anything that is connected to your deployment.

The CLI command to end connections to the deployment is 
```shell
code snippet needed
```

## Connection Pooling
{: #managing-redis-connection-pooling}

One way to prevent exceeding the connection limit and ensure that connections from your applications are being handled efficiently is through connection pooling.

