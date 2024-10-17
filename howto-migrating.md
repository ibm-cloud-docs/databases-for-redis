---
copyright:
  years: 2024
lastupdated: "2024-10-17"

keyowrds: redis, databases, migrating

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Migrating to {{site.data.keyword.databases-for-redis}}
{: #migrating}


## If you require data migration
{: #upgrading-req-data-migration}

If you absolutely must retain your persisted data when upgrading to the next major version of {{site.data.keyword.databases-for-redis}}, take steps to successfully migrate all of your data. Copy all of the key-value pairs into the new deployment. Do this from your old instance to your new deployment by using a method appropriate to your use case.

{{site.data.keyword.databases-for-redis}} provides an unsupported example script to help simplify this process. 

### The migration script
{: #upgrading-migration-script}

Warning! This script is unsupported and exists as an example of what you can build to move {{site.data.keyword.databases-for-redis}} data from one instance to another as part of a version upgrade. All source code and binaries that are attached to this document are referred to here as "the Program". IBM is not providing program services of any kind for the Program. IBM is providing the Program on an "AS IS" basis without warranty of any kind. IBM WILL NOT BE LIABLE FOR ANY ACTUAL, DIRECT, SPECIAL, INCIDENTAL, OR INDIRECT DAMAGES OR FOR ANY ECONOMIC CONSEQUENTIAL DAMAGES (INCLUDING LOST PROFITS OR SAVINGS), EVEN IF IBM, OR ITS RESELLER, HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES.
{: .attention}

Copying data from your old {{site.data.keyword.databases-for-redis}} deployment to a new version involves running a simple Python script [available on GitHub](https://github.com/IBM-Cloud/clouddatabases-migration-examples/blob/master/redis/redis_migration.py){: external}. The script copies all the keys from your source database over to your new {{site.data.keyword.databases-for-redis}} deployment. Download the script and make sure that you have Python 3.x installed. If you’re on macOS, use `homebrew` to install it running the command `brew install python3`, which gives you the latest version.

Next, create a migration window to let your users know that you are doing some maintenance. This way you have time to migrate all of your data over to your new {{site.data.keyword.databases-for-redis}} deployment. If you are using Redis as a key-value store with expire times on keys, these expiry times are copied over to your new database. Our example does this with 10 million keys in our database, which doesn't take much time to migrate depending on your bandwidth.

### Getting the destination and source database credentials
{: #upgrading-dest-source-cred}

#### Getting the destination and source database credentials in the UI
{: #upgrading-dest-source-cred-ui}
{: ui}

Now, have the credentials of both the old source database and your new {{site.data.keyword.databases-for-redis}} deployment. Get the credentials for both {{site.data.keyword.databases-for-redis}} deployments by selecting the databases from the Resources page. Then, click the _Service credentials_ link. From there, create a new credential for the destination database by clicking _New credential_. Use any credentials that you create for the source.

#### Getting the destination and source database credentials in the CLI
{: #upgrading-dest-source-cred-cli}
{: cli}

Get your destination and source database credentials by using the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin){: external}.

Run a command like:

```sh
ibmcloud cdb deployment-connections <Redis deployment name>
```
{: .pre}

This provides you with your {{site.data.keyword.databases-for-redis}} connection URI that includes the hostname and port. To get the decoded CA certificate for the database, run:

```sh
ibmcloud cdb deployment-cacert <Redis deployment name>
```
{: .pre}

After the CA certificate is decoded, save it to a file to connect to the database later. If you don’t know the password for your deployment, get it either from your generated service credentials or create a new password by running:

```sh
ibmcloud cdb deployment-user-password <Redis deployment name> admin <new password>
```
{: .pre}

With this information, you have what you need for the destination and the source databases.

### Run the script and migrate your data
{: #upgrading-script-migration-data}

Since you have all the credentials for both databases (source and new {{site.data.keyword.databases-for-redis}} destination), you're ready to run the `pymigration.py` script. Run the code from your terminal by using the credentials from the prior steps:

```python
python pymigration.py <source host> <source password> <source port>
<destination host> <destination_username:destination_password> <destination port>
<destination ca certificate path> --sslsrc --ssldst
```
{: .pre}

For newer versions of Redis as source, use `<source user:source password>`.
{: note}

Since you are copying data from a source outside of {{site.data.keyword.databases-for-redis}} database, add the `--sslsrc` flag if your {{site.data.keyword.databases-for-redis}} database is SSL/TLS enabled. If it isn’t, then don’t add the flag. 

This makes sure that Redis is connecting to a SSL/TLS enabled database. Also add `--ssldst` since the destination database is your new {{site.data.keyword.databases-for-redis}} deployment, which also is SSL/TLS enabled. 

Supplementary flags that you might add are `--db` and `--flush`. `--db` indicates the database that your keys are copied from, which is the database that they copied into in your new deployment. 

The `--flush` flag flushes the destination database before importing the keys from the source database. If you want to keep things fresh in your new deployment, `--flush` deletes all of the keys, and then imports the new keys from your source database.

Running the script that uses `OldDB` as the source for the data migration and `NewDB` as the destination for the migrated data, you see output like:

```text
python pymigration.py OldDB.databases.appdomain.cloud OldDBpassword1 88888 NewDB.databases.appdomain.cloud NewDBpassword1 99999 ~/NewDBCA  --sslsrc --ssldst  10000000 keys: 100% |###################################################| Time: 0:00:00 Keys disappeared on source during scan: 0 Keys already existing on destination: 0
```

This script copied 10 million keys from `OldDB` to `NewDB`. No keys were deleted on the `OldDB` database. If you add a new key to the `OldDB` deployment and attempt to migrate the data again, you notice that the key count on the destination changes to 10000000, as the original 10 million keys already exist in that database.

```text
10000000 keys: 100% |###################################################| Time: 0:00:00
Keys disappeared on source during scan: 0
Keys already existing on destination: 10000000
```

## Next steps
{: #upgrading-next-steps}

After your data migration, swap out your application’s database connection strings with your new connection string and credentials.
