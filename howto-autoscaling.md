---

copyright:
  years: 2020, 2025
lastupdated: "2025-05-21"

keywords: redis, databases, scaling, autoscaling, disk I/O, memory

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Autoscaling
{: #autoscaling}

Autoscaling is designed to respond to the short-to-medium term trends in resource usage on your {{site.data.keyword.databases-for-redis_full}} deployment. When enabled, your deployment is checked at the interval you specify. If it is running short on resources, more resources are added to the deployment. To keep an eye on your resources, use the [{{site.data.keyword.monitoringfull}} integration](/docs/databases-for-redis?topic=databases-for-redis-monitoring), which provides metrics for memory, disk space, and disk I/O utilization.

You can set your deployment to autoscale disk, RAM, or both. 

General Autoscaling parameters:

- When to scale, based on usage over a period of time.
- By how much to scale, as a percentage of the resources per member.
- How often to scale, measured either in seconds, minutes, or hours.
- A hard limit on scaling, your deployment stops scaling at the limit.

![Example Autoscaling panel](images/autoscaling-update.png){: caption="Example Autoscaling panel" caption-side="bottom"}

Memory - Memory autoscaling is based on disk I/O utilization in order to provide more memory for disk caching as your read/write load increases. The benefit is that additional memory might alleviate pressure on disk I/O by supporting more caching. 

If you have Redis configured as a cache, there aren't currently any parameters you can use that would trigger autoscaling on your deployment. Autoscaling configurations based on memory usage are currently not available. 
{: .tip}

Disk - Disk autoscaling can scale when either disk usage reaches a certain threshold, Disk I/O utilization reach a certain threshold, or both. (The "or" in the UI operates as an `inclusive or`, `|`, `v`.) The amount of IOPS available to your deployment increases with disk size at a ratio of 10 IOPS for each GB.

CPU and RAM autoscaling is not supported on Isolated Compute. Disk autoscaling is available. If you provisioned an isolated instance or switched over from a deployment with autoscaling, monitor your resources using [{{site.data.keyword.monitoringfull}} integration](/docs/databases-for-redis?topic=databases-for-redis-monitoring), which provides metrics for memory, disk space, and disk I/O utilization. To add resources to your instance, manually scale your deployment.

The resource numbers refer to each database node in a deployment. For example, there are two data members in a Redis deployment and if the deployment is scaled with 10 GB of disk and 1 GB of RAM, that means each member gets 10 GB of disk and 1 GB of RAM. The total resources added to your deployment is 20 GB of disk and 2 GB of RAM.

## Autoscaling considerations
{: #autoscaling-consider}

- Scaling your deployment up might cause your databases to restart. If your scaled deployment needs to be moved to a host with more capacity, then the databases are restarted as part of the move.

- Disk cannot be scaled down.

- A few scaling operations can be more long running than others. Drastically increasing RAM or Disk can take longer than smaller increases to account for provisioning more underlying hardware resources.

- Autoscaling operations are logged in [{{site.data.keyword.atracker_full}}](/docs/databases-for-redis?topic=databases-for-redis-at_events).

- Limits:
   - Can't set anything to scale in an interval less than 60 seconds.
   - Maximum Disk = 4 TB per member.
   - Maximum RAM = 112 GB per member.

- Autoscaling does not scale down deployments where disk or memory usage has shrunk. The RAM provisioned to your deployment remains for your future needs, or until you scale down your deployment manually. The disk provisioned to your deployment remains because disk cannot be scaled down.

- If you just need to add resources to your deployment occasionally or rarely, you can [manually scale](/docs/databases-for-redis?topic=databases-for-redis-resources-scaling) your deployment.

## Configuring autoscaling in the UI
{: #autoscaling-ui}
{: ui}

Autoscaling is in _Resources_ in your deployment's _Manage_ page. To enable scaling, enter your parameters. Then, check the boxes to enable the parameters you are using. Be sure to click **Save changes** for your configuration to be saved and your changes to take effect.

To disable autoscaling, clear the boxes for the parameters that you no longer want to use. If you clear all the boxes, autoscaling is disabled. Click **Save changes** to save the configuration.

CPU and RAM autoscaling is not supported on Isolated Compute. Disk autoscaling is available. If you provisioned an isolated instance or switched over from a deployment with autoscaling, monitor your resources using [{{site.data.keyword.monitoringfull}} integration](/docs/databases-for-redis?topic=databases-for-redis-monitoring), which provides metrics for memory, disk space, and disk I/O utilization. To add resources to your instance, manually scale your deployment.

## Configuring autoscaling in the CLI
{: #autoscaling-cli}
{: cli}

Get the autoscaling parameters for your deployment through the CLI using the [`cdb deployment-autoscaling`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-autoscaling) command.

```sh
ibmcloud cdb deployment-autoscaling <INSTANCE_NAME_OR_CRN> member
```
{: pre}

Enable and set autoscaling parameters through the CLI using a JSON object or file with the [`cdb deployment-autoscaling-set`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-autoscaling-set) command.

```sh
ibmcloud cdb deployment-autoscaling-set <INSTANCE_NAME_OR_CRN> member '{"autoscaling": { "memory": {"scalers": {"io_utilization": {"enabled": true, "over_period": "5m","above_percent": 90}},"rate": {"increase_percent": 10.0, "period_seconds": 300,"limit_mb_per_member": 125952,"units": "mb"}}}}'
```
{: pre}

CPU and RAM autoscaling is not supported on Isolated Compute. Disk autoscaling is available. If you provisioned an isolated instance or switched over from a deployment with autoscaling, monitor your resources using [{{site.data.keyword.monitoringfull}} integration](/docs/databases-for-redis?topic=databases-for-redis-monitoring), which provides metrics for memory, disk space, and disk I/O utilization. To add resources to your instance, manually scale your deployment.

## Configuring autoscaling in the API
{: #autoscaling-api}
{: api}

Get the autoscaling parameters for your deployment through the API by sending a `GET` request to the [`/deployments/{id}/groups/{group_id}/autoscaling`](/apidocs/cloud-databases-api/cloud-databases-api-v5#getautoscalingconditions) endpoint.

```sh
curl -X GET https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/groups/{group_id}/autoscaling
-H 'Authorization: Bearer <>' \
```
{: pre}

Enable and set the autoscaling parameters for your deployment through the API by sending a `POST` request to the endpoint. Enabling autoscaling works by setting the `scalers` (`io_utilization` or `capacity`) to `true`.

```sh
curl -X PATCH https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/groups/member/autoscaling -H 'Authorization: Bearer <>' 
-H 'Content-Type: application/json' 
-d '{"autoscaling": {
      "memory": {
        "scalers": {
          "io_utilization": {
            "enabled": true, 
            "over_period": "5m",
            "above_percent": 90}
          },
          "limits": {
            "scale_increase_percent": 10.0,
            "scale_period_seconds": 30,
            "scale_maximum_mb": 125952,
            "units": "mb"
          }
      }
    }'
```
{: pre}

Disable autoscaling by sending the `PATCH` request with the currently enabled scalers set to `false`. If all of them are set to `false`, then autoscaling is disabled on your deployment.

CPU and RAM autoscaling is not supported on Isolated Compute. Disk autoscaling is available. If you provisioned an isolated instance or switched over from a deployment with autoscaling, monitor your resources using [{{site.data.keyword.monitoringfull}} integration](/docs/databases-for-redis?topic=databases-for-redis-monitoring), which provides metrics for memory, disk space, and disk I/O utilization. To add resources to your instance, manually scale your deployment.
