---
copyright:
  years: 2017, 2024
lastupdated: "2024-10-17"

keywords: redis, databases, update, client, pub/sub

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Connecting through the command-line interface (CLI)
{: #connecting-cli-client}

Access your Redis database directly from a command-line interface (CLI). The CLI allows for direct interaction and monitoring of the data structures that are created within the database. It is also useful for administering and monitoring the keyspace and performance, installing and modifying scripts, and other management activities.

The `redli` client needs to be updated for the user management features introduced in [Redis 6](https://github.com/IBM-Cloud/redli/releases){: external}. If you try to connect to without updating the client, you see an error like: `(error) WRONGPASS invalid username-password pair`. 
{: .note}

## Connection strings
{: #connection-strings-cli}

Connection strings are displayed in the _Endpoints_ panel of your deployment's _Overview_ page, and can also be retrieved from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-connections), and the [{{site.data.keyword.databases-for}} API](/apidocs/cloud-databases-api/cloud-databases-api-v5#getconnection).

The information the clients need to connect to your deployment is in the "CLI" section of a credential created on the *Service credentials* page. The table contains a breakdown for reference.

| Field name | Index | Description |
| ---------- | ----- | ----------- |
| `Bin` | | The recommended binary to create a connection; in this case it is `redli`. |
| `Composed` | | A formatted command to establish a connection to your deployment. The command combines the `Bin` executable, `Environment` variable settings and uses `Arguments` as command-line parameters. |
| `Environment` | | A list of key/values you set as environment variables. |
| `Arguments` | 0... | The information that is passed as arguments to the command shown in the Bin field. |
| `Certificate` | Base64 | A self-signed certificate that is used to confirm that an application is connecting to the appropriate server. It is base64 encoded. |
| `Certificate` | Name | The allocated name for the self-signed certificate. |
| `Type` | | The type of package that uses this connection information; in this case `cli`. |
{: caption="`redis`/`cli` connection information" caption-side="top"}

* `0...` indicates that there might be one or more of these entries in an array.

## Installing `redli`
{: #install-redli}

`redli` is an open source Redis command-line client. It is stand-alone, mimics the redis-cli command-line arguments, and adds support for TLS/SSL Redis connections. It recognizes the `rediss:` protocol in URIs and supports a `--tls` flag for non-URI connections. It can connect to TLS/SSL secured Redis without the need for tunnels. Download and install it from the [releases page](https://github.com/IBM-Cloud/redli/releases){: external}. 

### Connecting with `redli`
{: #connection-redli}

The `ibmcloud cdb deployment-connections` command handles everything that is involved in creating the client connection. For example, to connect to a deployment named "NewRedis", use the following command.

```sh
ibmcloud cdb deployment-connections NewRedis --start
```
{: pre}

or

```sh
ibmcloud cdb cxn NewRedis -s
```
{: pre}

The command prompts for the `admin` password and then runs the `redli` command-line client to connect to the database.

If you have not installed the cloud databases plug-in, connect to your Redis databases with the `redli` command. Download and save the self-signed certificate from your deployment. Then, use `redli` by giving it the "composed" connection string and the path to the self-signed certificate. 

```sh
redli --uri rediss://admin:$PASSWORD@e6b2c3f8-54a6-439e-8d8a-aa6c4a78df49.8f7bfd8f3faa4218aec56e069eb46187.databases.appdomain.cloud:32371/0 --certfile /path/to/redis-cert.pem
```

There are other connection options and parameters that are supported by `redli`. For more information, see its documentation in the [`redli` GitHub repo](https://github.com/IBM-Cloud/redli){: external}.

## Installing `redis-cli`
{: #installing-redis-cli}

`redis-cli` is the official supported command-line interface for Redis. Unfortunately, it does not support TLS connections.

If you choose to use `redis-cli`, there are some extra configuration steps. It comes as part of the Redis package, so you need Redis installed locally to use it. On macOS, install [brew](http://brew.sh) and then use `brew install redis` to get up and running. On Linux, refer to your distributions package manager for the latest Redis package or, if you are so inclined, [download the source](http://redis.io/download) and build it yourself. 

### Connecting with `redis-cli`
{: #connecting-with-redis-cli}

`redis-cli` does not support TLS-enabled connections. If you want to use the `redis-cli` with an encrypted connection, set up a utility like [`stunnel`](https://www.stunnel.org/index.html), which wraps the `redis-cli` connection in TLS encryption.

### Setting up `stunnel`
{: #setting-up-stunnel}

1. Install `stunnel`. Use your package manager for Linux, Homebrew for Mac, or [download](https://www.stunnel.org/downloads.html) the appropriate package for your platform.

2. Grab connection information.
   To set up a connection, `stunnel` needs the host, the port, and the certificate of your Redis deployment. Host and port are both available from the CLI "composed" connection string. They can also be found parsed out in the [Connection Strings Breakdown](/docs/databases-for-redis?topic=databases-for-redis-connection-strings&interface=cli#connection-strings-breakdown) that is provided for connecting external applications and drivers.

   The certificate is in the Base64 field of the service credential connection information. Copy, decode, and save the certificate to a file.

3. Add your configuration information to the `stunnel.conf` file. The configuration includes the following information.
    - A name for a service. (`[redis-cli]`)
    - A setting that says this stunnel is a TLS client. (`client=yes`)
    - An IP address and port to accept connections on (`accept=127.0.0.1:6830`) and connect.
    - The host name and port to connect to. (`connect=`portal972-7.bmix-lon-yp-38898e17-ff6f-4340-9da8-2ba24c41e6d8.composeci-us-ibm-com.composedb.com:24370`)
    - The path to the certificate.
    
    ```sh
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

    ```sh
    redis-cli -p 6830 -a <password>
    ```
    {: pre}

## Using the self-signed certificate
{: #using-cert}

1. Copy the certificate information from the _Endpoints_ panel or the Base64 field of the service credential connection information.
2. If needed, decode the Base64 string into text.
3. Save the certificate  to a file. (You can use the Name that is provided or your own file name).
4. Provide the path to the certificate to the driver or client.

## CLI plug-in support for the self-signed certificate
{: #cli-support-cert}

You can display the decoded certificate for your deployment with the CLI plug-in with a command like:

```sh
ibmcloud cdb deployment-cacert <SERVICE_NAME>
```
{: pre}

This command decodes the Base64 into text. Copy and save the command's output to a file and provide the file's path to the client.
