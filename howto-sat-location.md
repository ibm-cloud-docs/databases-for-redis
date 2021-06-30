---

copyright:
  years: 2021
lastupdated: "2021-06-29"

keywords: IBM Cloud, databases, Satellite, ICD, get started

subcollection: cloud-databases

content-type: tutorial
services: databases-for-cd
completion-time: 15m

---

{:codeblock: .codeblock}
{:screen: .screen}
{:download: .download}
{:external: target="_blank" .external}
{:faq: data-hd-content-type='faq'}
{:gif: data-image-type='gif'}
{:important: .important}
{:note: .note}
{:pre: .pre}
{:tip: .tip}
{:preview: .preview}
{:deprecated: .deprecated}
{:beta: .beta}
{:term: .term}
{:shortdesc: .shortdesc}
{:script: data-hd-video='script'}
{:support: data-reuse='support'}
{:table: .aria-labeledby="caption"}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:help: data-hd-content-type='help'}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:tsSymptoms: .tsSymptoms}
{:curl: .ph data-hd-programlang='curl'}
{:step: data-tutorial-type='step'}
{:tutorial: data-hd-content-type='tutorial'}
{:ui: .ph data-hd-interface='ui'}
{:cli: .ph data-hd-interface='cli'}
{:api: .ph data-hd-interface='api'}

