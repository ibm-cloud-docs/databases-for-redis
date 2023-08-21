---
copyright:
  years: 2020, 2023
lastupdated: "2023-08-21"

keywords: acl, access control list, connection strings, admin, service credentials, new user, admin password, default user

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Managing Users and Roles
{: #user-management}

{{site.data.keyword.databases-for-redis_full}} deployments come with authentication enabled and use Redis's built-in access control. Redis 5.x and older support only a single `admin` user. Redis 6 introduced [Access Control List (ACL) support](https://redis.io/topics/acl){: external}. Upgrade to take advantage of multiple users and authentication.

## The `admin` user
{: #admin-user}

When you provision a new deployment in {{site.data.keyword.cloud_notm}}, you are automatically given access to the `admin` user. If you are using Redis 5.x and older, the `admin` user is the only user available on your deployment. If you are using Redis 6.x and newer, you have the `admin` user and the ability to create users and credentials.

To use the `admin` user to connect to your deployment, set the `admin` password.

### Setting the Admin Password in the UI
{: #user-management-set-admin-password-ui}
{: ui}

To set the password through the {{site.data.keyword.cloud_notm}} dashboard, select __Manage__ from the service dashboard. Open the _Settings_ tab, and use _Change Database Admin Password_ to set a new admin password.

### Setting the Admin Password in the CLI
{: #user-management-set-admin-password-cli}
{: cli}

Use the `cdb user-password` command from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin) to set the `admin` password.

For example, to set the admin password for a deployment named `example-deployment`, use the following command:

```sh
ibmcloud cdb user-password example-deployment admin <newpassword>
```
{: pre}

### Setting the Admin Password in the API
{: #user-management-set-admin-password-api}
{: api}

The Foundation Endpoint that is shown on the Overview panel Deployment Details section of your service provides the base URL to access this deployment through the API. Use it with the [Set specified user's password](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#changeuserpassword){: external} endpoint to set the admin password.

```sh
curl -X PATCH `https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/admin` \
-H `Authorization: Bearer <>` \
-H `Content-Type: application/json` \ 
-d `{"password":"newrootpasswordsupersecure21"}` \
```
{: pre}

## The `default` user
{: #redis-default-user}

Before the arrival of [ACL support in Redis 6](https://redis.com/blog/getting-started-redis-6-access-control-lists-acls/){: external}, the `default` user had broad permissions and was used internally by {{site.data.keyword.databases-for}} and external users to manage {{site.data.keyword.databases-for-redis}} deployments.

With Redis 6.x, {{site.data.keyword.databases-for-redis}} no longer uses the `default` user internally. {{site.data.keyword.databases-for-redis}} deployments are managed by {{site.data.keyword.databases-for}} the `ibm` user. 

### `default` user permissions
{: #redis-default-user-permissions}

If your deployments currently use `DEFAULT` user (Redis 5.x), it's possible to continue doing so in the later versions, like 6.2. However, continued usage of the `DEFAULT` user after upgrading to v6.2 requires a password change, which is expected to limit permissions. These permission limitations are an expected behavioral change as part of a major version upgrade. Specifically, the following permissions are restricted for the `default` user, starting with Redis v6.2.

- `config` The `default` user cannot view, add, update, or delete database configurations. The `default` user also cannot create or manage database users and roles.
- `acl` The `default` user cannot create new users.

### Upgrading to Redis 6.2 and the `default` user 
{: #redis-default-user-permissions-upgrading}

If you are using Redis 5, upgrade directly to Redis 6.2. After upgrading, thoroughly test your applications by using the `default` user to make sure your applications are fully functional. Upgrading to v6.2 and continuing to use the `DEFAULT` user requires a password change.

To update the `default` user password, use the following command:

```sh
ibmcloud cdb deployment-user-password <Redis deployment name> default <new password>
```
{: pre}

For more information, see [Upgrading to a new major version](/docs/databases-for-redis?topic=databases-for-redis-upgrading){: external}.

## Redis roles
{: #redis-roles}

The `admin` user and all other users on your deployment have full access to the set of redis commands, except for the subcommand `configure set` - this includes the `admin` user.

In Redis 6.x and newer, any user that you create; whether through _Service Credentials_, the CLI, API, or directly in Redis; have the same access. You cannot use Redis itself to create users or roles with access that is limited to specific keys or ranges of keys, as they are not propagated automatically in a cluster deployment. All other means to manage users ensure propagation across the cluster.

## Additional Users - Redis 6.x only
{: #add-users}

Access to your {{site.data.keyword.databases-for-redis}} deployment is not limited to the admin user. You can create users by using the _Service Credentials_ panel, the {{site.data.keyword.IBM_notm}} CLI, or through the {{site.data.keyword.IBM_notm}} {{site.data.keyword.databases-for}} API. 

All users on your deployment can use the connection strings, including connection strings for either public or private endpoints. 

### Creating Users From the _Service Credentials_ UI
{: #create-users-service-cred}
{: ui}

1. Navigate to the service dashboard for your service.
2. Click _Service Credentials_ to open the _Service Credentials_ panel.
3. Click __New Credential**.
4. Choose a descriptive name for your new credential. 
5. (Optional) Specify whether the new credentials use a public or private endpoint. Use either `{ "service-endpoints": "public" }` / `{ "service-endpoints": "private" }` in the _Add Inline Configuration Parameters_ field to generate connection strings using the specified endpoint. Use of the endpoint is not enforced. It just controls which hostnames are in the connection strings. Public endpoints are generated by default.
6. Click __Add__ to provision the new credentials. A username and password, and an associated {{site.data.keyword.databases-for-redis}} user is auto-generated.

The new credentials appear in the table, and the connection strings are available as JSON in a click-to-copy field under _View Credentials_.

### Creating Users From the Command Line
{: #create-users-cli}
{: cli}

If you manage your service through the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin), create a new user with `cdb user-create`. For example, to create a new user for an "example-deployment", use the following command:

```sh
ibmcloud cdb user-create example-deployment <newusername> <newpassword>
```
{: pre}

Once the task is finished, retrieve the new user's connection strings with the `ibmcloud cdb deployment-connections` command.

### Creating Users from the API
{: #create-users-api}
{: api}

The _Foundation Endpoint_ that is shown on the _Overview_ panel of your service provides the base URL to access this deployment through the API. To create and manage users, use the base URL with the `/users` endpoint:

```sh
curl -X POST 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"username":"jane_smith", "password":"newsupersecurepassword"}'
```
{: pre}

To retrieve a user's connection strings, use the base URL with the `/users/{userid}/connections` endpoint.

## Internal-use Users - Redis 6.x only
{: #internal-users}

There are four reserved users on your deployment. Modifying these users causes your deployment to become unstable or unusable.
- `ibm` - An internal `admin` user for managing the deployment and exposing metrics.
- `replication-user` - The user account that is used for replication.
- `sentinel-user` - The user account for sentinels to handle monitoring and failovers.
- `admin` - The default user provided to access your deployment.
