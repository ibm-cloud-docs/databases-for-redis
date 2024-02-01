---
copyright:
  years: 2020, 2024
lastupdated: "2024-02-01"

keywords: acl, access control list, connection strings, admin, service credentials, new user, admin password, default user

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Managing Users and Roles
{: #user-management}

{{site.data.keyword.databases-for-redis_full}} instances come with authentication enabled and use Redis's built-in access control. Redis 5.x and older support only a single `admin` user. Redis 6 introduced [Access Control List (ACL) support](https://redis.io/topics/acl){: external}. Upgrade to take advantage of multiple users and authentication.

## The Admin user
{: #admin-user}

When you provision a new instance in {{site.data.keyword.cloud_notm}}, you are automatically given access to the Admin user. If you are using Redis 5.x and older, the Admin user is the only user available on your instance. If you are using Redis 6.x and newer, you have the Admin user and the ability to create users and credentials.

To use the Admin user to connect to your instance, first set the Admin password.

### Setting the Admin Password in the UI
{: #user-management-set-admin-password-ui}
{: ui}

Set your Admin Password through the UI by selecting your instance from the Resource List in the [{{site.data.keyword.cloud_notm}} Dashboard](https://cloud.ibm.com/){: external}. Then, select **Settings**. Next, select *Change Database Admin Password*.

### Setting the Admin Password in the CLI
{: #user-management-set-admin-password-cli}
{: cli}

Use the `cdb user-password` command from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin) to set the `admin` password.

For example, to set the Admin password for an instance named `example-instance`, use the following command:

```sh
ibmcloud cdb user-password example-instance admin <newpassword>
```
{: pre}

### Setting the Admin Password in the API
{: #user-management-set-admin-password-api}
{: api}

The Foundation Endpoint that is shown on the Overview panel Deployment Details section of your service provides the base URL to access this instance through the API. Use it with the [Set specified user's password](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#changeuserpassword){: external} endpoint to set the admin password.

```sh
curl -X PATCH `https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/admin` \
-H `Authorization: Bearer <>` \
-H `Content-Type: application/json` \
-d `{"password":"newrootpasswordsupersecure21"}` \
```
{: pre}

## The `default` user
{: #redis-default-user}

Before the arrival of [ACL support in Redis 6](https://redis.com/blog/getting-started-redis-6-access-control-lists-acls/){: external}, the `default` user had broad permissions and was used internally by {{site.data.keyword.databases-for}} and external users to manage {{site.data.keyword.databases-for-redis}} instances.

With Redis 6.x, {{site.data.keyword.databases-for-redis}} no longer uses the `default` user internally. {{site.data.keyword.databases-for-redis}} instances are managed by the {{site.data.keyword.databases-for}} `ibm` user.

### `default` user permissions
{: #redis-default-user-permissions}

If your instances currently use `DEFAULT` user (Redis 5.x), it's possible to continue doing so in the later versions, like 6.2. However, continued usage of the `DEFAULT` user after upgrading to v6.2 requires a password change, which is expected to limit permissions. These permission limitations are an expected behavioral change as part of a major version upgrade. Specifically, the following permissions are restricted for the `default` user, starting with Redis v6.2.

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

## Role-based access control (RBAC)
{: #redis-rbac}

Role-based access control (RBAC) allows you to configure the level of access each user has.

{{site.data.keyword.databases-for-redis}} currently supports the following roles:
- The `admin` role provides full control and access to all commands and operations.
- The `all` role provides both `read` and `write` access, giving users full control over all commands and operations.
- The `read` role allows read-only access to commands. Users with this role can execute read operations but cannot perform write operations.
- The `write` role allows write-only access to commands. Users with this role can execute write operations but cannot perform read operations.`

These roles can also be combined to configure a user's level of access.

### RBAC role combinations
{: #redis-rbac-combos}

- `admin` + `read`: Full control with the ability to execute read operations.
- `admin` + `write`: Full control with the ability to execute write operations.
- `admin` + `all`: Full control with the ability to execute both read and write operations.
- `read` + `write`: Users with both `read` and `write` roles can perform both read and write operations, but they won't have administrative privileges.
- `read` + `all`: Users with both `read` and `all` roles can execute read operations and have administrative privileges, but cannot perform write-only operations.
- `write` + `all`: Users with both `write` and `all` roles can execute write operations and have administrative privileges, but cannot perform read-only operations.
- `admin` + `read` + `write`: Full control with the ability to execute both read and write operations.
- `admin` + `read` + `all`: Full control with the ability to execute read operations and have administrative privileges, but cannot perform write-only operations.
- `admin` + `write` + `all`: Full control with the ability to execute write operations and have administrative privileges, but cannot perform read-only operations.
- `read` + `write` + `all`: Users with all three roles have full control with the ability to execute both read and write operations, but they won't have additional administrative privileges beyond those provided by the `admin` role.

These combinations provide different levels of access control. Choose the combination that aligns with your specific requirements and security considerations.

### Create a user through the API
{: #redis-rba-create-user-api}
{: api}

To create a user using RBAC roles, use a command like:

```sh
Create User

POST /deployments/{id}/users/{user_type}
{
  "user": {
  "username": "#{USER}",
  "password": "<PASSWORD>",
  "role": "<+/-@all> <+/-@read> <+/-write> <+/-admin>"
  }
}
```
{: pre}

### Create a user through the CLI
{: #redis-rba-create-user-cli}
{: cli}

To create a user using RBAC roles, use a command like:

```sh
ibmcloud cdb deployment-user-create (NAME\ID) USERNAME PASSWORD [-t USER_TYPE] [-r USER_ROLE] [--json] [--api-version]
```
{: pre}

For `USER_ROLE`, use some combination of `<+/-@all>` `<+/-@read>` `<+/-write>` `<+/-admin>` to grant desired access.

### Create a user through Terraform
{: #redis-rba-create-user-tf}
{: tf}

To create a user using RBAC roles, use a configuration like:

```terraform
resource "ibm_database" "redis" {
  name = "example-redis"
  plan = "standard"
  location = "us-south"
  service = "databases-for-redis"
  resource_group_id = data.ibm_resource_group.group.id
  tags = ["tag1", "tag2"]
  version = "6"

  users {
    name = "coolguy"
    password = "securepassword123"
    role = "-@all +@read"
  }
}
```
{: pre}

For `role`, use some combination of `<+/-@all>` `<+/-@read>` `<+/-write>` `<+/-admin>` to grant desired access.


## Redis roles
{: #redis-roles}

The Admin user and all other users on your instance have full access to the set of redis commands, except for the subcommand `configure set` - this includes the `admin` user.

In Redis 6.x and newer, any user that you create; whether through _Service Credentials_, the CLI, API, or directly in Redis; have the same access. You cannot use Redis itself to create users or roles with access that is limited to specific keys or ranges of keys, as they are not propagated automatically in a cluster deployment. All other means to manage users ensure propagation across the cluster.

## Additional Users - Redis 6.x only
{: #add-users}

Access to your {{site.data.keyword.databases-for-redis}} instance is not limited to the admin user. You can create users by using the _Service Credentials_ panel, the {{site.data.keyword.IBM_notm}} CLI, or through the {{site.data.keyword.IBM_notm}} {{site.data.keyword.databases-for}} API.

All users on your instance can use the connection strings, including connection strings for either public or private endpoints.

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

If you manage your service through the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin), create a new user with `cdb user-create`. For example, to create a new user for an "example-instance", use the following command:

```sh
ibmcloud cdb user-create example-instance <newusername> <newpassword>
```
{: pre}

Once the task is finished, retrieve the new user's connection strings with the `ibmcloud cdb deployment-connections` command.

### Creating Users from the API
{: #create-users-api}
{: api}

The _Foundation Endpoint_ that is shown on the _Overview_ panel of your service provides the base URL to access this instance through the API. To create and manage users, use the base URL with the `/users` endpoint:

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

There are four reserved users on your instance. Modifying these users causes your instance to become unstable or unusable.
- `ibm` - An internal `admin` user for managing the instance and exposing metrics.
- `replication-user` - The user account that is used for replication.
- `sentinel-user` - The user account for sentinels to handle monitoring and failovers.
- `admin` - The default user provided to access your instance.
