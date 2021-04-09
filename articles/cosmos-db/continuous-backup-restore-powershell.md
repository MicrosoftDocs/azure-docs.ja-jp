---
title: Azure PowerShell を使用して、Azure Cosmos DB で継続的バックアップとポイントインタイム リストアを構成する
description: Azure PowerShell を使用して、継続的バックアップとデータの復元を行うアカウントをプロビジョニングする方法について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381836"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>継続的バックアップとポイントインタイム リストア (プレビュー) を構成および管理する - Azure PowerShell を使用
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB のポイントインタイム リストア機能 (継続的バックアップ モード) は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Cosmos DB の特定の時点への復元機能 (プレビュー) を使用すると、コンテナー内で誤って行われた変更から回復し、削除されたアカウント、データベース、またはコンテナーの復元や、任意のリージョン (バックアップが存在していた場所) への復元が行えます。 継続的バックアップ モードにより、過去 30 日以内の任意の時点に復元できます。

この記事では、Azure PowerShell を使用して、継続的バックアップとデータの復元を行うアカウントをプロビジョニングする方法について説明します。

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Azure PowerShell をインストールする

1. Azure PowerShell から次のコマンドを実行して、`Az.CosmosDB` プレビュー モジュールをインストールします。これには、ポイントインタイム リストアに関連するコマンドが含まれています。

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. モジュールをインストールしたら、以下を使用して Azure にログインします。

   ```azurepowershell
   Connect-AzAccount
   ```

1. 次のコマンドを使用して、特定のサブスクリプションを選択します。

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>継続的バックアップを行う SQL API アカウントをプロビジョニングする

継続的バックアップを行うアカウントをプロビジョニングするには、通常のプロビジョニング コマンドに引数 `-BackupPolicyType Continuous` を追加します。

次のコマンドレットは、*myrg* リソース グループで "*米国西部*" リージョンに作成された継続的バックアップ ポリシーを持つ単一リージョン書き込みアカウント `pitracct2` の例です。

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>継続的バックアップを行う MongoDB API アカウントをプロビジョニングする

次のコマンドレットは、*myrg* リソース グループで "*米国西部*" リージョンに作成された継続的バックアップ アカウント *pitracct2* の例です。

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>復元操作をトリガーする

次のコマンドレットは、ターゲット アカウント、ソース アカウント、場所、リソース グループ、タイムスタンプを使用して restore コマンドで復元操作をトリガーする例です。

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**例 1:** アカウント全体を復元する。

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**例 2:** 特定のコレクションとデータベースを復元する。 この例では、myDB1 のコレクション myCol1、myCol2 と、データベース myDB2 全体 (その下にあるすべてのコンテナーが含まれます) を復元します。

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API の復元可能なリソースを列挙する

列挙コマンドレットは、さまざまなタイムスタンプで復元に使用できるリソースの検出に役立ちます。 また、復元可能なアカウント、データベース、コンテナー リソースでの主要イベントのフィードも示されます。

**現在のサブスクリプションで復元可能なすべてのアカウントを一覧表示する**

現在のサブスクリプションで復元可能なすべてのアカウントを一覧表示するには、`Get-AzCosmosDBRestorableDatabaseAccount` PowerShell コマンドを実行します。

応答には、復元可能なすべてのデータベース アカウント (ライブと削除済みの両方) と、それらの復元元として指定できるリージョンが含まれます。

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

アカウントの `CreationTime` または `DeletionTime` と同様に、リージョンにも `CreationTime` または `DeletionTime` があります。 これらの時間によって、適切なリージョンとそのリージョンに復元するときの有効な時間範囲を選択できます。

**ライブ データベース アカウント内のすべてのバージョンの SQL データベースを一覧表示する**

データベースのすべてのバージョンを一覧表示すると、データベースの実際の存在時間が不明なシナリオで、正しいデータベースを選択できます。

データベースのすべてのバージョンを一覧表示するには、次の PowerShell コマンドを実行します。 このコマンドは、ライブ アカウントでのみ機能します。 `DatabaseAccountInstanceId` と `LocationName` パラメーターは、`Get-AzCosmosDBRestorableDatabaseAccount` コマンドレットの応答の `name` および `location` プロパティから取得されます。 `DatabaseAccountInstanceId` 属性は、復元されるソース データベース アカウントの `instanceId` プロパティを参照します。


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**ライブ データベース アカウント内のデータベースの SQL コンテナーのすべてのバージョンを一覧表示する**

SQL コンテナーのすべてのバージョンを一覧表示するには、次のコマンドを使用します。 このコマンドは、ライブ アカウントでのみ機能します。 `DatabaseRid` パラメーターは復元するデータベースの `ResourceId` です。 これは、`Get-AzCosmosdbSqlRestorableDatabase` コマンドレットの応答で検出される `ownerResourceid` 属性の値です。 この応答には、このデータベース内のすべてのコンテナーで実行された操作の一覧も含まれています。

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**特定のタイムスタンプで復元可能なデータベースやコンテナーを見つける**

特定のタイムスタンプで復元可能なデータベースやコンテナーの一覧を取得するには、次のコマンドを使用します。 このコマンドは、ライブ アカウントでのみ機能します。

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>MongoDB の復元可能なリソースを列挙する

下に示す列挙コマンドは、さまざまなタイムスタンプで復元に使用できるリソースの検出に役立ちます。 また、復元可能なアカウント、データベース、コンテナー リソースでの主要イベントのフィードも示されます。 これらのコマンドは、ライブ アカウントでのみ機能し、SQL API コマンドに似ていますが、コマンド名には `sql` ではなく `MongoDB` を使用します。

**ライブ データベース アカウント内の MongoDB データベースのすべてのバージョンを一覧表示する**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**ライブ データベース アカウント内のデータベースの MongoDB コレクションのすべてのバージョンを一覧表示する**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**指定されたタイムスタンプとリージョンで復元に使用可能な mongodb データベース アカウントのすべてのリソースを一覧表示する**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>次のステップ

* [Azure CLI](continuous-backup-restore-command-line.md)、[Resource Manager](continuous-backup-restore-template.md)、または [Azure portal](continuous-backup-restore-portal.md) を使用して、継続的バックアップを構成および管理する
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
