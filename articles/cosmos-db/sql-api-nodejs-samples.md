---
title: Azure Cosmos データベースのデータを管理する例 (Node.js)
description: CRUD 操作など、Azure Cosmos DB の一般的なタスクについては、GitHub の Node.js のサンプルを参照してください。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: e2d8316e0b44593e220a0c9a9358b465ad39c9eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216838"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Azure Cosmos DB のデータを管理する例 (Node.js)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK のサンプル](sql-api-dotnet-samples.md)
> * [.NET V3 SDK のサンプル](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK のサンプル](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK のサンプル](sql-api-spring-data-sdk-samples.md)
> * [Node.js のサンプル](sql-api-nodejs-samples.md)
> * [Python のサンプル](sql-api-python-samples.md)
> * [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB のリソースで CRUD 操作などの一般的な操作を実行するサンプル ソリューションは、[azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) GitHub リポジトリに含まれています。 この記事では、次の内容について説明します。

* 各 Node.js サンプル プロジェクト ファイルのタスクへのリンク。
* 関連する API リファレンス コンテンツへのリンク。

**前提条件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)ことができます。Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[JavaScript SDK](sql-api-sdk-node.md) も必要となります。
   
   > [!NOTE]
   > 各サンプルは自己完結型であり、自身をセットアップし、自身をクリーンアップします。 そのため、一連のサンプルで、[Containers.create](/javascript/api/%40azure/cosmos/containers) の呼び出しが複数回発行されます。 これが行われるたびに、作成するコンテナーのパフォーマンス レベルに従い、1 時間分の使用量がサブスクリプションに課金されます。
   > 
   > 

## <a name="database-examples"></a>データベースのサンプル

[DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) ファイルには、データベースに対する CRUD 操作の実行方法が紹介されています。 以下のサンプルを実行する前に Azure Cosmos データベースについて知るために、[データベース、コンテナー、アイテムの操作](account-databases-containers-items.md)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [データベースの作成 (データベースが存在しない場合)](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases#createifnotexists-databaserequest--requestoptions-) |
| [アカウントのデータベースの一覧表示](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases#readall-feedoptions-) |
| [ID でのデータベースの読み取り](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database#read-requestoptions-) |
| [データベースの削除](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database#delete-requestoptions-) |

## <a name="container-examples"></a>コンテナーの例

[ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) ファイルには、コンテナーに対する CRUD 操作の実行方法が紹介されています。 以下のサンプルを実行する前に Azure Cosmos のコレクションについて知るために、[データベース、コンテナー、アイテムの操作](account-databases-containers-items.md)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [コンテナーの作成 (コンテナーが存在しない場合)](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers#createifnotexists-containerrequest--requestoptions-) |
| [アカウントのコンテナーの一覧表示](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers#readall-feedoptions-) |
| [コンテナーの定義の読み取り](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container#read-requestoptions-) |
| [コンテナーの削除](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container#delete-requestoptions-) |

## <a name="item-examples"></a>項目の例

[ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) ファイルには、項目に対する CRUD 操作の実行方法が紹介されています。 以下のサンプルを実行する前に Azure Cosmos のドキュメントについて知るために、[データベース、コンテナー、アイテムの操作](account-databases-containers-items.md)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [項目の作成](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items#create-t--requestoptions-) |
| [コンテナー内のすべての項目の読み取り](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items#readall-feedoptions-) |
| [ID での項目の読み取り](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item#read-requestoptions-) |
| [項目の読み取り (項目が変更されている場合のみ)](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [ドキュメントのクエリ](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items) |
| [項目の置換](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item) |
| [条件付き ETag チェックによる項目の置換](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [項目の削除](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item) |

## <a name="indexing-examples"></a>インデックス作成のサンプル

[IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) ファイルには、インデックスの管理方法が紹介されています。 以下のサンプルを実行する前に Azure Cosmos DB におけるインデックス作成について知るために、[インデックス作成ポリシー](index-policy.md)、[インデックスの種類](index-overview.md#index-types)、[インデックスのパス](index-policy.md#include-exclude-paths)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [特定の項目の手動でのインデックス作成](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [インデックスからの特定の項目の手動での除外](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [インデックスからのパスの除外](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy#excludedpaths) |
| [文字列のパスでの範囲インデックスの作成](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind)、[IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy)、[Items.query](/javascript/api/%40azure/cosmos/items) |
| [既定の indexPolicy のコンテナーを作成し、オンラインで更新する](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers)

## <a name="server-side-programming-examples"></a>サーバー側プログラミングのサンプル

[ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) プロジェクトの [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB のサーバー側プログラミングについて知るために、「[ストアド プロシージャ、トリガー、およびユーザー定義関数](stored-procedures-triggers-udfs.md)」の概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [ストアド プロシージャの作成](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures) |
| [ストアド プロシージャの実行](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure) |

サーバー側プログラミングの詳細については、「[Azure Cosmos DB server-side programming: Stored procedures, database triggers, and UDFs (Azure Cosmos DB のサーバー側プログラミング: ストアド プロシージャ、データベース トリガー、UDF)](stored-procedures-triggers-udfs.md)」をご覧ください。
