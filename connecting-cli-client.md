---
copyright:
  years: 2017,2018
lastupdated: "2018-09-27"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}


# Connecting with a command line client

You can access your Redis database directly from a command line client. A command line client allows for direct interaction and monitoring of the data structures that are created within the database. It is also useful for administering and monitoring the keyspace and performance, installing and modifying scripts, and other management activities.

## Installing `redis-cli`
 
`redis-cli` is the official supported command line interface for Redis. Unfortunately, it does not support TLS connections. For that reason, we suggest you consider `redli` as a client (see the next section).

If you do choose to use `redis-cli`, there are some extra configuration steps. It comes as part of the Redis package, so you need Redis installed locally to use it. On macOS, we recommend installing [brew](http://brew.sh) and then using `brew install redis` to get up and running. On Linux, refer to your distributions package manager for the latest Redis package or, if you are so inclined, [download the source](http://redis.io/download) and build it yourself. 

## Installing `redli`

`redli` is an open source Redis command line client. It is stand-alone, mimics the redis-cli command line arguments, and adds support for TLS/SSL redis connections. It recognizes the `rediss:` protocol in URIs and  supports a `--tls` flag for non-URI connections. It can connect to TLS/SSL secured Redis without the need for tunnels. You can download and install it from the [releases page](https://github.com/IBM-Cloud/redli/releases). 

## Admin Connection Strings

Redis only supports a single-user, so you use the same credentials for all applications, including the command line clients, that need to connect to your deployment. Documentation for retrieving the deployment's connection strings is on the [Getting Connection Strings](./howto-getting-connection-strings.html) page. 

The information the clients need to make a connection to your deployment is in the "cli" section of your connection strings. The table contains a breakdown for reference.

Field Name|Index|Description
----------|-----|-----------
`Bin`||The recommended binary to create a connection; in this case it is `redli`.
`Composed`||A formatted command to establish a connection to your deployment. The command combines the `Bin` executable, `Environment` variable settings and uses `Arguments` as command line parameters.
`Environment`||A list of key/values you set as environment variables.
`Arguments`|0...|The information that is passed as arguments to the command shown in the Bin field.
`Certificate`|Base64|A self-signed certificate that is used to confirm that an application is connecting to the appropriate server. It is base64 encoded.
`Certificate`|Name|The allocated name for the self-signed certificate.
`Type`||The type of package that uses this connection information; in this case `cli`. 
{: caption="Table 1. `redis`/`cli` connection information" caption-side="top"}

* `0...` indicates that there might be one or more of these entries in an array.

You have to set the admin password before you connect to the database. For more information, see the [Setting the Admin Password](./howto-admin-password.html) page.
{: .tip}

## Connecting with `redli`

The `ibmcloud cdb deployment-connections` command handles everything that is involved in creating the client connection. For example, to connect to a deployment named  "NewRedis", use the following command.

```
ibmcloud cdb deployment-connections NewRedis --start
```
Or
```
ibmcloud cdb cxn NewRedis -s
```

The command prompts for the admin password and then runs the `redli` command line client to connect to the database.


If you have not installed the cloud databases plug-in, connect to your Redis databases using `redli` by giving it the "composed" connection string and the path to the self-signed certificate. 

```
redli -u
```

For more information, see the [GitHub repo](https://github.com/IBM-Cloud/redli).

## Connecting with `redis-cli`

`redis-cli` does not support TLS-enabled connections. If you want to use the `redis-cli` with an encrypted connection, you can set up a utility like [`stunnel`](https://www.stunnel.org/index.html), which wraps the `redis-cli` connection in TLS encryption.

### Setting up `stunnel`

1. Install `stunnel`. Use your package manager for Linux, Homebrew for Mac, or [download](https://www.stunnel.org/downloads.html) the appropriate package for your platform.

2. Grab connection information.
   To set up a connection, `stunnel` needs the host, the port, and the certificate of your Redis deployment. Host and port are both available from the CLI "composed" connection string. They can also be found parsed out in the [table of connection information](./howto-getting-connection-strings.html#the-redis-section) that is provided for connecting external applications and drivers.

   The certificate is in the  "Base 64" field of the connection information. Copy, decode, and save the certificate to a file.

3. Add your configuration information to the `stunnel.conf` file. The configuration includes the following information.
    - A name for a service. (`[redis-cli]`)
    - A setting that says this stunnel is a TLS client. (`client=yes`)
    - An IP address and port to accept connections on (`accept=127.0.0.1:6830`) and connect.
    - The host name and port we want to connect to. (`connect=`portal972-7.bmix-lon-yp-38898e17-ff6f-4340-9da8-2ba24c41e6d8.composeci-us-ibm-com.composedb.com:24370`)
    - The path to the certificate.
    
    ```text
    [redis-cli]
    client=yes  
    accept=127.0.0.1:6830  
    connect=sl-us-south-1-portal.7.dblayer.com:23870
    verify=2  
    checkHost=sl-us-south-1-portal.7.dblayer.com 
    CAfile=/path/to/redis/cert.crt
    ```

4. Run `stunnel`.

    Type the `stunnel` command at the command line. It immediately runs in the background.
    
5. In a new terminal window, run `redis-cli` pointing to the local host and port, and authenticate with the deployment's credentials.

    ```shell
    redis-cli -p 6830 -a <password>
    ```






