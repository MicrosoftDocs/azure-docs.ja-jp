---
title: Azure Cosmos DB の SQL API の Python サンプル
description: CRUD 操作など、Azure Cosmos DB の一般的なタスクについては、GitHub の Python のサンプルを参照してください。
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 08/11/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 34257281470f051f3d385aa729ca5a4f3591e61c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586252"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB の Python サンプル

> [!div class="op_single_selector"]
> * [.NET V2 SDK のサンプル](sql-api-dotnet-samples.md)
> * [.NET V3 SDK のサンプル](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK のサンプル](sql-api-java-sdk-samples.md)
> * [Node.js のサンプル](sql-api-nodejs-samples.md)
> * [Python のサンプル](sql-api-python-samples.md)
> * [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Azure Cosmos DB のリソースで CRUD 操作などの一般的な操作を実行するサンプル ソリューションは、[azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub リポジトリに含まれています。 この記事では、次の内容について説明します。

* 各 Python サンプル プロジェクト ファイルのタスクへのリンク。
* 関連する API リファレンス コンテンツへのリンク。

## <a name="prerequisites"></a>前提条件

- Cosmos DB アカウント。 オプションは次のとおりです。
    * Azure アクティブ サブスクリプション内で:
        * [Azure 無料アカウントを作成する](https://azure.microsoft.com/free)か、既存のサブスクリプションを使用します 
        * [Visual Studio 月単位クレジット](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB の Free レベル](https://docs.microsoft.com/azure/cosmos-db/optimize-dev-test#azure-cosmos-db-free-tier)
    * Azure アクティブ サブスクリプションなしで:
        * 30 日間継続するテスト環境として、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)。
        * [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 または 3.5.3 以降](https://www.python.org/downloads/) (`python` 実行可能ファイルが `PATH` に設定されていること)。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Visual Studio Code 用の Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)。
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="database-examples"></a>データベースのサンプル

[database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python サンプルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos データベースについて知るために、[データベース、コンテナー、アイテムの操作](databases-containers-items.md)に関する概念記事を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [データベースの作成](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [ID でのデータベースの読み取り](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [データベースに対するクエリの実行](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [アカウントのデータベースの一覧表示](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [データベースの削除](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>コンテナーの例

[container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) Python サンプルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos のコレクションについて知るために、[データベース、コンテナー、アイテムの操作](databases-containers-items.md)に関する概念記事を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [コンテナーに対するクエリの実行](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [コンテナーの作成](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [データベース内のすべてのコンテナーの一覧表示](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [ID によるコンテナーの取得](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [コンテナーのプロビジョニング済みスループットの管理](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer、container.replace_throughput|
| [コンテナーの削除](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>項目の例

[item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) Python サンプルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos のドキュメントについて知るために、[データベース、コンテナー、アイテムの操作](databases-containers-items.md)に関する概念記事を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [コンテナーでの項目の作成](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [ID による項目の読み取り](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [コンテナー内のすべての項目の読み取り](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [ID による項目に対するクエリの実行](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [項目の置換](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [項目のアップサート](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [項目の削除](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [コンテナー内の項目の変更フィードの取得](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>インデックス作成のサンプル

[index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python サンプルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB におけるインデックス作成について知るために、[インデックス作成ポリシー](index-policy.md)、[インデックスの種類](index-types.md)、[インデックスのパス](index-paths.md)に関する概念記事を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [インデックスからの特定項目の除外](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [特定項目にインデックスを付けることによる手動のインデックス作成の使用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [インデックス作成からのパスの除外](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |除外するパスは `IndexingPolicy` プロパティで定義します |
| [文字列の範囲のインデックスの使用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | 文字列データ型で範囲インデックスを使用してインデックス作成ポリシーを定義します。 `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [インデックス変換の実行](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (更新されたインデックス作成ポリシーを使用)|
| [パスにハッシュ インデックスが存在する場合にのみスキャンを使用する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | 項目に対してクエリを実行するときは、`enable_scan_in_query=True` および `enable_cross_partition_query=True` を設定します |
