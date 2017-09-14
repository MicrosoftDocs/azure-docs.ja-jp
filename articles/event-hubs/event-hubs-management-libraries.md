---
title: "Azure Event Hubs 管理ライブラリ | Microsoft Docs"
description: ".NET から Event Hubs の名前空間とエンティティを管理します。"
services: event-hubs
cloud: na
documentationcenter: na
author: sethmanheim
manager: timlt
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 0d659cb860a6c98342b548212820efe046decfcc
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

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

これらのチュートリアルでは、`AppId` (クライアント ID)、`TenantId`、`ClientSecret` (認証キー) が提供されます。これらはすべて管理ライブラリによって認証に使用されます。 実行するリソース グループに対する**所有者**のアクセス許可が必要です。

## <a name="programming-pattern"></a>プログラミング パターン

Event Hubs リソースを操作するパターンは、次の共通のプロトコルに従います。

1. `Microsoft.IdentityModel.Clients.ActiveDirectory` ライブラリを使用して AAD からトークンを取得します。
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

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

1. `CreateOrUpdate` パラメーターを指定した値に設定します。
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

