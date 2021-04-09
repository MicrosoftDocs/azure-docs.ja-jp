---
title: プログラムによる Azure Service Bus エンティティの作成 | Microsoft Docs
description: この記事では、Service Bus の名前空間とエンティティを動的に、またはプログラミングによってプロビジョニングする方法について説明します。
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 57192ab2ee1624cb18de832ac91c95290da727df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539867"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Service Bus の名前空間とエンティティの動的なプロビジョニング 
Azure Service Bus 管理ライブラリは、Service Bus の名前空間とエンティティを動的にプロビジョニングできます。 これにより、複雑なデプロイとメッセージング シナリオが可能になり、プロビジョニングするエンティティをプログラムを使用して決定できます。 現在、これらのライブラリは .NET で使用できます。

## <a name="overview"></a>概要
Service Bus エンティティの作成と管理に使用できる管理ライブラリは 3 つあります。 これらは次のとおりです。

- [Azure.Messaging.ServiceBus.Administration](#azuremessagingservicebusadministration)
- [Microsoft.Azure.ServiceBus.Management](#microsoftazureservicebusmanagement)
- [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)

これらのすべてのパッケージが、**キュー、トピックおよびサブスクリプション** の作成、取得、一覧表示、削除、更新、削除および更新処理をサポートします。 ただし、**名前空間** の作成、更新、一覧表示、取得、および削除操作、SAS キーの一覧表示と再生成などをサポートしているのは、[Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus) のみです。 

Microsoft.Azure.Management.ServiceBus ライブラリは、Azure Active Directory (Azure AD) 認証でのみ動作し、接続文字列の使用はサポートしていません。 これに対し、他の 2 つのライブラリ (Azure.Messaging.ServiceBus と Microsoft.Azure.ServiceBus) では、サービスの認証に接続文字列を使用でき、使いやすくなっています。 これらのライブラリ間では、Azure.Messaging.ServiceBus が最新であり、使用が推奨されています。

これらのライブラリの詳細は、以下のセクションのとおりです。 

## <a name="azuremessagingservicebusadministration"></a>Azure.Messaging.ServiceBus.Administration
名前空間、キュー、トピック、およびサブスクリプションの管理には、[Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration) 名前空間の [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) クラスを使用できます。 コード例を次に示します。 完全な例については、[CRUD の例](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs)に関するページを参照してください。

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft.Azure.ServiceBus.Management 
名前空間、キュー、トピック、およびサブスクリプションの管理には、[Microsoft.Azure.ServiceBus.Management](/dotnet/api/microsoft.azure.servicebus.management) 名前空間の [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) クラスを使用できます。 コード例を次に示します。 

> [!NOTE]
> 最新の SDK である `Azure.Messaging.ServiceBus.Administration` ライブラリの `ServiceBusAdministrationClient` クラスを使用することをお勧めします。 詳細については、[最初のセクション](#azuremessagingservicebusadministration)を参照してください。 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
このライブラリは、Azure Resource Manager ベースのコントロール プレーン SDK の一部です。 

### <a name="prerequisites"></a>前提条件

このライブラリの使用を開始するには、Azure Active Directory (Azure AD) サービスで認証する必要があります。 Azure AD では、Azure リソースへのアクセスを提供するサービス プリンシパルとして認証する必要があります。 サービス プリンシパルを作成する方法については、次の記事のいずれかをご覧ください。  

* [リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルを Azure Portal で作成する](../active-directory/develop/howto-create-service-principal-portal.md)
* [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する](/cli/azure/create-an-azure-service-principal-azure-cli)

これらのチュートリアルでは、`AppId` (クライアント ID)、`TenantId`、`ClientSecret` (認証キー) が提供されます。これらはすべて管理ライブラリによって認証に使用されます。 実行するリソース グループに対しては、少なくとも [**Azure Service Bus データ所有者**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)または [**共同作成者**](../role-based-access-control/built-in-roles.md#contributor)のアクセス許可が必要です。

### <a name="programming-pattern"></a>プログラミング パターン

すべての Service Bus リソースを操作するパターンは、次の共通のプロトコルに従います。

1. **Microsoft.IdentityModel.Clients.ActiveDirectory** ライブラリを使用して、Azure AD からトークンを取得します。
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. `ServiceBusManagementClient` オブジェクトを作成します。

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. `CreateOrUpdate` パラメーターを指定した値に設定します。

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. 呼び出しを実行します。

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

### <a name="complete-code-to-create-a-queue"></a>キューを作成するための完全なコード
Service Bus キューを作成するサンプル コードは次のとおりです。 完全な例については、[GitHub 上の .NET 管理サンプル](https://github.com/Azure-Samples/service-bus-dotnet-management/)を参照してください。 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

## <a name="fluent-library"></a>Fluent ライブラリ
Fluent ライブラリを使用して Service Bus エンティティを管理する例については、[このサンプル](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus)を参照してください。 

## <a name="next-steps"></a>次の手順
次の参照トピックを参照してください。 

- [Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft.Azure.ServiceBus.Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)