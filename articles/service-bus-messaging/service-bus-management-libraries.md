---
title: Azure Service Bus 管理ライブラリ | Microsoft Docs
description: .NET から Service Bus の名前空間とメッセージング エンティティを管理します。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: d6c2cd813e96b40fc9f95785a1fd28e324a0437b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850970"
---
# <a name="service-bus-management-libraries"></a>Service Bus 管理ライブラリ

Azure Service Bus 管理ライブラリは、Service Bus の名前空間とエンティティを動的にプロビジョニングできます。 これにより、複雑なデプロイとメッセージング シナリオが可能になり、プロビジョニングするエンティティをプログラムを使用して決定できます。 現在、これらのライブラリは .NET で使用できます。

## <a name="supported-functionality"></a>サポートされる機能

* 名前空間の作成、更新、削除
* キューの作成、更新、削除
* トピックの作成、更新、削除
* サブスクリプションの作成、更新、削除

## <a name="prerequisites"></a>前提条件

Service Bus 管理ライブラリの使用を開始するには、Azure Active Directory (Azure AD) サービスで認証する必要があります。 Azure AD では、Azure リソースへのアクセスを提供するサービス プリンシパルとして認証する必要があります。 サービス プリンシパルを作成する方法については、次の記事のいずれかをご覧ください。  

* [リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルを Azure Portal で作成する](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

これらのチュートリアルでは、`AppId` (クライアント ID)、`TenantId`、`ClientSecret` (認証キー) が提供されます。これらはすべて管理ライブラリによって認証に使用されます。 実行するリソース グループに対する**所有者**のアクセス許可が必要です。

## <a name="programming-pattern"></a>プログラミング パターン

すべての Service Bus リソースを操作するパターンは、次の共通のプロトコルに従います。

1. **Microsoft.IdentityModel.Clients.ActiveDirectory** ライブラリを使用して、Azure AD からトークンを取得します。
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
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

## <a name="next-steps"></a>次の手順

* [.NET 管理のサンプル](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.Management.ServiceBus API リファレンス](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
