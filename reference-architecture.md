---

Copyright:
  years: 2018
lastupdated: "2018-08-21"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Architecture

{{site.data.keyword.databases-for-redis_full}} is a managed cloud database service that runs in containers that are orchestrated by Kubernetes. It is fully integrated into the {{site.data.keyword.cloud_notm}} ecosystem. The database, storage, and monitoring all run in {{site.data.keyword.cloud_notm}}.

## Redis Databases

An {{site.data.keyword.databases-for-redis}} service contains a cluster with two data members; a leader and a follower member. Data is replicated across both data members, and their disk space is spread over the region's availability zones. High-availability is managed with ..... If one data member become unreachable, your cluster will continue to operate normally.

### Storage

All storage for {{site.data.keyword.cloud_notm}} Databases is provided on {{site.data.keyword.cloud_notm}} Object Storage. Storage for {{site.data.keyword.databases-for-redis}} is auto-scaled to the size of your data on disk.

### Full-disk Encryption

All {{site.data.keyword.databases-for-redis}} services all have encryption at rest. Your data resides on servers that have volume-level encryption enabled.

## Portals

{{site.data.keyword.databases-for-redis}} database connections are managed by 2 HAProxy portals. They automatically connect to the leader member of the Redis cluster and provide load-balancing. The portals are then behind the Kubernetes Nodeport Service, which provides the point of connection for your applications. Having two portals allows for applications to maintain connectivity if one of the portals becomes unreachable.

### Encryption in Transit

All {{site.data.keyword.databases-for-redis}} HAProxy portals are TLS/SSL enabled and support TLS version 1.2. Self-signed certificates are provided to enable applications to validate the server upon connection.

## Monitoring

Each cluster's monitoring gathers the collected database, container, pod, and cluster metrics. All relevant container and database application monitoring is collected and sent to the IBM Cloud Monitoring Service.

## Access Management
{{site.data.keyword.databases-for-redis}} is an IAM-integrated service. Access to the service is governed by the roles and attributes that are consistent with IAM integrated services across the {{site.data.keyword.cloud_notm}}. For more information on IAM, see the [What is IAM?](https://console.{DomainName}/docs/iam/index.html#iamoverview) documentation. For details on how IAM affects access to {{site.data.keyword.databases-for-redis}}, see the [Managing Access](./access-management.html) page.