# IBM Cloud™ Databases enabled by IBM Cloud Satellite
{: #cd-satellite}

With IBM Cloud™ Databases (ICD) enabled by IBM Cloud Satellite, you can deploy ICD instances into a Satellite location. The ICD service will then install an ICD Satellite service cluster in your Satellite location into which your database instances will be deployed.
ICD enabled by IBM Cloud Satellite supports Satellite locations on [Amazon Web Services (AWS)](https://cloud.ibm.com/docs/satellite?topic=satellite-aws), as well as on your on-premises data centers that are attached to the IBM Cloud management location in Washington, DC.
{: shortdesc}

ICD enabled by IBM Cloud Satellite supports {{site.data.keyword.databases-for-cassandra}}, {{site.data.keyword.databases-for-enterprisedb}}, {{site.data.keyword.databases-for-etcd}}, {{site.data.keyword.databases-for-postgresql}}, {{site.data.keyword.databases-for-redis}}, and {{site.data.keyword.messages-for-rabbitmq}}.
{: .note}

Before proceeding, you should refer to the [Satellite Usage requirements](/docs/satellite?topic=satellite-requirements).
{: .tip}

## Before you begin:
{: #cd-satellite-location}

- If you have not already created a Satellite location, see [Setting up Satellite locations](/docs/satellite?topic=satellite-locations). We recommend following the steps in the [Manually creating Satellite locations
](/docs/satellite?topic=satellite-locations#location-create) documentation.

- Also, be sure you have set up the [IBM Cloud command-line interface (CLI)](/docs/satellite?topic=satellite-setup-cli
), the plug-in for Satellite commands, and other related CLIs.

- For the management location, choose **Washington DC**. If creating your Satellite location on AWS, adjust the **host zones** to AWS-default zone names, for example: **us-east-1a**, **us-east-1b**, **us-east-1c**.
{: .important}

- Before proceeding with **Step 1**, you should have set up your Satellite location properly and ensured the Satellite control plane is up and running.

## Step 1: Prepare Satellite location for IBM Cloud™ Databases
{: #cd-satellite-host}

Before deploying the ICD enabled by IBM Cloud Satellite service, you should prepare your Satellite location.

### Attach additional hosts to the Satellite location

These additional attached worker nodes are used to create a service cluster into which the database instances will later be deployed.
Attach to your Satellite location:
- three type **8x32** hosts
   - on AWS, choose three hosts of type **AWS m5d.2xlarge**
- three type **32x128** hosts
   - on AWS choose three hosts of type **AWS m5d.8xlarge**

### Create a Satellite block storage configuration

ICD enabled by IBM Cloud Satellite supports the following [Satellite storage templates](https://cloud.ibm.com/docs/satellite?topic=satellite-sat-storage-template-ov):
- If you are setting up your Satellite location using AWS, you should configure your block storage using [Amazon Elastic Block Storage (EBS)](/docs/satellite?topic=satellite-config-storage-ebs).
- If you are setting up your data center on-prem, you should use [NetApp ONTAP-SAN](/docs/satellite?topic=satellite-config-storage-netapp) storage template for a Satellite location within your data center (on-premises)

#### AWS EBS

To create an AWS Satellite location, refer to [Creating an AWS EBS storage configuration](/docs/satellite?topic=satellite-config-storage-ebs).

See below for an EBS storage configuration code example. For a full list of steps, consult the above documentation.

```
ibmcloud sat storage config create  \
  --name 'aws-ebs-config-storage-testing-1' \
  --template-name 'aws-ebs-csi-driver' \
  --template-version '0.9.14' \
  --location 'c2tace1w07k5gvilnqq0' \
  -p "aws-access-key=${SAT_EBS_ADMIN_KEY_ID}" \
  -p "aws-secret-access-key=${SAT_EBS_ADMIN_KEY}"
```

#### NetApp ONTAP-SAN

For a Satellite location within your on-prem data center, refer to [NetApp ONTAP-SAN](/docs/satellite?topic=satellite-config-storage-netapp).

See below for an NetApp ONTAP-SAN storage configuration code example. For a full list of steps, consult the above documentation.

```
ibmcloud sat storage config create \
    --name 'netapp-san'  \
    --location '<location-id>' \
    --template-name 'netapp-ontap-san' \
    --template-version '21.04'  \
    --param "dataLIF=<dataLIF IP address>" \
    --param "managementLIF=<dataLIF IP address" \
    --param  "svm=<svm name>" \
    --param "username=admin" \
    --param 'password=<password>' \
    --source-org 'ntap-org'  \
    --source-branch 'develop'
````

### Enable public endpoints on the Satellite Control Plane
To gain access to your cluster from the public network, such as to test access to your cluster from your local machine, first retrieve your public endpoints from your AWS portal for the control plane hosts. 
Then, using the IP's from your AWS portal, enter the following command:
```
ibmcloud sat location dns register --location <location> --ip <public-ip1> --ip <public-ip2> --ip <public-ipN>
```

For more information on accessing clusters, refer to [Accessing clusters from the public network](/docs/openshift?topic=openshift-access_cluster#sat_public_access).

## Step 2: Grant a service authorization
{: #cd-satellite-serviceauth}

Begin by configuring IAM Authorizations:
- Configure your IAM Authorizations under the **Manage** tab.
- Choose the **Authorizations** tab from the lefthand menu.
- Click the **create** button to create an authorization that will allow a service instance access to another service instance.

The source service is the service that is granted access to the target service. The roles you select define the level of access for this service. The target service is the service you are granting permission to be accessed by the source service based on the assigned roles.
{: .note}

- In the **Source Service** field, select **Databases for <cloud database>**.
- In the **Target Service** field, select **Satellite**.
- Select all options:
  - **Satellite Cluster Creator**
  - **Satellite Link Administrator**
  - **Satellite Link Source Access Controller**
 - Then **Authorize**.

## Step 3: Provisioning ICD Satellite Deployment

When you create a new database service instance, a service cluster will first be deployed into your Satellite location. This service cluster step requires you to have prepared the Satellite location properly (see Step 2 above). 
Once you have prepared your satellite location and granted service authorization, you will need to provision your ICD Satellite Deployment by selecting the Satellite location you have created. For thorough documentation of the provisioning process, see the relevant provisioning documentation for  your ICD Satellite deployment. For example, for PostgreSQL, refer to [Provisioning](docs/databases-for-postgresql?topic=cloud-databases-provisioning).

Deployment of the first service cluster can take up to one hour. Once the service cluster is created, you must create a storage assignment manually **before** the database instance will be started. Subsequent clusters will provision more quickly.
{: .important}

## Step 4: Create a Storage Assignment

When the service cluster is available in your Satellite location, the next step is to create a Satellite storage assignment. This will allow the service cluster to create volumes on the previously configured storage.

- From the lefthand **Navigation Menu**, select **Satellite**, then **Locations**.
- Select your Satellite location.
- Select **Services**
- Follow the prompt to attach block storage.

To obtain your `ROKS-Service-cluster-ID`, enter the following command into the IBM Cloud CLI:
```
ibmcloud oc cluster ls --provider satellite
```
{: pre}

See the example below for an AWS Satellite location storage assignment:
```
ibmcloud sat storage assignment create  \
    --name "ebs-assignment"  \
    --service-cluster-id <ROKS-Service-cluster-ID>  \
    --config 'aws-ebs-config-storage-testing-1'
```

See the example below for an NetApp ONTAP-SAN on-prem Satellite location storage assignment:

```
ibmcloud sat storage assignment create \
  --name "san-assignment"  \
  --service-cluster-id <ROKS-Service-cluster-ID>  \
  --config 'netapp-san'
```

After storage assignment has been created, allow up to 30 minutes for the database instance to be ready for usage.
