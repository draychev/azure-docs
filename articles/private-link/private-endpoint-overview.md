---
title: What is an Azure Private Endpoint?
description: Learn about Azure Private Endpoint
services: private-link
author: asudbring
# Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure private endpoints so that I can securely connect to my Azure PaaS services within the virtual network.

ms.service: private-link
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: allensu
---
# What is Azure Private Endpoint?

Azure Private Endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. Private Endpoint uses a private IP address from your VNet, effectively bringing the service into your VNet. 

The service could be an Azure service such as:

* Azure Storage
* Azure Cosmos DB
* Azure SQL Database
* Your own service using a [Private Link Service](private-link-service-overview.md).
  
## Private Endpoint properties 
 A Private Endpoint specifies the following properties: 


|Property  |Description |
|---------|---------|
|Name    |    A unique name within the resource group.      |
|Subnet    |  The subnet to deploy and where the private IP address is assigned. For subnet requirements, see the Limitations section in this article.         |
|Private Link Resource    |   The private link resource to connect using resource ID or alias, from the list of available types. A unique network identifier will be generated for all traffic sent to this resource.       |
|Target subresource   |      The subresource to connect. Each private link resource type has different options to select based on preference.    |
|Connection approval method    |  Automatic or manual. Depending on Azure Role based access control permissions, your private endpoint can be approved automatically. If you try to connect to a private link resource without Azure role-based access control, use the manual method to allow the owner of the resource to approve the connection.        |
|Request Message     |  You can specify a message for requested connections to be approved manually. This message can be used to identify a specific request.        |
|Connection status   |   A read-only property that specifies if the private endpoint is active. Only private endpoints in an approved state can be used to send traffic. More states available: <br>-**Approved**: Connection was automatically or manually approved and is ready to be used.</br><br>-**Pending**: Connection was created manually and is pending approval by the private link resource owner.</br><br>-**Rejected**: Connection was rejected by the private link resource owner.</br><br>-**Disconnected**: Connection was removed by the private link resource owner. The private endpoint becomes informative and should be deleted for cleanup. </br>|

