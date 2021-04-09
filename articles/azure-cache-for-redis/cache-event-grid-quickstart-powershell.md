---
title: 'クイックスタート: PowerShell を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする'
description: Azure Event Grid を使用して Azure Cache for Redis のイベントをサブスクライブし、それらのイベントを Webhook に送信して Web アプリケーションで処理します。
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 6c3b433a8e433f39b723a7155bb6de116857efca
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508165"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>クイックスタート: PowerShell を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする

Azure Event Grid は、クラウドのイベント処理サービスです。 このクイックスタートでは、Azure PowerShell を使用して Azure Cache for Redis のイベントをサブスクライブし、イベントをトリガーして結果を表示します。 

通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 ただし単純化するために、このクイックスタートではメッセージを収集して表示する Web アプリにイベントを送信します。 このクイックスタートの手順の最後に、イベント データが Web アプリに送信済みであることを確認できます。

## <a name="setup"></a>セットアップ

このクイックスタートでは、Azure PowerShell の最新バージョンを実行していることを前提にしています。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](/powershell/azure/install-Az-ps)に関するページを参照してください。

## <a name="sign-in-to-azure"></a>Azure にサインインする

`Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従って認証を行います。

```powershell
Connect-AzAccount
```

この例では、**westus2** を使って、全体で使用される 1 つの変数に選択肢を格納しています。

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

Event Grid のトピックは、個々の Azure リソースとしてデプロイされるため、Azure リソース グループにプロビジョニングする必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。

次の例では、**gridResourceGroup** という名前のリソース グループを **westus2** の場所に作成します。  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis インスタンスを作成する 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
PowerShell でのキャッシュ インスタンスの作成について詳しくは、[Azure PowerShell リファレンス](/powershell/module/az.rediscache/new-azrediscache)を参照してください。 

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

トピックをサブスクライブする前に、イベント メッセージ用のエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

`<your-site-name>` は、Web アプリの一意の名前に置き換えてください。 Web アプリ名は、DNS エントリの一部であるため、一意である必要があります。

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

現在表示されているメッセージがないサイトが表示されます。

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="空の Event Grid ビューアー サイト。":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Azure Cache for Redis イベントをサブスクライブする

この手順では、トピックをサブスクライブすることによって、追跡するイベントを Event Grid に伝えます。次の例では、作成したキャッシュ インスタンスをサブスクライブし、Web アプリの URL をイベント通知のエンドポイントとして渡しています。 Web アプリのエンドポイントには、サフィックス `/api/updates/` が含まれている必要があります。

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 目のアイコンを選択してイベント データを展開します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 Web アプリには、サブスクリプションを検証するコードが含まれています。

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid ビューアー。":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Azure Cache for Redis からイベントをトリガーする

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
PowerShell でのインポートについて詳しくは、[Azure PowerShell リファレンス](/powershell/module/az.rediscache/import-azrediscache)を参照してください。 

以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 Web アプリを表示して、送信したイベント確認します。

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>リソースをクリーンアップする
引き続きこの Azure Cache for Redis インスタンスとイベント サブスクリプションを使用する場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 引き続き使用する予定がない場合は、次のコマンドを使用して、このクイックスタートで作成したリソースを削除します。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次のステップ

これで、トピックを作成し、イベントをサブスクライブする方法がわかったので、Azure Cache for Redis のイベントについて、また Event Grid でできることについて、さらに情報を収集しましょう。

- [Azure Cache for Redis イベントへの対応](cache-event-grid.md)
- [Event Grid について](../event-grid/overview.md)