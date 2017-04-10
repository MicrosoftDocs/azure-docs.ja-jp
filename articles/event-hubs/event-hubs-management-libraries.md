---
title: "Azure Event Hubs 管理ライブラリ | Microsoft Docs"
description: ".NET から Event Hubs の名前空間とエンティティを管理します。"
services: event-hubs
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/6/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: dfd1ae52cc56a4d4b4c7ee3f69f0c454be607401
ms.openlocfilehash: 84075b60074b0607c14787db72c8dff8b701a8ea


---

# <a name="event-hubs-management-libraries"></a>Event Hubs 管理ライブラリ

Event Hubs 管理ライブラリでは、Event Hubs の名前空間とエンティティを動的にプロビジョニングできます。 これにより、複雑なデプロイとメッセージング シナリオが可能になり、プロビジョニングするエンティティをプログラムを使用して決定できます。 現在、これらのライブラリは .NET で使用できます。

## <a name="supported-functionality"></a>サポートされる機能

* 名前空間の作成、更新、削除
* Event Hubs の作成、更新、削除
* コンシューマー グループの作成、更新、削除

## <a name="prerequisites"></a>前提条件

Event Hubs 管理ライブラリの使用を開始するには、Azure Active Directory (AAD) で認証する必要があります。 AAD では、Azure リソースへのアクセスを提供するサービス プリンシパルとして認証する必要があります。 サービス プリンシパルを作成する方法については、次の記事のいずれかをご覧ください。  

* [リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルを Azure Portal で作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

これらのチュートリアルでは、`AppId` (クライアント ID)、`TenantId`、`ClientSecret` (認証キー) が提供されます。これらはすべて管理ライブラリによって認証に使用されます。 実行するリソース グループに対する "所有者" のアクセス許可が必要です。

## <a name="programming-pattern"></a>プログラミング パターン

Event Hubs リソースを操作するパターンは、次の共通のプロトコルに従います。

1. `Microsoft.IdentityModel.Clients.ActiveDirectory` ライブラリを使用して Azure Active Directory からトークンを取得します。
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. `EventHubManagementClient` オブジェクトを作成します。
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. CreateOrUpdate パラメーターを指定した値に設定します。
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. 呼び出しを実行します。
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>次のステップ
* [.NET 管理のサンプル](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub リファレンス](/dotnet/api/Microsoft.Azure.Management.EventHub) 



<!--HONumber=Jan17_HO3-->


