---

copyright:
  years: 2018, 2024
lastupdated: "2024-10-17"

keywords: redis, databases, soc, hipaa, gdpr, terms

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Security and compliance
{: #security-compliance}

## Protection against unauthorized access
{: #protect-unauth-access}

{{site.data.keyword.databases-for-redis_full}} use the following methods to protect data in transit or in storage.

- All {{site.data.keyword.databases-for-redis}} connections use TLS/SSL encryption for data in transit. The current supported version of this encryption is TLS 1.2.
- Access to the Account, Management Console UI, and API is secured via [Identity and Access Management (IAM)](/docs/databases-for-redis?topic=databases-for-redis-iam).
- Access to the database is secured through the standard access controls provided by the database. These access controls are configured to require valid database-level credentials that are obtainable only through prior access to the database or through our Management Console UI or API.
- All {{site.data.keyword.databases-for-redis}} storage is provided on storage encrypted with LUKS using AES-256. The default keys are managed by [{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about). Bring-your-own-key (BYOK) for encryption is also available through [Key Protect Integration](/docs/databases-for-redis?topic=databases-for-redis-key-protect&interface=ui).
- [Context-based restrictions](/docs/databases-for-redis?topic=databases-for-redis-cbr) - Context-based restrictions give account owners and administrators the ability to define and enforce access restrictions for their resources based on the context of access requests.
- Isolated Compute - Isolated Compute is a secure single-tenant offering for complex, highly-performant enterprise workloads. By placing your deployment and all associated user-data management agents on an isolated machine, Cloud Databases Isolated Compute provides dedicated computing resources, dedicated storage bandwidth, and hypervisor-level isolation.
- Public and Private Networking - {{site.data.keyword.databases-for-redis}} is integrated with [Service Endpoints](/docs/databases-for-redis?topic=databases-for-redis-service-endpoints). You can select whether to use connections over the public network, the {{site.data.keyword.cloud_notm}} internal network, or both.
- Dedicated Cores - Allocating dedicated cores to your deployment introduces hypervisor-level isolation to your database instance, using isolated virtual machines to ensure your data processing remains separated from other customers. It also provides a guaranteed minimum number of CPUs to your deployment. Deployments with dedicated cores in the same Resource Group and {{site.data.keyword.cloud_notm}} Region may share a virtual machine.
- IP allowlisting (deprecated) - All deployments support [allowlisting IP addresses](/docs/databases-for-redis?topic=databases-for-redis-allowlisting&interface=ui) to restrict access to the service.

## Data resilience
{: #data-resilience}

- [Backups](/docs/databases-for-redis?topic=databases-for-redis-dashboard-overview&interface=ui#dashboard-overview-backups) for your deployment are included, unless you [configure Redis as a cache](/docs/databases-for-redis?topic=databases-for-redis-redis-cache). The {{site.data.keyword.databases-for-redis}} backups reside in [{{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage&cloud-object-storage-about-cloud-object-storage) and are also [encrypted](/docs/cloud-object-storage?topic=cloud-object-storage-security).
- All {{site.data.keyword.databases-for-redis}} deployments are configured with replication to provide both data resilience and [high-availability](/docs/databases-for-redis?topic=databases-for-redis-high-availability). Deployments contain a cluster with two data members in a primary/replica configuration and state is managed with a quorum of three [Redis sentinels](https://redis.io/topics/sentinel){: external}.
- If you deploy to an {{site.data.keyword.cloud_notm}} Single-Zone Region (SZR), each database node resides on a different host in the data center. 
- If you deploy to an {{site.data.keyword.cloud_notm}} Multi-Zone Region (MZR), the nodes are spread over the region's availability zone locations.
 
## Compliance certifications
{: #security-compliance-certifications}

### SOC 2 Type 2 certification
{: #soc2-cert}

{{site.data.keyword.IBM_notm}} provides a Service Organization Controls (SOC) 2 Type 2 report for {{site.data.keyword.databases-for-redis}}. The reports evaluate IBM's operational controls according to the criteria set by the American Institute of Certified Public Accountants (AICPA) Trust Services Principles. The Trust Services Principles define adequate control systems and establish industry standards for service providers such as IBM Cloud to safeguard their customers' data and information.

You can request an SOC 2 Type 2 report from the customer portal or contact your sales representative. Alternatively, you can open a support ticket with [IBM Cloud support](https://cloud.ibm.com/unifiedsupport/supportcenter){: external}

### ISO 27017, ISO 27018
{: #iso}

{{site.data.keyword.databases-for-redis}} conforms to the guidelines for information security controls applicable to the provision and use of cloud services that are defined in [ISO 27017](https://www.iso.org/standard/43757.html) and [ISO 27018](https://www.iso.org/standard/76559.html).

### General Data Protection Regulation (GDPR) 
{: #gdpr}

If you have an account with IBM Cloud, your personal data is held by {{site.data.keyword.cloud_notm}}. The {{site.data.keyword.IBM_notm}} Data Processing Addendum (DPA) applies to the processing of client's personal data by {{site.data.keyword.IBM_notm}} on behalf of client in order to provide {{site.data.keyword.IBM_notm}} standard services.  
[IBM DPA](https://www.ibm.com/dpa){: external}

{{site.data.keyword.databases-for-redis}} processes limited client Personal Information (PI) in the course of running the service and optimizing the user experience. 

{{site.data.keyword.databases-for-redis}} provides a [Data Sheet Addendum (DSA)](https://www.ibm.com/software/reports/compatibility/clarity-reports/report/html/softwareReqsForProduct?deliverableId=FCCD4BA07A5D11E89D57EFEED3CB8BE9){: external} with its policies as a Data Processor regarding content and data protection. 

### HIPAA
{: #security-hipaa}

{{site.data.keyword.databases-for-redis}} meets the required {{site.data.keyword.IBM_notm}} controls that are commensurate with the Health Insurance Portability and Accountability Act of 1996 (HIPAA) Security and Privacy Rule requirements. These requirements include the appropriate administrative, physical, and technical safeguards required of Business Associates in 45 CFR Part 160 and Subparts A and C of Part 164. HIPAA must be requested at the time of provisioning and requires a representative to sign a Business Associate Addendum (BAA) agreement with {{site.data.keyword.IBM_notm}}.

### PCI DSS
{: #security-pci-dss}

{{site.data.keyword.databases-for-redis}} are compliant with the Payment Card Industry Data Security Standard (PCI DSS). {{site.data.keyword.cloud_notm}} completes annual PCI DSS assessments by using an approved Qualified Security Assessor (QSA), and the resulting Attestations of Compliance (AOCs) and Service Responsibility Matrix (SRM) guides are available upon customer request. Auditors reviewed {{site.data.keyword.databases-for-redis}} for compliance under PCI DSS version 3.2.1 at Service Provider Level 1. 

Customers are responsible for the storing, processing, and transmission of their cardholder data, and can create cardholder data environments (CDEs) that can store, transmit, or process cardholder data by using {{site.data.keyword.databases-for-redis}}. Customers can request and use the {{site.data.keyword.cloud_notm}} AOCs and SRM guides when they seek their own PCI DSS certifications. It is the responsibility of the customer to document and operate CDEs and applications that are built by using {{site.data.keyword.cloud_notm}} Platform services in a PCI DSS-compliant manner.

It is the customer’s responsibility to familiarize themselves with these processes and to manage data retention and removal from the service according to the customer’s policies.

A full list of PCI DSS-ready {{site.data.keyword.cloud_notm}} Platform services, and options to request a PCI DSS AOC and SRM guide, can be found at the [IBM Cloud compliance page](https://www.ibm.com/cloud/compliance/industry).

## Terms
{: #security-terms}

- [The IBM privacy policy](https://www.ibm.com/privacy/us/en/){: external}
- [The IBM Cloud notices and terms of use](/docs/overview/terms-of-use?topic=overview-terms){: external}
