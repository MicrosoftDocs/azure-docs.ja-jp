---
title: Azure CLI を使用して、Azure Cosmos DB で継続的バックアップとポイントインタイム リストアを構成します。
description: Azure CLI を使用して、継続的バックアップとデータの復元を行うアカウントをプロビジョニングする方法について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377331"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>継続的バックアップとポイントインタイム リストア (プレビュー) を構成および管理する - Azure CLI を使用
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB のポイントインタイム リストア機能 (継続的バックアップ モード) は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Cosmos DB の特定の時点への復元機能 (プレビュー) を使用すると、コンテナー内で誤って行われた変更から回復し、削除されたアカウント、データベース、またはコンテナーの復元や、任意のリージョン (バックアップが存在していた場所) への復元が行えます。 継続的バックアップ モードにより、過去 30 日以内の任意の時点に復元できます。

この記事では、Azure CLI を使用して、継続的バックアップとデータの復元を行うアカウントをプロビジョニングする方法について説明します。

## <a name="install-azure-cli"></a><a id="install"></a>Azure CLI のインストール

1. Azure CLI の最新バージョンをインストールします

   * [Azure CLI](/cli/azure/install-azure-cli) の最新バージョンまたは 2.17.1 より上位のバージョンをインストールします。
   * CLI を既にインストールしている場合は、`az upgrade` コマンドを使用して、最新バージョンに更新します。 このコマンドは、2.11 より上位の CLI バージョンでのみ機能します。 以前のバージョンがある場合は、上記のリンクを使用して最新バージョンをインストールします。

1. `cosmosdb-preview` CLI 拡張機能をインストールします。

   * ポイントインタイム リストア コマンドは、`cosmosdb-preview` 拡張機能で使用できます。
   * この拡張機能をインストールするには、コマンド `az extension add --name cosmosdb-preview` を実行します。
   * この拡張機能をアンインストールするには、コマンド `az extension remove --name cosmosdb-preview` を実行します。

1. サインインして、使用するサブスクリプションを選択する

   * `az login` コマンドで Azure アカウントにサインインします。
   * `az account set -s <subscriptionguid>` コマンドを使用して、必要なサブスクリプションを選択します。

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>継続的バックアップを行う SQL API アカウントのプロビジョニング

継続的バックアップを行う SQL API アカウントをプロビジョニングするには、通常のプロビジョニング コマンドと共に追加の引数 `--backup-policy-type Continuous` を渡す必要があります。 次のコマンドは、*myrg* リソース グループの下で "*米国西部*" リージョンに継続的バックアップ ポリシーを作成している `pitracct2` という名前の単一リージョン書き込みアカウントの例です。

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>継続的バックアップを行う MongoDB 用 Azure Cosmos DB API アカウントのプロビジョニング

次のコマンドは、*myrg* リソース グループの下で "*米国西部*" リージョンに継続的バックアップ ポリシーを作成している `pitracct3` という名前の単一リージョン書き込みアカウントの例を示しています。

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>CLI による復元操作のトリガー

復元をトリガーする最も簡単な方法は、ターゲット アカウントの名前、ソース アカウント、場所、リソース グループ、タイムスタンプ (UTC)、および必要に応じてデータベースとコンテナーの名前を指定して、復元コマンドを発行することです。 復元操作をトリガーする例を次に示します。

1. 既存のアカウントから復元して、新しい Azure Cosmos DB アカウントを作成します。

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. 既存のデータベース アカウントから選択したデータベースとコンテナーのみを復元して、新しい Azure Cosmos DB アカウントを作成します。

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API の復元可能なリソースの列挙

下記の列挙コマンドは、さまざまなタイムスタンプで復元に使用できるリソースを検出するのに役立ちます。 また、復元可能なアカウント、データベース、およびコンテナー リソースでのキー イベントのフィードも提供されます。

**現在のサブスクリプションで復元可能なすべてのアカウントを一覧表示する**

現在のサブスクリプションで復元可能なすべてのアカウントを一覧表示するには、次の CLI コマンドを実行します

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

応答には、復元可能なすべてのデータベース アカウント (ライブと削除済みの両方) と、復元可能なリージョンが含まれます。

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

アカウントの `CreationTime` または `DeletionTime` と同様に、リージョンにも `CreationTime` または `DeletionTime` があります。 これらの時間によって、そのリージョンに復元する適切なリージョンと有効な時間範囲を選択できます。

**ライブ データベース アカウント内のデータベースのすべてのバージョンを一覧表示する**

データベースのすべてのバージョンを一覧表示すると、データベースの存在の実際の時間が不明なシナリオで、正しいデータベースを選択できます。

データベースのすべてのバージョンを一覧表示するには、次の CLI コマンドを実行します。 このコマンドは、ライブ アカウントでのみ機能します。 `instanceId` と `location` パラメーターは、`az cosmosdb restorable-database-account list` コマンドの応答の `name` および `location` プロパティから取得されます。 instanceId 属性は、復元されるソース データベース アカウントのプロパティでもあります。

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

このコマンドの出力に、データベースが作成および削除されたタイミングが表示されるようになります。

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**ライブ データベース アカウント内のデータベースの SQL コンテナーのすべてのバージョンを一覧表示する**

SQL コンテナーのすべてのバージョンを一覧表示するには、次のコマンドを使用します。 このコマンドは、ライブ アカウントでのみ機能します。 `databaseRid` パラメーターは復元するデータベースの `ResourceId` です。 これは、`az cosmosdb sql restorable-database list` コマンドの応答で検出される `ownerResourceid` 属性の値です。

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

このコマンドの出力には、このデータベース内のすべてのコンテナーで実行された操作の一覧が含まれています。

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**特定のタイムスタンプで復元可能なデータベースやコンテナーを見つける**

特定のタイムスタンプで復元可能なデータベースやコンテナーの一覧を取得するには、次のコマンドを使用します。 このコマンドは、ライブ アカウントでのみ機能します。

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>MongoDB API アカウントの復元可能なリソースの列挙

下記の列挙コマンドは、さまざまなタイムスタンプで復元に使用できるリソースを検出するのに役立ちます。 また、復元可能なアカウント、データベース、およびコンテナー リソースでのキー イベントのフィードも提供されます。 SQL API と同様に、`az cosmosdb` コマンドを使用できますが、`sql` の代わりに `mongodb` をパラメーターとして使用します。 これらのコマンドは、ライブ アカウントでのみ機能します。

**ライブ データベース アカウント内の mongodb データベースのすべてのバージョンを一覧表示する**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**ライブ データベース アカウント内のデータベースの mongodb コレクションのすべてのバージョンを一覧表示する**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**指定されたタイムスタンプとリージョンで復元に使用可能な mongodb データベース アカウントのすべてのリソースを一覧表示する**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>次のステップ

* [Azure portal](continuous-backup-restore-portal.md)、[PowerShell](continuous-backup-restore-powershell.md)、または [Azure Resource Manager](continuous-backup-restore-template.md) を使用して、継続的バックアップを構成および管理します。
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
