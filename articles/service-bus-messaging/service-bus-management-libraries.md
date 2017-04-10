---
title: "Azure Service Bus 管理ライブラリ | Microsoft Docs"
description: ".NET から Service Bus の名前空間とエンティティを管理します。"
services: service-bus-messaging
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/6/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: dfd1ae52cc56a4d4b4c7ee3f69f0c454be607401
ms.openlocfilehash: 7b04227327235f788ecf4018b6c159d33113b63d


---

# <a name="service-bus-management-libraries"></a>Service Bus 管理ライブラリ

Service Bus 管理ライブラリは、Service Bus の名前空間とエンティティを動的にプロビジョニングできます。 これにより、複雑なデプロイとメッセージング シナリオを可能にし、プロビジョニングするエンティティをプログラムを使用して決定できます。 現在、これらのライブラリは .NET で使用できます。

## <a name="supported-functionality"></a>サポートされる機能

* 名前空間の作成、更新、削除
* キューの作成、更新、削除
* トピックの作成、更新、削除
* サブスクリプションの作成、更新、削除

## <a name="prerequisites"></a>前提条件

Service Bus 管理ライブラリの使用を開始するには、Azure Active Directory (AAD) で認証する必要があります。 AAD では、Azure リソースへのアクセスを提供するサービス プリンシパルとして認証する必要があります。 サービス プリンシパルを作成する方法について詳しくは、次の記事のいずれかをご覧ください。  

* [リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルを Azure Portal で作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

これらのチュートリアルにより `AppId` (クライアント ID)、`TenantId`、および `ClientSecret` (認証キー) が提供され、すべて管理ライブラリによって認証に使用されます。 実行するリソース グループに対する 'オーナー' のアクセス許可が必要です。

## <a name="programming-pattern"></a>プログラミング パターン

すべての Service Bus リソースを操作するパターンは、次の共通のプロトコルに従います。

1. `Microsoft.IdentityModel.Clients.ActiveDirectory` ライブラリを使用して Azure Active Directory からトークンを取得します。
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. `ServiceBusManagementClient` オブジェクトを作成します。
    ```csharp
    var creds = new TokenCredentials(token);
    var sbClient = new ServiceBusManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. CreateOrUpdate パラメーターを指定した値に設定します。
    ```csharp
    var queueParams = new QueueCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"],
        EnablePartitioning = true
    };
    ```

1. 呼び出しを実行します。
    ```csharp
    await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
    ```

## <a name="next-steps"></a>次のステップ
* [.NET 管理のサンプル](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.Management.ServiceBus リファレンス](/dotnet/api/Microsoft.Azure.Management.ServiceBus)



<!--HONumber=Jan17_HO3-->


