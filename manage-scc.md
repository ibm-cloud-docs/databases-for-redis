---
copyright:
  years: 2018, 2024
lastupdated: "2024-10-17"

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Managing security and compliance with {{site.data.keyword.databases-for-redis_full}}
{: #manage-security-compliance}

{{site.data.keyword.databases-for-redis_full}} is integrated with the {{site.data.keyword.compliance_short}} to help you manage security and compliance for your organization.
{: shortdesc}

With the {{site.data.keyword.compliance_short}}, you can monitor for controls and goals that pertain to {{site.data.keyword.databases-for-redis_full}}.

## Monitoring security and compliance posture with {{site.data.keyword.databases-for-redis_full}}
{: #monitor-cloud-databases}

As a security or compliance focal, you can use the {{site.data.keyword.databases-for-redis_full}} goals to help ensure that your organization is adhering to the external and internal standards for your industry. By using the {{site.data.keyword.compliance_short}} to validate the resource configurations in your account against a [profile](#x2034950){: term}, you can identify potential issues as they arise.

All of the goals for {{site.data.keyword.databases-for-redis_full}} are added to the {{site.data.keyword.cloud_notm}} Best Practices Controls 1.0 profile but can also be mapped to other profiles.
{: note}

To start monitoring your resources, check out [Getting started with {{site.data.keyword.compliance_short}}](/docs/security-compliance?topic-security-compliance-getting-started)

### Available goals for {{site.data.keyword.databases-for-redis_full}}
{: #mongodb-available-goals}

- **Check whether {{site.data.keyword.databases-for-redis_full}} is enabled with IBM-managed or customer-managed encryption and Bring Your Own Key (BYOK).** All {{site.data.keyword.databases-for-redis_full}} instances are automatically encrypted at rest with IBM-managed keys, for customer-managed encryption keys review the [following documentation](/docs/cloud-databases?topic=cloud-databases-key-protect).
- **Check whether {{site.data.keyword.databases-for-redis_full}} is accessible only through HTTPS.** All {{site.data.keyword.databases-for}} connections use TLS/SSL encryption for data in transit. The current supported version of this encryption is TLS 1.2. 
- **Check whether {{site.data.keyword.databases-for-redis_full}} is accessible only by using private endpoints.** Customers can disable public endpoints at provision time. For more information, see [Service endpoints integration](https://cloud.ibm.com/docs/cloud-databases?topic=cloud-databases-service-endpoints).
- **Check whether {{site.data.keyword.databases-for-redis_full}} network access is restricted to a specific IP range.** To learn more about how to check or achieve this goal, see [Context-based restrictions](/docs/databases-for-redis?topic=databases-for-redis-cbr) or [allowlisting](https://cloud.ibm.com/docs/cloud-databases?topic=cloud-databases-allowlisting).
- **Check whether {{site.data.keyword.databases-for-redis_full}} version is up-to-date.**
- **Check whether {{site.data.keyword.databases-for-redis_full}} disk space autoscales to avoid database downtime.**
