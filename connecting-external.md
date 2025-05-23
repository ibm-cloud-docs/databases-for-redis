---
copyright:
  years: 2018, 2025
lastupdated: "2025-05-14"

keywords: redis, databases

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Connecting an external application
{: #external-app}

Your applications and drivers use connection strings to make a connection to {{site.data.keyword.databases-for-redis_full}}. The service provides connection strings specifically for drivers and applications. Connection strings are displayed in the *Endpoints* panel of your deployment's *Overview* page, and can also be retrieved from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-connections), and the [{{site.data.keyword.databases-for}} API](/apidocs/cloud-databases-api/cloud-databases-api-v5#getconnection).

The connection strings can be used by any of the credentials you create in your deployment. While you can use the admin user for all of your connections and applications, it might be better to create users specifically for your applications to connect with. For more information, see [Getting Connection Strings](/docs/databases-for-redis?topic=databases-for-redis-connection-strings).

## Connection strings for applications
{: #connection-strings-application}

All the information a driver needs to make a connection to your deployment is in the "redis" section of a credential created on the *Service credentials* page. The table contains a breakdown for reference.

| Field name | Index | Description |
| ---------- | ----- | ----------- |
| `Type` | | Type of connection - for Redis, it is "URI". |
| `Scheme` | | Scheme for a URI - for Redis, it is "rediss". |
| `Path` | | Path for a URI - for Redis, it is the database number. |
| `Authentication` | `Username` | The username that you use to connect. |
| `Authentication` | `Password` | A password for the user - might be shown as `$PASSWORD`. |
| `Authentication` | `Method` | How authentication takes place; "direct" authentication is handled by the driver. |
| `Hosts` | `0...` | A hostname and port to connect to. |
| `Composed` | `0...` | A URI combining scheme, authentication, host, and path. |
| `Certificate` | `Name` | The allocated name for the service proprietary certificate for database deployment. |
| `Certificate` | Base64 | A base64 encoded version of the certificate. |
{: caption="redis/URI connection information" caption-side="top"}

* `0...` indicates that there might be one or more of these entries in an array.

Redis drivers are often able to make a connection to your deployment when given the URI-formatted connection string found in the "composed" field of the connection information. For example, if you set the connection string in the environment variable `REDIS_URL`, as in the following example:

```sh
export REDIS_URL=rediss://admin:$PASSWORD@e6b2c3f8-54a6-439e-8d8a-aa6c4a78df49.8f7bfd8f3faa4218aec56e069eb46187.databases.appdomain.cloud:32371/0
```
{: pre}

Then, the Node.js client is able to make a connection with.

```sh
let connectionString = process.env.REDIS_URL;

if (connectionString === undefined) {  
  console.error("Please set the REDIS_URL environment variable");
  process.exit(1);
}

let client = null;

client = redis.createClient(connectionString, {
  tls: { servername: new URL(connectionString).hostname }
});
```

Alternatively, the connection string can be parsed and its parts sent to the connection handler, as with the following Python client example:

```python
parsed = urlparse(connection_string)

r = redis.StrictRedis(
    host=parsed.hostname,
    port=parsed.port,
    password=parsed.password,
    ssl=True,
    ssl_ca_certs='/etc/ssl/certs/ca-certificates.crt',
    decode_responses=True)
```
{: pre}

Redis has an array of clients for applications to use. A fairly [comprehensive list is maintained on the Redis site](https://redis.io/clients){: external}. Some useful things to keep in mind when choosing a client are features that allow you to easily design your application for the cloud, like configuring [high-availability](/docs/databases-for-redis?topic=databases-for-redis-high-availability), security, and service proprietary certificate support.

## TLS and service proprietary certificate support
{: #tls-cert-support}

All connections to {{site.data.keyword.databases-for-redis}} are TLS 1.2 enabled, so the driver you use to connect need to be able to support TLS encryption.

If your driver does not support the `rediss:` protocol or TLS/SSL connections, it is still possible to tunnel connections to the Redis database endpoint by using a TLS/SSL tunnel application such as Stunnel. An example of using Stunnel can be found on the [Connecting with a command-line client](/docs/databases-for-redis?topic=databases-for-redis-connecting-cli-client) page, where it is used to connect the `redis-cli` application.

Deployments also come with a service proprietary certificate so you can verify the server upon starting a connection. While not required, it is an additional security step that is recommended if your client supports it.

For more information, see [{{site.data.keyword.databases-for}} Certificates FAQ](/docs/databases-for-redis?topic=databases-for-redis-faq-cert){: external}.

### Using the service proprietary certificate
{: #using-cert}

1. Copy the certificate information from the _Endpoints_ panel or the Base64 field of the service credential connection information.
2. If needed, decode the Base64 string into text.
3. Save the certificate  to a file. (You can use the Name that is provided or your own file name).
4. Provide the path to the certificate to the driver or client.

### CLI plug-in support for the service proprietary certificate
{: #cli-support-cert}

You can display the decoded certificate for your deployment with the CLI plug-in with a command like:

```sh
ibmcloud cdb deployment-cacert <INSTANCE_NAME_OR_CRN>
```
{: pre}

This command decodes the Base64 into text. Copy and save the command's output to a file and provide the file's path to the client.
