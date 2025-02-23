---
copyright:
  years: 2020, 2024
lastupdated: "2024-12-06"

keywords: acl, access control list, connection strings, admin, service credentials, new user, admin password, default user, rbac

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Managing users and roles
{: #user-management}

{{site.data.keyword.databases-for-redis_full}} instances come with authentication enabled and use Redis's built-in access control. Redis 5.x and older support only a single `admin` user. Redis 6 introduced [Access Control List (ACL) support](https://redis.io/topics/acl){: external}. Upgrade to take advantage of multiple users and authentication.

## Managing Redis users
{: #user-management-users}

### The admin user
{: #admin-user}

When you provision a new instance in {{site.data.keyword.cloud_notm}}, you are automatically given access to the Admin user. If you are using Redis 5.x and older, the Admin user is the only user available on your instance. If you are using Redis 6.x and newer, you have the Admin user and the ability to create users and credentials.

To use the Admin user to connect to your instance, first set the Admin password.

#### Setting the admin password in the UI
{: #user-management-set-admin-password-ui}
{: ui}

Set your Admin password through the UI by selecting your instance from the Resource List in the [{{site.data.keyword.cloud_notm}} Dashboard](https://cloud.ibm.com/){: external}. Then, select **Settings**. Next, select *Change Database Admin Password*.

#### Setting the admin password in the CLI
{: #user-management-set-admin-password-cli}
{: cli}

Use the `cdb user-password` command from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin) to set the `admin` password.

For example, to set the admin password for your instance, use the following command:

```sh
ibmcloud cdb user-password <INSTANCE_NAME_OR_CRN> admin <NEWPASSWORD>
```
{: pre}

#### Setting the admin password in the API
{: #user-management-set-admin-password-api}
{: api}

The Foundation Endpoint that is shown in the Overview Deployment Details section of your service provides the base URL to access this instance through the API. Use it with the [Set specified user's password](/apidocs/cloud-databases-api/cloud-databases-api-v5#updateuser){: external} endpoint to set the admin password.

```sh
curl -X PATCH `https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/admin` \
-H `Authorization: Bearer <>` \
-H `Content-Type: application/json` \
-d `{"password":"newrootpasswordsupersecure21"}` \
```
{: pre}

### The `default` user
{: #redis-default-user}

Before the arrival of [ACL support in Redis 6](https://redis.com/blog/getting-started-redis-6-access-control-lists-acls/){: external}, the `default` user had broad permissions and was used internally by {{site.data.keyword.databases-for}} and external users to manage {{site.data.keyword.databases-for-redis}} instances.

With Redis 6.x, {{site.data.keyword.databases-for-redis}} no longer uses the `default` user internally. Instead, {{site.data.keyword.databases-for-redis}} instances are managed by the {{site.data.keyword.databases-for}} `ibm-user`.

If your instances currently use the `default` user (Redis 5.x), it's possible to continue doing so in the later versions, like 6.2. However, continued usage of the `default` user after upgrading to v6.2 requires a password change, which is expected to limit permissions. These permission limitations are an expected behavioral change as part of a major version upgrade. Specifically, the following permissions are restricted for the `default` user, starting with Redis v6.2:

- `config`: The `default` user cannot view, add, update, or delete database configurations. The `default` user also cannot create or manage database users and roles.
- `acl`: The `default` user cannot create new users.

#### Upgrading to Redis 6.2 and the `default` user
{: #redis-default-user-permissions-upgrading}

If you are using Redis 5, upgrade directly to Redis 6.2. After upgrading, thoroughly test your applications by using the `default` user to make sure that your applications are fully functional. Upgrading to v6.2 and continuing to use the `default` user requires a password change.

To update the `default` user password, use a command like:

```sh
ibmcloud cdb deployment-user-password <INSTANCE_NAME_OR_CRN> default <NEW PASSWORD>
```
{: pre}

For more information, see [Upgrading to a new major version](/docs/databases-for-redis?topic=databases-for-redis-upgrading){: external}.

## Managing Redis roles
{: #user-management-roles}

### Role-based access control (RBAC)
{: #redis-rbac}

Role-based access control (RBAC) allows you to configure the level of access each user has.

{{site.data.keyword.databases-for-redis}} currently supports the following roles:

- The `admin` role provides full control and access to all admin commands and operations.
- The `all` role provides `admin`, `read` and `write` access, giving users full control over all commands and operations.
- The `read` role allows read-only access to commands. Users with this role can run read operations but cannot perform write operations.
- The `write` role allows write-only access to commands. Users with this role can run write operations but cannot perform read operations.

The admin commands `config get`, `config reset`, `acl whoami`, `acl cat`, `acl users`, `acl genpass`, `acl log`, and `acl help` are available for use in `admin` and `all` but all other `acl` and `config` commands are not.

These roles can also be combined to configure a user's level of access.

#### RBAC role combinations
{: #redis-rbac-combos}

`+` includes commands

`-` excludes commands

`+@` includes command categories

`-@` excludes command categories

- `admin` + `read`: Full `admin` control with the ability to run `read` operations.
- `admin` + `write`: Full `admin` control with the ability to run `write` operations.
- `admin` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `read` + `write`: Users with both `read` and `write` roles can perform both `read` and `write` operations, but they won't have administrative privileges.
- `read` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `write` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `admin` + `read` + `write`: Full `admin` control with the ability to run both `read` and `write` operations.
- `admin` + `read` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `admin` + `write` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.
- `read` + `write` + `all`: Simplifies to `all`, which includes `admin`, `read`, and `write`.

These combinations provide different levels of access control. Choose the combination that aligns with your specific requirements and security considerations.

### Create a user with RBAC roles through the API
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
  "role": "<+/-@all> <+/-@read> <+/-@write> <+/-@admin>"
  }
}
```
{: pre}

### Create a user with RBAC roles through the CLI
{: #redis-rba-create-user-cli}
{: cli}

To create a user using RBAC roles, use a command like:

```sh
ibmcloud cdb deployment-user-create <INSTANCE_NAME_OR_CRN> <USERNAME> <PASSWORD> -r "+@read +@write"
```
{: pre}

For `-r USER_ROLE`, use some combination of `<+/-@all>` `<+/-@read>` `<+/-@write>` `<+/-@admin>` to grant access.

Once the task is finished, retrieve the new user's connection strings with the `ibmcloud cdb deployment-connections` command.

### Create a user with RBAC roles through Terraform
{: #redis-rba-create-user-tf}
{: terraform}

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
    name = "<user_name>"
    password = "securepassword123"
    role = "-@all +@read"
  }
}
```
{: pre}

For `role`, use some combination of `<+/-@all>` `<+/-@read>` `<+/-@write>` `<+/-@admin>` to grant  access.

## Redis roles
{: #redis-roles}

The Admin user and all other users on your instance have full access to the set of Redis commands, except for the subcommand `config` and `acl` - this includes the Admin user. `config get`, `config reset`, `acl whoami`, `acl cat`, `acl users`, `acl genpass`, `acl log`, and `acl help` are useable.

In Redis 6.x and newer, any user that you create; whether through *Service Credentials*, the CLI, API, or directly in Redis; have the same access. You cannot use Redis itself to create users or roles with access that is limited to specific keys or ranges of keys, as they are not propagated automatically in a cluster deployment. All other means to manage users ensure propagation across the cluster.

### Creating users through the UI
{: #create-users-service-cred}
{: ui}

1. Navigate to the service dashboard for your service.
2. Select *Service Credentials*.
3. Select *New Credential*.
4. Choose a descriptive name for your new credential.
5. (Optional) Specify whether the new credentials use a public or private endpoint. Use either `{ "service-endpoints": "public" }` / `{ "service-endpoints": "private" }` in the *Add Inline Configuration Parameters* field to generate connection strings using the specified endpoint. Use of the endpoint is not enforced. It just controls which hostnames are in the connection strings. Public endpoints are generated by default.
6. Click *Add* to provision the new credentials. A username, password, and an associated user are auto-generated.

The new credentials appear in the table, and the connection strings are available as JSON in a click-to-copy field under *View Credentials*.

### Creating users from the API
{: #create-users-api}
{: api}

The _Foundation Endpoint_ that is shown in the _Overview_ of your service provides the base URL to access this instance through the API. To create and manage users, use the base URL with the [users](/apidocs/cloud-databases-api/cloud-databases-api-v5#updateuser) endpoint:

```sh
curl -X POST https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/{user_type} \
-H "Authorization: Bearer $APIKEY" \ 
-H "Content-Type: application/json" \
-d "{"user": {"username": "user", "password": "v3ry-1-secUre-pAssword-2"}}" \
```
{: pre}

To retrieve a user's connection strings, use the base URL with the `/users/{userid}/connections` endpoint.

## Internal-use users - Redis 6.x and newer
{: #internal-users}

There are four reserved users on your instance. Modifying these users causes your instance to become unstable or unusable.

- `ibm-user` - An internal `admin` user for managing the instance and exposing metrics.
- `replication-user` - The user account that is used for replication.
- `sentinel-user` - The user account for sentinels to handle monitoring and failovers.
- `admin` - The default user provided to access your instance.
