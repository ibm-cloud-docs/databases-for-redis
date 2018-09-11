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


# Getting your Connection Strings

The {{site.data.keyword.databases-for-redis_full}} service is provisioned with authentication enabled. You need a username and password to connect and issue commands. The username for Redis is always "admin".

You have to set the admin password before connecting to the database For more information, see the [Setting the Admin Password](./admin-password.html) page.
{: .tip}

The simplest way to retrieve connection information is from the [cloud databases plug-in](./working-cli.html#the-cloud-databases-plugin). Use the `ibmcloud cdb deployment-connections` command to display a formatted connection URI for your deployment. For example, to retrieve a connection string for a deployment named  "NewRedis", use the following command.

```
ibmcloud cdb deployment-connections NewRedis
```
Or
```
ibmcloud cdb cxn NewRedis
```

Full connection information is returned by the `ibmcloud cdb deployment-connections` command with the `--all` flag. Retrieve all the connection information for a deployment named  "NewRedis", use the following command.

```
ibmcloud cdb deployment-connections NewRedis --all
```

## Other ways of accessing the connection strings

It is possible to make the admin connection information available the in _Service Credentials_ panel. The resulting "CLI" JSON object contains the same fields as Table 1. For more information, see the [Connecting an External Application](./connecting-external.html) page

The information in Table 1 is also available as JSON from the cloud databases API. The _Foundation Endpoint_ that is shown on the _Overview_ panel of your service provides the base URL to access this deployment through the API. Use it with the `/users/admin` endpoint to retrieve the admin connection strings. For more information, see the [API Reference]().

## Connection String Breakdown

### The Redis Section

The "Redis" section contains information suited to applications making connections to Redis.

Field Name|Index|Description
----------|-----|-----------
`Type`||Type of connection - for Redis, it is "URI"
`Scheme`||Scheme for a URI - for Redis, it is "rediss"
`Path`||Path for a URI - for Redis, it is the database number
`Authentication`|`Username`|A username that should be used when connecting.
`Authentication`|`Password`|A password for the user - might be shown as `$PASSWORD`
`Authentication`|`Method`|How authentication takes place; "direct" authentication is handled by the driver.
`Hosts`|`0...`|A hostname and port to connect to
`Composed`|`0...`|A URI combining Scheme, Authentication, Host, and Path
`Certificate`|`Name`|The allocated name for the self-signed certificate for database deployment
`Certificate`|Base64|A base64 encoded version of the certificate.
{: caption="Table 1. `redis`/`URI` connection information" caption-side="top"}

### The CLI Section

The "CLI" section contains information suited for command-line clients making connections to Redis.

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

## Using the self-signed certificate

1. Copy the certificate information from the Base64 field of the connection information. 
2. Decode the Base64 string into text and save it to a file. (You can use the Name that is provided or your own file name).

### CLI plug-in support for the self-signed certificate

You can display the decoded certificate for your deployment with the CLI plug-in with the command `ibmcloud cdb deployment-cacert "your-service-name"`. It decodes the base64 into text. Copy and save the command's output to a file and provide the file's path to the `ROOTCERT` environment variable.