Here are some key details about private endpoints: 
- Private endpoint enables connectivity between the consumers from the same VNet, regionally peered VNets, globally peered VNets and on premises using [VPN](https://azure.microsoft.com/services/vpn-gateway/) or [Express Route](https://azure.microsoft.com/services/expressroute/) and services powered by Private Link.
 
- Network connections can only be initiated by clients connecting to the private endpoint. Service providers don't have routing configuration to create connections into service consumers. Connections can only be established in a single direction.

- When creating a private endpoint, a read-only network interface is created for the lifecycle of the resource. The interface is assigned a dynamic private IP address from the subnet that maps to the private link resource. The value of the private IP address remains unchanged for the entire lifecycle of the private endpoint.
 
- The private endpoint must be deployed in the same region and subscription as the virtual network. 
 
- The private link resource can be deployed in a different region than the virtual network and private endpoint.
 
- Multiple private endpoints can be created using the same private link resource. For a single network using a common DNS server configuration, the recommended practice is to use a single private endpoint for a given private link resource to avoid duplicate entries or conflicts in DNS resolution. 
 
- Multiple private endpoints can be created on the same or different subnets within the same virtual network. There are limits to the number of private endpoints you can create in a subscription. For details, see [Azure limits](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- The subscription from the private link resource must also be registered with Microsoft. Network resource provider. For details, see [Azure Resource Providers](../azure-resource-manager/management/resource-providers-and-types.md).

 
## Private link resource 
A private link resource is the destination target of a given private endpoint. The table below lists the available private endpoint resources: 
 
| Private link resource name | Resource type | Subresources |
| ---------------------------| ------------- | ------------- |
| **Azure App Configuration** | Microsoft.Appconfiguration/configurationStores | configurationStores |
| **Azure Automation** | Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
| **Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Table |
| **Azure Batch** | Microsoft.Batch/batchAccounts | batch account |
| **Azure Cache for Redis** | Microsoft.Cache/Redis | redisCache |
| **Azure Cache for Redis Enterprise** | Microsoft.Cache/redisEnterprise | redisEnterprise |
| **Cognitive Services** | Microsoft.CognitiveServices/accounts | account |
| **Azure Managed Disks** | Microsoft.Compute/diskAccesses | managed disk |
| **Azure Container Registry** | Microsoft.ContainerRegistry/registries | registry |
| **Azure Kubernetes Service - Kubernetes API** | Microsoft.ContainerService/managedClusters | management |
| **Azure Data Factory** | Microsoft.DataFactory/factories | data factory |
| **Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers | mariadbServer |
| **Azure Database for MySQL** | Microsoft.DBforMySQL/servers | mysqlServer |
| **Azure Database for PostgreSQL - Single server** | Microsoft.DBforPostgreSQL/servers | postgresqlServer |
| **Azure IoT Hub** | Microsoft.Devices/IotHubs | iotHub |
| **Microsoft Digital Twins** | Microsoft.DigitalTwins/digitalTwinsInstances | digitaltwinsinstance |
| **Azure Event Grid** | Microsoft.EventGrid/domains | domain |
| **Azure Event Grid** | Microsoft.EventGrid/topics  | Event grid topic |
| **Azure Event Hub** | Microsoft.EventHub/namespaces | namespace |
| **Azure API for FHIR** | Microsoft.HealthcareApis/services | service |
| **Azure Keyvault HSM** | Microsoft.Keyvault/managedHSMs | HSM |
| **Azure Key Vault** | Microsoft.KeyVault/vaults | vault |
| **Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces | amlworkspace |
| **Azure Migrate** | Microsoft.Migrate/assessmentProjects | project |
| **Application Gateway** | Microsoft.Network/applicationgateways | application gateway |
| **Private Link Service** (Your own service) |  Microsoft.Network/privateLinkServices | empty |
| **Power BI** | Microsoft.PowerBI/privateLinkServicesForPowerBI | Power BI |
| **Azure Purview** | Microsoft.Purview/accounts | account |
| **Azure Backup** | Microsoft.RecoveryServices/vaults | vault |
| **Azure Relay** | Microsoft.Relay/namespaces | namespace |
| **Microsoft Search** | Microsoft.Search/searchServices | search service |
| **Azure Service Bus** | Microsoft.ServiceBus/namespaces | namespace |
| **SignalR** | Microsoft.SignalRService/SignalR | signalr |
| **SignalR** | Microsoft.SignalRService/webPubSub | webpubsub |
| **Azure SQL Database** | Microsoft.Sql/servers | Sql Server (sqlServer) |
| **Azure Storage** | Microsoft.Storage/storageAccounts | Blob (blob, blob_secondary)<BR> Table (table, table_secondary)<BR> Queue (queue, queue_secondary)<BR> File (file, file_secondary)<BR> Web (web, web_secondary) |
| **Azure File Sync** | Microsoft.StorageSync/storageSyncServices | File Sync Service |
| **Azure Synapse** | Microsoft.Synapse/privateLinkHubs | synapse |
| **Azure Synapse Analytics** | Microsoft.Synapse/workspaces | Sql, SqlOnDemand, Dev | 
| **Azure App Service** | Microsoft.Web/hostingEnvironments | hosting environment |
| **Azure App Service** | Microsoft.Web/sites | sites |
| **Azure App Service** | Microsoft.Web/staticSites | staticSite |

 
## Network security of private endpoints 
When using private endpoints for Azure services, traffic is secured to a specific private link resource. The platform performs an access control to validate network connections reaching only the specified private link resource. To access more resources within the same Azure service, extra private endpoints are required. 
 
You can completely lock down your workloads from accessing public endpoints to connect to a supported Azure service. This control provides an extra network security layer to your resources by providing a built-in exfiltration protection that prevents access to other resources hosted on the same Azure service. 
 
## Access to a private link resource using approval workflow 
You can connect to a private link resource using the following connection approval methods:
- **Automatically** approved when you own or have permission on the specific private link resource. The permission required is based on the private link resource type in the following format: Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionsApproval/action
- **Manual** request when you don't have the permission required and would like to request access. An approval workflow will be initiated. The private endpoint and later private endpoint connections will be created in a "Pending" state. The private link resource owner is responsible to approve the connection. After it's approved, the private endpoint is enabled to send traffic normally, as shown in the following approval workflow diagram.  

![workflow approval](media/private-endpoint-overview/private-link-paas-workflow.png)
 
The private link resource owner can do the following actions over a private endpoint connection: 

- Review all private endpoint connections details. 
- Approve a private endpoint connection. The corresponding private endpoint will be enabled to send traffic to the private link resource. 
- Reject a private endpoint connection. The corresponding private endpoint will be updated to reflect the status.
- Delete a private endpoint connection in any state. The corresponding private endpoint will be updated with a disconnected state to reflect the action, the private endpoint owner can only delete the resource at this point. 
 
> [!NOTE]
> Only a private endpoint in an approved state can send traffic to a given private link resource. 

### Connecting using Alias
Alias is a unique moniker that is generated when the service owner creates the private link service behind a standard load balancer. Service owner can share this Alias with their consumers offline. Consumers can request a connection to private link service using either the resource URI or the Alias. If you want to connect using. Alias, you must create private endpoint using manual connection approval method. For using manual connection approval method, set manual request parameter to true during private endpoint create flow. Look at [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) and [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) for details. 

## DNS configuration 
When connecting to a private link resource using a fully qualified domain name (FQDN) as part of the connection string, it's important to correctly configure your DNS settings to resolve to the given private IP address. Existing Azure services might already have a DNS configuration to use when connecting over a public endpoint. This configuration must be overwritten to connect using your private endpoint. 
 
The network interface associated with the private endpoint contains the complete set of information required to configure your DNS, including FQDN and private IP addresses given for a private link resource. 

For complete detailed information about recommendations to configure DNS for Private Endpoints, see [Private Endpoint DNS configuration](private-endpoint-dns.md).



 
## Limitations
 
The following table includes a list of known limitations when using private endpoints: 


|Limitation |Description |Mitigation  |
|---------|---------|---------|
|Network Security Group (NSG) rules and User-Defined Routes don't apply to Private Endpoint    | NSG isn't supported on private endpoints. While subnets containing the private endpoint can have NSG associated with it, the rules won't be effective on traffic processed by the private endpoint. You must have [network policies enforcement disabled](disable-private-endpoint-network-policy.md) to deploy private endpoints in a subnet. NSG is still enforced on other workloads hosted on the same subnet. Routes on any client subnet will be using an /32 prefix, changing the default routing behavior requires a similar UDR  | Control the traffic by using NSG rules for outbound traffic on source clients. Deploy individual routes with /32 prefix to override private endpoint routes. NSG Flow logs and monitoring information for outbound connections are still supported and can be used        |


## Next steps
- [Create a Private Endpoint for Azure Web Apps using the portal](create-private-endpoint-portal.md)
- [Create a Private Endpoint for Azure Web Apps using PowerShell](create-private-endpoint-powershell.md)
- [Create a Private Endpoint for Azure Web Apps using CLI](create-private-endpoint-cli.md)
- [Create a Private Endpoint for Storage account using the portal](./tutorial-private-endpoint-storage-portal.md)
- [Create a Private Endpoint for Azure Cosmos account using the portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Create your own Private Link service using Azure PowerShell](create-private-link-service-powershell.md)
- [Create your own Private Link for Azure Database for PostgreSQL - Single server using the portal](../postgresql/howto-configure-privatelink-portal.md)
- [Create your own Private Link for Azure Database for PostgreSQL - Single server using CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Create your own Private Link for Azure Database for MySQL using the portal](../mysql/howto-configure-privatelink-portal.md)
- [Create your own Private Link for Azure Database for MySQL using CLI](../mysql/howto-configure-privatelink-cli.md)
- [Create your own Private Link for Azure Database for MariaDB using the portal](../mariadb/howto-configure-privatelink-portal.md)
- [Create your own Private Link for Azure Database for MariaDB using CLI](../mariadb/howto-configure-privatelink-cli.md)
- [Create your own Private Link for Azure Key Vault using the portal and CLI](../key-vault/general/private-link-service.md)
