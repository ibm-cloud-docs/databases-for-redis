---

copyright:
  years: 2018, 2026
lastupdated: "2026-01-27"

keywords: databases-for-redis release notes

subcollection: databases-for-redis

content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}

# Release notes
{: #redis-relnotes}

Use these release notes to learn about the latest updates to {{site.data.keyword.databases-for-redis_full}} that are grouped by _date_ or _build number_.
{: shortdesc}

## 27 January 2026
{: #databases-for-redis-27jan2026}
{: release-note}

Redis 8.2 introduces considerable improvements over Redis 7.x including performance gains, reduced memory usage, expanded metrics and observability, and new functionality in Streams, bit operations, and vector search.

For more details and information about specific key Redis modules, see [Redis 8 features](/docs/databases-for-redis?topic=databases-for-redis-v8-features).

## 10 March 2025
{: #databases-for-redis-10mar2025}
{: release-note}

{{site.data.keyword.databases-for-redis}} {{site.data.keyword.satelliteshort}} plan is deprecated
:   {{site.data.keyword.databases-for-redis}} {{site.data.keyword.satelliteshort}} plan is now deprecated. As of March 10 2025, all documentation relating to {{site.data.keyword.databases-for-redis}} {{site.data.keyword.satelliteshort}} plan has been removed, as well as the ability to select {{site.data.keyword.databases-for-redis}} {{site.data.keyword.satelliteshort}} plan in the Cloud console.

## 31 January 2025
{: #databases-for-redis-31jan2025}
{: release-note}

{{site.data.keyword.databases-for-redis}} version 6.2 end of life
:  End of life announcement: Version 6.2 reaches end of life on **30 July 2025**. All {{site.data.keyword.databases-for-redis}} instances on version 6.2 that are still active, will be upgraded in-place to the next major version. However, there are no SLAs for this forced migration.

We recommend that you [upgrade to a new major version](/docs/databases-for-redis?topic=databases-for-redis-upgrading){: external} before the EOL date of your database version.

## 15 January 2025
{: #databases-for-redis-15jan2025}
{: release-note}

{{site.data.keyword.databases-for-redis}}  metrics addition
:  Metrics for *active connections*, *blocked connections*, *rejected connections*, *number of operations per second*, and*total commands processed* are added for {{site.data.keyword.databases-for-redis}}. You can use these metrics on {{site.data.keyword.monitoringfull}} in your 'custom report' or leverage the {{site.data.keyword.databases-for-redis}} dashboard. For more information, see [Redis metrics](https://cloud.ibm.com/docs/databases-for-redis?topic=databases-for-redis-monitoring#metrics-by-plan-redis-desc){: external}.

## 15 November 2024
{: #databases-for-redis-15nov2024}
{: release-note}

{{site.data.keyword.databases-for}} logs and events are now available on {{site.data.keyword.logs_full}}
: {{site.data.keyword.databases-for}} has onboarded {{site.data.keyword.logs_full_notm}}, a scalable logging service that persists logs and provides users with capabilities for querying, tailing, and visualizing logs. Use {{site.data.keyword.logs_full_notm}} to review your database logs and events starting **November 15, 2024**. For more information, see [Set up logging and monitoring](/docs/databases-for-redis?topic=databases-for-redis-getting-started-cdb-logging-monitoring&interface=ui) and [About IBM Cloud Logs](/docs/cloud-logs?topic=cloud-logs-about-cl).

## 16 September 2024
{: #databases-for-redis-16sept2024}
{: release-note}

Private endpoints as new default
:  To ensure best possible security for your databases, private endpoints are now the default in the {{site.data.keyword.cloud}} console. CLI and Terraform now require the endpoint type to be provided as part of creating an instance.

## 11 June 2024
{: #databases-for-redis-11jun2024}
{: release-note}

New `maxmemory` policies
:  Two new `maxmemory` policies have been introduced: `allkeys-lfu` and `volatile-lfu`. For more information, see [`maxmemory-policy`](/docs/databases-for-redis?topic=databases-for-redis-redis-cache#redis-cache-maxmemory-policy){: external}.

## 21 May 2024
{: #databases-for-redis-21may2024}
{: release-note}

New guidance information about best practices and how to avoid common errors
: [Best practices for {{site.data.keyword.databases-for-redis}}](/docs/databases-for-redis?topic=databases-for-redis-best-practices){: external} details the recommended best practices for using {{site.data.keyword.databases-for-redis}}. Take time to review these suggested practices. [How can I avoid common {{site.data.keyword.databases-for-redis}} errors?](/docs/databases-for-redis?topic=databases-for-redis-troubleshoot-common-errors){: external} describes several common errors seen on {{site.data.keyword.databases-for-redis}} and provides multiple suggested solutions to fix each error.

## 1 May 2024
{: #databases-for-redis-01may2024}
{: release-note}

New hosting models
:  You can choose between two hosting models: Isolated Compute and Shared Compute. Isolated Compute is a secure single-tenant offering for complex, highly performant enterprise workloads. Shared Compute is a flexible multi-tenant offering for dynamic, fine-tuned, and decoupled capacity selections. For more information, see [Hosting models](/docs/databases-for-redis?topic=databases-for-redis-hosting-models).

## 19 April 2024
{: #databases-for-redis-19apr2024}
{: release-note}

{{site.data.keyword.databases-for-redis}} updated licensing agreement
:  Recently, Redis Inc. updated their licensing agreement for Redis v7.4 and onward. While the offering is being adjusted to align with the new licensing terms, this update does not impact you, and you can continue to use {{site.data.keyword.databases-for-redis_full_notm}}.

## 07 March 2024
{: #databases-for-redis-07mar2024}
{: release-note}

Introducing {{site.data.keyword.databases-for-redis}} v7.2
:  {{site.data.keyword.databases-for-redis}} v7.2 is ready for use under [Preview](/docs/cloud-databases?topic=cloud-databases-versioning-policy#version-tags){: external}. This update includes [optimizations, new commands, improvements, and bug fixes](https://raw.githubusercontent.com/redis/redis/7.2/00-RELEASENOTES){: external}. Version 7.2 also introduces [RBAC (Role Based Access Control)](/docs/databases-for-redis?topic=databases-for-redis-user-management&interface=ui#redis-rbac){: external}, which allows for better access control and improves overall security. Note: Backward compatibility is not available from {{site.data.keyword.databases-for-redis}} v7.2. For more information, see [Versioning Policy](/docs/cloud-databases?topic=cloud-databases-versioning-policy){: external}.

## 06 March 2024
{: #databases-for-redis-06mar2024}
{: release-note}

Role-based access control (RBAC) feature released
:  Role-based access control (RBAC) allows you to configure the level of access for each user. This feature is available for version 6 and up. For more information, see [Role-based access control (RBAC)](/docs/databases-for-redis?topic=databases-for-redis-user-management&interface=ui#redis-rbac){: external}.

## 27 November 2023
{: #databases-for-redis-27nov2023}
{: release-note}

Monitoring Integration documentation updated
:  Monitoring Integration documentation now lists metrics for all {{site.data.keyword.databases-for}} services. For more information, see [Monitoring Integration](/docs/cloud-databases?topic=cloud-databases-monitoring){: external}.

## 12 October 2023
{: #databases-for-redis-12oct2023}
{: release-note}

{{site.data.keyword.databases-for-redis}} versions end of life
:  End of life announcement: Version 5.0 reaches end of life 26 April 2024. Version 6.0 reaches end of life 25 October 2024. All {{site.data.keyword.databases-for-redis}} instances on deprecated versions that are still active will be upgraded in-place to the next major version. We recommend that you upgrade following our [backup and restore process](/docs/cloud-databases?topic=cloud-databases-dashboard-backups){: external} before the EOL date of your version.

For more information, see [Upgrading to a new major version](/docs/databases-for-redis?topic=databases-for-redis-upgrading){: external}.

Users preferring to continue with default user after the upgrade to version 6.2 require a password change. For more information, see [the default user](/docs/databases-for-redis?topic=databases-for-redis-user-management&interface=ui#redis-default-user-permissions-upgrading){: external}.
{: note}

## 21 August 2023
{: #databases-for-redis-21aug2023}
{: release-note}

Redis default user
:  Before the arrival of [ACL support in Redis 6](https://redis.com/blog/getting-started-redis-6-access-control-lists-acls/){: external}, the `default` user had broad permissions and was used internally by {{site.data.keyword.databases-for}} and external users to manage {{site.data.keyword.databases-for-redis}} deployments. With Redis 6.x, {{site.data.keyword.databases-for-redis}} no longer uses the `default` user internally. {{site.data.keyword.databases-for-redis}} deployments are managed by {{site.data.keyword.databases-for}} the `ibm` user. For more information, see [Managing Users and Roles](/docs/databases-for-redis?topic=databases-for-redis-user-management){: external}.

## 23 May 2023
{: #databases-for-redis-23may2023}
{: release-note}

Setting up disk alerts for disk utilization tutorial
:  In this tutorial, you use the {{site.data.keyword.cloud_notm}} API and the [{{site.data.keyword.cloud_notm}} CLI](https://cloud.ibm.com/docs/cli?topic=cli-getting-started){: external} to set up an alert that emails you whenever the disk utilization of your database exceeds 90%. This specific example creates an alert on a {{site.data.keyword.databases-for-elasticsearch}} deployment, but it is applicable to all the databases in the IBM {{site.data.keyword.databases-for}} catalog. For more information, see [Setting up disk alerts for disk utilization](/docs/databases-for-redis?topic=databases-for-redis-disk-util-alert-tutorial).

## 19 October 2022
{: #databases-for-redis-19oct2022}
{: release-note}

Deploying and Connecting a Cloud Databases Instance Tutorial
:  This tutorial guides you through the process of deploying a {{site.data.keyword.databases-for}} instance and connecting it to a web front end by creating a webpage that allows visitors to input a word and its definition. These values are then stored in a database running on {{site.data.keyword.databases-for}}. You install the database infrastructure by using Terraform and your web application uses the popular Express framework. The application can then be run locally, or by using Docker. For more information, see [Deploying and Connecting a {{site.data.keyword.databases-for}} Instance](/docs/databases-for-redis?topic=databases-for-redis-create-instance-tutorial).

## 11 October 2022
{: #databases-for-redis-11oct2022}
{: release-note}

Protecting {{site.data.keyword.databases-for-redis_full}} resources with context-based restrictions
:  Context-based restrictions (CBR) give account owners and administrators the ability to define and enforce access restrictions for {{site.data.keyword.cloud}} resources based on the context of access requests. Access to {{site.data.keyword.databases-for}} resources can be controlled with CBR and identity and access management (IAM) policies. For more information, see [Protecting Cloud Databases resources with context-based restrictions](/docs/databases-for-redis?topic=databases-for-redis-cbr).

## 25 March 2022
{: #databases-for-redis-25mar2022}
{: release-note}

{{site.data.keyword.databases-for-redis_full}} Security Goals Updated
:  Available security and compliance goals updated. See updated goals [here](/docs/databases-for-redis?topic=databases-for-redis-manage-security-compliance).

## 09 November 2021
{: #databases-for-redis-09nov2021}
{: release-note}

{{site.data.keyword.databases-for-redis_full}} 4.0 End of Life in May 2022
:  After May 27, 2022, all {{site.data.keyword.databases-for-redis_full}} instances on version 4.0 that are still active will be upgraded in-place to the next major version, Redis 5.0.

## 30 June 2021
{: #databases-for-redis-30jun2021}
{: release-note}

General Availability of {{site.data.keyword.databases-for-redis_full}} support for IBM Cloud Databases enabled by IBM Cloud Satellite.
:  A distributed cloud provides consistent security and services across environments, centralized workload visibility, reduced latency, easier compliance and higher application development velocity.

## 13 April 2020
{: #databases-for-redis-13apr2020}
{: release-note}

{{site.data.keyword.databases-for-redis_full}} autoscaling
:  We are excited to announce that autoscaling of your deployments based on disk capacity and disk I/O utilization is now available for {{site.data.keyword.databases-for-redis_full}} via the UI, API, and CLI.

## 17 October 2019
{: #databases-for-redis-17oct2019}
{: release-note}

{{site.data.keyword.databases-for-redis_full}} 5 is Available in Preview
:  Redis 5 is now available in Preview on {{site.data.keyword.databases-for-redis_full}}. This release has lots of interesting new features, but the most important is the introduction of Redis Streams.

## 6 August 2019
{: #databases-for-redis-06aug2019}
{: release-note}

New Regions Available for IBM Cloud Database Services
:  {{site.data.keyword.databases-for-redis_full}} is now available to be deployed in Seoul; South Korea; and Chennai, India.

## 2 October 2018
{: #databases-for-redis-02oct2018}
{: release-note}

General Availability of {{site.data.keyword.databases-for-redis_full}}
:  {{site.data.keyword.databases-for-redis_full}} added to the [IBM Cloud Databases](https://www.ibm.com/cloud/databases) family.
