---

copyright:
  years: 2018, 2024
lastupdated: "2024-12-06"

keywords: redis, databases, pub/sub, application

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Connecting an {{site.data.keyword.cloud_notm}} application
{: #ibmcloud-app}

Applications running in {{site.data.keyword.cloud_notm}} can be bound to your {{site.data.keyword.databases-for-redis_full}} deployment. 

## Connecting a Kubernetes service application
{: #ibmcloud-app-connect-kubernetes}

There are two steps to connecting a Cloud databases deployment to a Kubernetes Service application. First, your deployment needs to be bound to your cluster and its connection strings stored in a secret. The second step is configuring your application to use the connection strings.

The sample app in the [Connecting a Kubernetes service tutorial](/docs/cloud-databases?topic=cloud-databases-tutorial-k8s-app) provides a sample application that uses Node.js and demonstrates how to bind the sample application to a {{site.data.keyword.databases-for}} deployment.
{: .tip}

Before connecting your Kubernetes Service application to a deployment, make sure that the deployment and cluster are both in the same region and resource group.

### Binding your deployment
{: #ibmcloud-app-bind-deployment}

1. **Public or private endpoints**

  - **Public endpoints** - If you are using the default public service endpoint to connect to your deployment, you can run the `cluster service bind` command with your cluster name, the resource group, and your instance name or CRN.

    ```sh
    ibmcloud ks cluster service bind <YOUR_CLUSTER_NAME> <RESOURCE_GROUP> <INSTANCE_NAME_OR_CRN>
    ```
    
  - **Private endpoints** - If you want to use a private endpoint (if one is enabled on your deployment), then first you need to create a service key for your database. Kubernetes uses it when binding to the database.

    ```sh
    ibmcloud resource service-key-create <YOUR-PRIVATE-KEY> --instance-name <INSTANCE_NAME_OR_CRN> --service-endpoint private  
    ```
    
    The private service endpoint is selected with `--service-endpoint private`. After that, you bind the database to the Kubernetes cluster through the private endpoint with the `cluster service bind` command.

    ```sh
    ibmcloud ks cluster service bind <YOUR_CLUSTER_NAME> <RESOURCE_GROUP> <INSTANCE_NAME_OR_CRN> --key <YOUR-PRIVATE-KEY>
    ```

2. **Verify** - Verify that the Kubernetes secret was created in your cluster namespace. By running the following command, you get the API key for accessing the instance of your deployment in your account.

    ```sh
    kubectl get secrets --namespace=default
    ```
    
    For more information on binding services, see the [Kubernetes Service documentation](/docs/containers?topic=containers-service-binding#bind-services).

### Configuring in your Kubernetes app 
{: #ibmcloud-app-configuring-kubernetes}

When you bind your application to Kubernetes Service, it creates an environment variable from the cluster's secrets. Your deployment's connection information lives in `BINDING` as a JSON object. Load and parse the JSON object into your application to retrieve the information your application's driver needs to make a connection to the database. 

The [Connection Strings](/docs/databases-for-redis?topic=databases-for-redis-connection-strings#connection-string-breakdown) page contains a reference of the JSON fields.

For more information, see the [Kubernetes Service docs](https://cloud.ibm.com/docs/containers?topic=containers-service-binding#reference_secret).

## Pub/Sub
{: #ibmcloud-app-pubsub}

{{site.data.keyword.databases-for-redis}} supports Pub/Sub (publish/subscribe). Pub/Sub is a messaging technology that facilitates communication between different components in a distributed system.

For more information, see [Pub/Sub (publish/subscribe)](https://redis.com/glossary/pub-sub/){: external}.
