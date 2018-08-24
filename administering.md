---
copyright:
  years: 2017,2018
lastupdated: "2017-08-24"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Administering your Redis databases

The {{site.data.keyword.databases-for-redis_full}} service is provisioned with authentication enabled, so you will need a username and password to connect and issue commands.

To get started, you will have to complete the following steps:

1. Set the admin credentials
2. Retrieve the connection strings and other connection information
3. Decode and save a copy of the self-signed certificate

## Setting the admin password

You have to set the admin password before you can use it to connect. To set the password through the {{site.data.keyword.cloud_notm}} dashboard, select _Manage_ from the service dashboard to open the management panel for your service. Open the _Settings_ tab, and use the _Change Password_ panel to set a new admin password.

You can also set the admin user password through the API. Send a `PATCH` request to the `https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users/admin` endpoint.

```
curl -X PATCH "https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users/admin" \
-H "Authorization: Bearer $APITOKEN" \
-H "Content-Type: application/json; charset=utf-8" \
-d \
  '{
  "user": 
    {
      "password":"lkdfj1ieo4fhhelk5aei2efjdsa"
    }
  }'
```

For more information, see the [API Reference](https://pages.github.ibm.com/compose/apidocs/apiv4doc-static.html#operation/changeUserPassword).

## Connecting with command-line tools


### Getting the connection string with the API

To retrieve the admin user's connection strings through the API, send a GET request to `https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users/admin`. The JSON response includes the strings to connect to `redli` in "cli" field.

The `redis` connection information table describes the sub-fields of connection information.

Field Name|Description
----------|-----------
`arguments`|The information that is passed as arguments to the `redis` command,
`bin`|The package that this information is intended for; in this case `redis`.
`certificate`|A self-signed certificate that is used to confirm that an application is connecting to the appropriate server. This is base64 encoded. You need to decode the key before using it.
`composed`|A formatted `rediss` command to establish a connection to your deployment.
`environment`|`redis` arguments that can be set and pulled from the environment.
`type`|The type of package that uses this connection information; in this case `cli`. 
{: caption="Table 1. `redis` connection information" caption-side="top"}

## Connecting with `redli`

`redli` is an open-source Redis-cli application. It mimics the redis-cli command line arguments with support for rediss: protocols and a `--tls` flag. It can connect to TLS/SSL secured Redis without the need for tunnels. You can download and install it from the [releases page](https://github.com/IBM-Cloud/redli/releases). 

To connect to your Redis databases using `redli` by giving it the "composed" connection string and the path to the self-signed certificate. 
```
redli example command here
```
For more information, see the [GitHub repo](https://github.com/IBM-Cloud/redli).


### Connecting to 'redli' with the CLI plugin

The {{site.data.keyword.cloud_notm}} CLI cloud databases plugin provides the admin user's connection string in URI format with the command: `ibmcloud dbs deployment-connections "your-service-name"`.

You can also connect to `redli` from the cloud databases plugin with the admin user with `ibmcloud cdb deployment-connections "your-service-name" -u admin --start`. Enter the admin password when prompted.

## Using the self-signed certificate

The formatted `redis` command sets the option to verify the server via certificate upon connection. To use this feature, you need to complete the following steps:

1. Download and save a copy of the certificate
2. Decode the certificate from base64 format to the .pem certificate format.
3. Provide the path to the certificate to the connection string in the `ROOTCERT` environment variable.

### CLI plugin support for the self-signed certificate

You can display the decoded certificate for your deployment with the CLI plugin with the `ibmcloud cdb deployment-cacert "your-service-name" command. Copy and save the command's output to a file and provide the file's path to the `ROOTCERT` environment variable.

## Connecting with `redis-cli`

To use the `redis-cli` with an encrypted connection set up a utility like `stunnel`, which can wrap the redis-cli connection in TLS encryption. The steps to set up [stunnel](https://www.stunnel.org/index.html) are:

1. Install `stunnel`. Use your package manager for Linux, Homebrew for Mac, or [download](https://www.stunnel.org/downloads.html) the appropriate package for your platform.

2. Parse the **Connection String**.
   
    ```text
    rediss://admin:PASSWORD@portal972-7.bmix-lon-yp-38898e17-ff6f-4340-9da8-2ba24c41e6d8.composeci-us-ibm-com.composedb.com:24370
    ```

    - The password is the text between the second colon and at-symbol: `PASSWORD`.
    - The host is the text after the @ and up to the next colon: `portal972-7.bmix-lon-yp-38898e17-ff6f-4340-9da8-2ba24c41e6d8.composeci-us-ibm-com.composedb.com`.
    - The port is the number following that colon: `24370`.

3. Add your configuration information to the `stunnel.conf` file. The configuration includes the following information.
    - A name for a service (`[redis-cli]`)
    - A setting that says this stunnel is a TLS client (`client=yes`)
    - An IP address and port to accept connections on (`accept=127.0.0.1:6830`) and connect
    - The host name and port we want to connect to (`connect=`portal972-7.bmix-lon-yp-38898e17-ff6f-4340-9da8-2ba24c41e6d8.composeci-us-ibm-com.composedb.com:24370`)
    
    You need to get the certificate information and copy it all to a text file. Then, add the path to this certificate information to the `stunnel.conf` file.
    
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
    
5. In a new Terminal window, run `redis-cli` pointing to the local host and port, and authenticate with the deployment's credentials.

    ```shell
    redis-cli -p 6830 -a <password>
    ```











