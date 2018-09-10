---
copyright:
  years: 2017,2018
lastupdated: "2018-09-10"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Administering your Redis databases

The {{site.data.keyword.databases-for-redis_full}} service is provisioned with authentication enabled. You will need a username and password to connect and issue commands. The username for Redis is always "admin".

## Prerequisites

Before starting, you will have to complete the following steps:

1. Set the admin user's password
2. Install a command line client for Redis, `redli` or `redis-cli`.

It is also recommended that you install the [{{site.data.keyword.cloud_notm}} CLI](https://console.{DomainName}/docs/cli/index.html#overview) and install the [cloud databases plugin](). These both make it easier to access connection information and connect to your Redis deployment.

## Setting the admin password

You have to set the admin password before you can use it to connect. To set the password through the {{site.data.keyword.cloud_notm}} dashboard, select _Manage_ from the service dashboard to open the management panel for your service. Open the _Settings_ tab, and use the _Change Password_ panel to set a new admin password.

### Setting the admin password via the command line

Use the `cdb user-password` command from the {{site.data.keyword.cloud_notm}} CLI cloud databases plugin to set the admin password with the command line.

For example, to set the admin password for a deployment named "example-deployment", use the following command.
```
ibmcloud cdb user-password example-deployment admin <newpassword>
```

### Setting the admin password via the API

The _Foundation Endpoint_ shown on the _Overview_ panel of your service provides the base URL to access this deployment through the API. Use it in conjunction with the `/users/admin` endpoint if you need to manage or automate setting the password programmatically.

For more information, see the [API Reference](https://pages.github.ibm.com/compose/apidocs/apiv4doc-static.html#operation/changeUserPassword).

## Connecting to `redli` via the cloud databases plugin

The `ibmcloud cdb deployment-connections` command handles everything involved in creating a command line client connection. For example, to connect to a deployment named  "NewRedis", use the following command.

```
ibmcloud cdb deployment-connections NewRedis -start
```

The command will prompt for the admin password and then run the `redli` command line client to connect to the database.

## Getting command line connection information

You can also connect to your Redis database directly from the command line clients. Retrieve connection information from the cloud databases plugin by using the `ibmcloud cdb deployment-connections` command to display a formatted connection URI. For example, to retrieve a connection string for a deployment named  "NewRedis", use the following command.
```
ibmcloud cdb deployment-connections NewRedis
```

Full connection information is returned by the `ibmcloud cdb deployment-connections` command with the `--all` flag. Retrieve all the connection information for a deployment named  "NewRedis", use the following command.

```
ibmcloud cdb deployment-connections NewRedis --all
```

The table describes the returned values.

Field Name|Index|Description
----------|-----|-----------
`Bin`||The recommended binary to create a connection; in this case it is `redli`.
`Composed`||A formatted command to establish a connection to your deployment. The command combines the `Bin` executable, `Environment` variable settings and uses `Arguments` as command line parameters.
`Environment`||`redli` arguments that can be set and pulled from the environment.
`Arguments`|0...|The information that is passed as arguments to the command shown in the Bin field.
`Certificate`|Base64|A self-signed certificate that is used to confirm that an application is connecting to the appropriate server. It is base64 encoded. You need to decode the key before using it.
`Certificate`|Name|The allocated name for the self-signed certificate.
`Type`||The type of package that uses this connection information; in this case `cli`. 
{: caption="Table 1. `cli` connection information for Redis" caption-side="top"}

The CLI connection information is also available in _Service Credentials_ if you have generated the admin credentials there. It is also available from the [cloud databases API]().
{: tip}

## Connecting with `redli`

`redli` is an open source Redis-cli application. It mimics the redis-cli command line arguments with support for rediss: protocols and a `--tls` flag. It can connect to TLS/SSL secured Redis without the need for tunnels. You can download and install it from the [releases page](https://github.com/IBM-Cloud/redli/releases). 

To connect to your Redis databases using `redli` by giving it the "composed" connection string and the path to the self-signed certificate. 
```
redli example command here
```
For more information, see the [GitHub repo](https://github.com/IBM-Cloud/redli).

## Using the self-signed certificate

The formatted `redis` command sets the option to verify the server via certificate upon connection. To use this feature, you need to complete the following steps.

1. Copy the certificate information from the Base64 field of the connection information. 
2. Decode the Base64 string into text and save it to a file. (You can use the Name provided or your own filename).
3. Provide the path to the certificate file to the connection string in the `ROOTCERT` environment variable.

### CLI plug-in support for the self-signed certificate

You can display the decoded certificate for your deployment with the CLI plug-in with the command `ibmcloud cdb deployment-cacert "your-service-name"`. It will decode the base64 into text. Copy and save the command's output to a file and provide the file's path to the `ROOTCERT` environment variable.

## Connecting with `redis-cli`

`redis-cli` does not support TLS-enabled connections. If you want to use the `redis-cli` with an encrypted connection, you can set up a utility like `stunnel`, which wraps the redis-cli connection in TLS encryption. The steps to set up [stunnel](https://www.stunnel.org/index.html) are:

1. Install `stunnel`. Use your package manager for Linux, Homebrew for Mac, or [download](https://www.stunnel.org/downloads.html) the appropriate package for your platform.

2. Grab connection information.
   To set up a connection, stunnel will need the the host, the port, and the certificate of your Redis deployment. Host and port are both available from the CLI "composed" connection string, but they can also be found parsed out in the [table of connection information](./connecting-external.html) provided for connecting external applications and drivers.

   The certificate is in the  "Base 64" field of the connection information. [Copy, decode, and save](#using-the-self-signed-certificate) the certificate to a file.

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











