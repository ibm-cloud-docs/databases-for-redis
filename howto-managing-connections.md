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

You can check the value of `maxclients` with your [admin user](/docs/databases-for-mysql?topic=databases-for-mysql-user-management#the-admin-user) and [`mysql`](/docs/databases-for-mysql?topic=databases-for-mysql-connecting-mysql).
```shell
ibmclouddb=> SHOW max_connections;
 max_connections
-----------------
 200
(1 row)
```
{: .codeblock}

## MySQL Connection Limits 
{: #managing-mysql-connection-limits}

At provision, {{site.data.keyword.databases-for-mysql}} sets the maximum number of connections to your MySQL database to **200**. You can raise this value by [Changing the MySQL Configuration](/docs/databases-for-mysql?topic=databases-for-mysql-changing-configuration). We recommend leaving some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. 

As well, it is recommended that you limit the number of simultaneous connections for any non-admin account. For example, setting `max_user_connections=3` will restrict the given user account to a maximum of three simultaneous connections.
{: .tip}

After the connection limit has been reached, any attempts at starting a new connection result in an error. 

```shell
FATAL: remaining connection slots are reserved for
non-replication superuser connections
```
Exceeding the connection limit for your deployment can cause your database to be unreachable by your applications.

You can access information about connections to your deployment with the admin user, `mysql`, and `SHOW GLOBAL STATUS`.
```shell
mysql> SHOW GLOBAL STATUS;
+-----------------------------------+------------+
| Variable_name                     | Value      |
+-----------------------------------+------------+
| Aborted_clients                   | 0          |
| Aborted_connects                  | 0          |
| Bytes_received                    | 155372598  |
| Bytes_sent                        | 1176560426 |
...
| Connections                       | 30023      |
| Created_tmp_disk_tables           | 0          |
| Created_tmp_files                 | 3          |
| Created_tmp_tables                | 2          |
...
| Threads_created                   | 217        |
| Threads_running                   | 88         |
| Uptime                            | 1389872    |
+-----------------------------------+------------+
```

If you need to figure out where the connections are going, you can break down the connections by database with the help of the `threads_connected` variable.
``` shell
mysql> show status where `variable_name` = 'Threads_connected';
```
{: pre}

To further investigate your connections, use the `SHOW PROCESSLIST` command.
```shell
SHOW [FULL] PROCESSLIST;
```
{: pre}

## Terminating Connections
{: #managing-mysql-connections-terminating}

Each connection to [mysqld](https://dev.mysql.com/doc/refman/5.7/en/mysqld.html), the MySQL Server, runs in a separate thread and can be killed with a `processlist_id` statement.
```shell
KILL [CONNECTION | QUERY] processlist_id
```
{: pre}

- `KILL CONNECTION` terminates the connection associated with the given `processlist_id`, after terminating ny statement the connection is executing. 
- `KILL QUERY` terminates the statement the connection is currently executing, but leaves the connection itself intact.

Check out the [MySQL 5.7 Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/kill.html) for more information on the `KILL` statement.


### End Connections
{: #managing-mysql-connections-end}

If your deployment reaches the connection limit or you are having trouble connecting to your deployment and suspect that a high number of connections is a problem, you can disconnect (or end) all of the connections to your deployment. 

In the UI, on the _Settings_ tab, there is a button to `End Connections` to your deployment. Use caution, as it disrupts anything that is connected to your deployment.

The CLI command to end connections to the deployment is 
```shell
ibmcloud cdb deployment-kill-connections <deployment name or CRN>
```

You can also use the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api#kill-connections-to-a-MySql-deployment) to perform the end all connections operation.

## Connection Pooling
{: #managing-mysql-connection-pooling}

One way to prevent exceeding the connection limit and ensure that connections from your applications are being handled efficiently is through connection pooling.

[MySQL Connectors](https://dev.mysql.com/doc/refman/5.7/en/connectors-apis.html) enable you to connect and execute MySQL statements from another language or environment, including ODBC, Java (JDBC), C++, Python, PHP, Perl, Ruby, and native C and embedded MySQL instances.

For example, connection pooling with [MySQL Connector/J](https://dev.mysql.com/doc/refman/5.7/en/connector-j-info.html) can increase performance while reducing overall usage. [MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/connector-python-connection-pooling.html) also allows for optimization using the `mysql.connector.pooling` module.
