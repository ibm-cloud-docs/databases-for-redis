---

copyright:
  years: 2019, 2024
lastupdated: "2024-12-06"

keywords: redis, databases, redis cache

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Redis as a cache
{: #redis-cache}

{{site.data.keyword.databases-for-redis_full}} supports changing the [Redis database configuration](/docs/databases-for-redis?topic=databases-for-redis-changing-configuration), and you can use it to configure [Redis as a cache](https://redis.io/topics/lru-cache){: .external}. When configured as a cache, Redis evicts old data in favor of new data according to the cache settings you define. Even when configured as a cache, {{site.data.keyword.databases-for-redis}} deployments still take a daily backup snapshot. It is not currently possible to disable backups on your deployment. They also write some data to disk for high-availability. Redis relies on copying over an `.rdb` file to resync followers.

## Cache settings
{: #redis-cache-settings}

To configure the cache, you adjust the `maxmemory` and the `maxmemory` settings of your deployment. `maxmemory` defines the size of the cache. The `maxmemory-policy` defines the eviction behavior when the `maxmemory` limit is reached. In addition, other settings take care of database operations and tuning.

### `maxmemory`
{: #redis-cache-maxmemory}

By default, `maxmemory` is set to 80% of a data node's available memory, so your node doesn't run out of system resources. You can adjust this setting, but set a reasonable limit. Otherwise, your data can take all the available memory and your deployment runs out of resources.

### `maxmemory-policy`
{: #redis-cache-maxmemory-policy}

| Policy | Behavior |
| --------- | --------- |
| `noeviction` | Does not evict keys and returns an error when the `maxmemory` limit is reached. |
| `allkeys-lfu` | Keeps frequently used keys and removes least frequently used (LFU) keys. |
| `volatile-lfu` | Removes least frequently used keys with the expire field set to true. |
| `allkeys-lru` | Evicts less recently used (LRU) keys first. |
| `volatile-lru` | Evicts less recently used (LRU) keys from the set of keys that expire first. |
| `allkeys-random` | Evicts keys randomly. |
| `volatile-random` | Evicts keys randomly from the set of keys that expire. |
| `volatile-ttl` | Evicts keys that expire, and tries to evict keys with a shorter time to live (TTL) first. |
{: caption="Available Redis eviction policies" caption-side="top"}

With an `allkeys` policy, the algorithm chooses which keys to evict from the set of all keys. With a `volatile` policy, the algorithm chooses to evict keys that have either [expired](https://redis.io/commands/expire){: external} or have a [time-to-live (TTL)](https://redis.io/commands/ttl){: .external} set. In a `volatile` policy, if no keys match the policy, no keys are evicted.
{: .tip} 

### Other settings
{: #redis-cache-other-settings}

| Policy | Behavior | Notes |
| ---------|---------|------------ |
| `appendonly` | Default value, `yes`. Enables Redis data to be written to disk. | If you are caching data, you want to set this value to `no`. |
| `stop-writes-on-bgsave-error` | Default value, `yes`. Redis stops accepting writes if it detects an unsuccessful backup snapshot.| For caching, you can set to `no`. |
| `maxmemory-samples` | Tunes the LRU algorithm, default value `5`. | [Approximated LRU algorithm](https://redis.io/topics/lru-cache#approximated-lru-algorithm){: .external} |
{: caption="Redis cache settings " caption-side="top"}

## Setting an example cache
{: #redis-cache-example-cache}

To adjust the configuration of your deployment, send a JSON object with the settings that you want to change and their new values. 

You are able to use `CONFIG SET` directly from a Redis cli-client, but changes made there are not permanent. Use the {{site.data.keyword.databases-for}} [CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-configuration) or [API](/apidocs/cloud-databases-api/cloud-databases-api-v5#updatedatabaseconfiguration) to change your deployment's configuration file. More information is in [Changing Your Redis Configuration](/docs/databases-for-redis?topic=databases-for-redis-changing-configuration).
{: .tip} 

For example, the Redis documentation recommends the `allkeys-lru` setting as a good starting place for a general-use cache. It's also fine to leave the `maxmemory` and `maxmemory-samples` at their default values. 

To configure the cache **from the CLI**, you can use the following:

```sh
ibmcloud cdb deployment-configuration '<INSTANCE_NAME_OR_CRN>' '{"configuration":{"maxmemory-policy":"allkeys-lru", "appendonly":"no", "stop-writes-on-bgsave-error":"no"}}'
```
{: pre}

To set up the same configuration **through the API**, you can use the following:

```sh
curl -X PATCH 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/configuration/schema' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"configuration":{
        "maxmemory-policy":"allkeys-lru",
        "appendonly":"no",
        "stop-writes-on-bgsave-error":"no"
      }
    }'
```
{: pre}
