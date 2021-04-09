---
title: Azure Cosmos DB のポイントインタイム リストア機能についてよく寄せられる質問。
description: この記事では、継続的バックアップ モードを使用することで実現する Azure Cosmos DB のポイントインタイム リストア機能についてよく寄せられる質問を示します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393226"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Azure Cosmos DB のポイントインタイム リストア機能 (プレビュー) についてよく寄せられる質問
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB のポイントインタイム リストア機能 (継続的バックアップ モード) は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、継続的バックアップ モードを使用することで実現する Azure Cosmos DB のポイントインタイム リストア機能 (プレビュー) についてよく寄せられる質問を示します。

## <a name="how-much-time-does-it-takes-to-restore"></a>復元にはどのくらいの時間がかかりますか?
復元時間は、データのサイズによって決まります。

### <a name="can-i-submit-the-restore-time-in-local-time"></a>復元時刻をローカル時刻で送信できますか?
データベースやコンテナーなどの主要リソースがその時点で存在していたかどうかによっては、復元が行われない場合があります。 確認するには、時刻を入力し、指定した時刻について、選択したデータベースまたはコンテナーを調べます。 復元するリソースが存在しない場合、復元プロセスは機能しません。

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>アカウントが復元されているかどうかは、どのようにして追跡できますか?
復元コマンドを送信して、同じページで待機します。操作が完了すると、ステータス バーに、アカウントが正常に復元されたというメッセージが表示されます。 また、復元されたアカウントを検索し、[復元されているアカウントの状態を追跡する](continuous-backup-restore-portal.md#track-restore-status)こともできます。 復元の進行中は、アカウントの状態は *Creating* (作成中) になります。復元操作が完了すると、アカウントの状態は *Online* (オンライン) に変わります。

同様に、PowerShell と CLI では、次のように `az cosmosdb show` コマンドを実行して、復元操作の進行状況を追跡できます。

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

アカウントがオンラインになっている場合、provisioningState には *Succeeded* (成功) と表示されます。

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>アカウントが別のアカウントから復元されたかどうかを確認するにはどうすればよいですか?
`az cosmosdb show` コマンドを実行します。出力で、`createMode` プロパティの値を確認できます。 値が **Restore** に設定されている場合は、 アカウントが別のアカウントから復元されたことを示しています。 `restoreParameters` プロパティには、ソース アカウント ID を持つ `restoreSource` などの詳細があります。 `restoreSource` パラメーターの最後の GUID は、ソース アカウントの instanceId です。

たとえば、次の出力では、ソース アカウントのインスタンス ID は *7b4bb-f6a0-430e-ade1-638d781830cc* になります。

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>共有スループット データベース、または共有スループット データベース内のコンテナーを復元するとどうなりますか?
共有スループット データベース全体が復元されます。 共有スループット データベース内のコンテナーのサブセットを復元用に選択することはできません。

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>アカウント定義で InstanceID はどのように使用されますか?
いかなる時点においても、Azure Cosmos DB アカウントの `accountName` プロパティは、それが有効な間はグローバルに一意です。 ただし、アカウントが削除された後は、同じ名前で別のアカウントを作成することが可能です。したがって、"accountName" はアカウントのインスタンスを識別するのに十分ではなくなります。 

ID または `instanceId` はアカウントのインスタンスのプロパティです。これは、複数のアカウント (ライブおよび削除済み) で復元用に同じ名前が使用されている場合に、それらを明確に区別するために使用されます。 インスタンス ID を取得するには、`Get-AzCosmosDBRestorableDatabaseAccount` または `az cosmosdb restorable-database-account` コマンドを実行します。 name 属性の値は "InstanceID" を表します。

## <a name="next-steps"></a>次のステップ

* [継続的バックアップ](continuous-backup-restore-introduction.md) モードとは
* [Azure portal](continuous-backup-restore-portal.md)、[PowerShell](continuous-backup-restore-powershell.md)、[CLI](continuous-backup-restore-command-line.md)、または [Azure Resource Manager](continuous-backup-restore-template.md) を使用して、継続的バックアップを構成および管理します。
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)