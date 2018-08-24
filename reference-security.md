---

Copyright:
  years: 2018
lastupdated: "2018-08-20"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Security and Compliance


## Protection Against Unauthorized Access

{{site.data.keyword.databases-for-redis_full}} use the following methods to protect data in transit or in storage.
- All {{site.data.keyword.databases-for-redis}} use TLS/SSL encryption for data in transit. The current supported version of this encryption is TLS 1.2.
- Access to the Account, Management Console UI, and API is secured via IAM (Identity and Access Management)
- Access to the database is secured through the standard access controls provided by the database. These access controls are configured to require valid database-level credentials that are obtainable only through prior access to the database or through our Management Console UI or API.
- All {{site.data.keyword.databases-for-redis}} storage is provided on encrypted volumes that use the Linux Unified Keys Setup (LUKS).  

## Data Resilience

- Backups are included in the service. {{site.data.keyword.databases-for-redis}} backups reside in the same cloud storage location as the database service itself, and so are also encrypted.
- All {{site.data.keyword.databases-for-redis}} are configured with replication, so the data exists with multiple copies and each copy resides on a different cluster and host. Where available, those clusters are also spread in different availability zones within the region where the service is deployed.

## Terms

The IBM Privacy Policy - https://www.ibm.com/privacy/us/en/
The IBM Cloud Notices and Terms of Use - https://console.bluemix.net/docs/overview/terms-of-use/notices.html#notices


