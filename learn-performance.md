---

copyright:
  years: 2019, 2025
lastupdated: 2025-04-02

keywords: redis, databases

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Performance
{: #performance}

{{site.data.keyword.databases-for-redis_full}} deployments deployments can be both manually [scaled to your usage](/docs/databases-for-redis?topic=databases-for-redis-resources-scaling), or configured to [autoscale](/docs/databases-for-redis?topic=databases-for-redis-autoscaling) under certain resource conditions. If you are tuning the performance of your deployment, consider a few factors.

## Monitoring your deployment
{: #monitor-deployment}

{{site.data.keyword.databases-for-redis}} deployments offer an integration with the [{{site.data.keyword.monitoringfull}} service](/docs/databases-for-redis?topic=databases-for-redis-monitoring) for basic monitoring of resource usage on your deployment. Many of the available metrics, like disk usage and IOPS, are presented to help you configure [autoscaling](/docs/databases-for-redis?topic=databases-for-redis-autoscaling) on your deployment. Observing trends in your usage and configuring the autoscaling to respond to them can help alleviate performance problems before your databases become unstable due to resource exhaustion.

## Memory policies
{: #mem-policies}

By default, deployments are configured with a `noeviction` policy. All data is kept in memory until the `maxmemory` limit is reached and Redis returns an error if the memory limit is exceeded. The `maxmemory` is set to 80% of a data node's available memory, so your node doesn't run out of system resources. 

You can scale the amount of memory to accommodate more data, and you can configure the `maxmemory` setting to tune memory usage. The [Redis documentation](https://redis.io/topics/memory-optimization#memory-allocation){: external} has some good information on memory behavior and tuning `maxmemory`.

You can also configure your deployment to use [Redis as a cache](/docs/databases-for-redis?topic=databases-for-redis-redis-cache), allowing Redis to evict data out of memory once the memory limit is reached. 

## Disk IOPS
{: #disk-iops}

The number of Input-Output Operations per second (IOPS) is limited by the type of storage volume. Storage volumes for {{site.data.keyword.databases-for-redis}} deployments are provisioned on [Block Storage Endurance Volumes in the 10 IOPS per GB tier](/docs/BlockStorage?topic=BlockStorage-orderingBlockStorage). By default, a deployment starts with persistence enabled. It's possible for very busy databases to exceed the IOPS for the disk size, and increasing disk can alleviate a performance bottleneck. 
