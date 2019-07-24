---
title: Azure Cosmos DB は:SQL API の非同期 Java のサンプル
description: CRUD 操作など、Azure Cosmos DB SQL API を使う一般的なタスクについては、GitHub の非同期 Java のサンプルを参照してください。
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 06/18/2018
ms.author: sngun
ms.openlocfilehash: 34551dcc64e6add2baa1ec94067037c98de67abe
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917942"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>Azure Cosmos DB は:SQL API の非同期 Java のサンプル

> [!div class="op_single_selector"]
> * [.NET のサンプル](sql-api-dotnet-samples.md)
> * [Java のサンプル](sql-api-java-samples.md)
> * [非同期 Java のサンプル](sql-api-async-java-samples.md)
> * [Node.js のサンプル](sql-api-nodejs-samples.md)
> * [Python のサンプル](sql-api-python-samples.md)
> * [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB リソースに対する CRUD 操作などの一般的な操作を実行する最新のサンプル アプリケーションは、[azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) GitHub レポジトリにあります。 この記事では、次の内容について説明します。

* 非同期 Java プロジェクトのサンプル ファイルのタスクへのリンク。 
* 関連する API リファレンス コンテンツへのリンク。

**前提条件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)ことができます。Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

このサンプル アプリケーションを実行するには、次のものが必要です。

* Java Development Kit 8
* Microsoft Azure Cosmos DB Java SDK

プロジェクトで使用するための最新の Microsoft Azure Cosmos DB Java SDK バイナリが必要な場合は、Maven を使用して取得できます。 [こちら](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)から最新バージョンを選択できます。 必要な依存関係は Maven によって自動的に追加されます。 または、pom.xml ファイルに一覧表示されている依存関係を直接ダウンロードして、ビルドのパスに追加することもできます。

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-cosmosdb</artifactId>
    <version>${LATEST}</version>
</dependency>
```

**サンプル アプリケーションの実行**

サンプル リポジトリを複製します。
```bash
$ git clone https://github.com/Azure/azure-cosmosdb-java.git

$ cd azure-cosmosdb-java
```

サンプルは、Eclipse を使用して実行するか、Maven を使用してコマンドラインから実行できます。

Eclipse から実行するには:
* Eclipse にメインの親プロジェクト pom.xml ファイルを読み込みます。azure-cosmosdb-examples が自動的に読み込まれます。
* サンプルを実行するには、有効な Azure Cosmos DB エンドポイントが必要です。 エンドポイントは次の場所から読み取られます。`src/test/java/com/microsoft/azure/cosmosdb/rx/examples/TestConfigurations.java`
* エンドポイントの資格情報は、Eclipse JUnit Run Config で VM 引数として渡すか、TestConfigurations.java 内に設定できます。
    ```bash
    -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```
* これで、サンプルを Eclipse で JUnit テストとして実行できます。

コマンド ラインから実行するには:
* サンプルは、Maven を使用して実行することもできます。
* Maven を実行して、Azure Cosmos DB エンドポイントの資格情報を渡します。
    ```bash
    mvn test -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```

   > [!NOTE]
   > 各サンプルは自己完結型であり、自身をセットアップし、自身をクリーンアップします。 サンプルでは [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) の複数の呼び出しを発行します。 これが行われるたびに、作成中のコレクションのパフォーマンス階層ごとに 1 時間の使用量に対するサブスクリプションが課金されます。 
   > 
   > 

## <a name="database-examples"></a>データベースのサンプル
[DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos データベースについて知るために、[データベース、コンテナー、アイテムの操作](databases-containers-items.md)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [データベースを作成する](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdatabase) |
| [既に存在するデータベースは作成できない](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [データベースの作成と読み取り](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdatabase) |
| [データベースの作成と削除](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedatabase) |
| [データベースの作成とクエリ](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydatabases) |

## <a name="collection-examples"></a>コレクションのサンプル
[CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos のコレクションについて知るために、[データベース、コンテナー、アイテムの操作](databases-containers-items.md)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [シングル パーティション コレクションの作成](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) |
| [カスタムのマルチパーティション コレクションの作成](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions) |
| [既に存在するデータベースは作成できない](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [コレクションの作成と読み取り](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readcollection) |
| [コレクションの作成と削除](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletecollection) |
| [コレクションの作成とクエリ](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querycollections) |

## <a name="document-examples"></a>ドキュメントのサンプル
[DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos のドキュメントについて知るために、[データベース、コンテナー、アイテムの操作](databases-containers-items.md)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [ドキュメントの作成](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdocument) |
| [プログラミング可能なドキュメント定義を使用したドキュメントの作成](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [ドキュメント](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resource.setid) |
| [ドキュメントの作成と合計 RU コストの検出](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resourceresponse.getrequestcharge) |
| [既に存在するドキュメントは作成できない](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [ドキュメントの作成と置き換え](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replacedocument) |
| [ドキュメントの作成とアップサート](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.upsertdocument) |
| [ドキュメントの作成と削除](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedocument) |
| [ドキュメントの作成と読み取り](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdocument) |

## <a name="indexing-examples"></a>インデックス作成のサンプル
[CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) ファイルは、次のタスクを実行する方法を示しています。  以下のサンプルを実行する前に Azure Cosmos DB におけるインデックス作成について知るために、[インデックス作成ポリシー](index-policy.md)、[インデックスの種類](index-types.md)、[インデックスのパス](index-paths.md)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [インデックスの作成とインデックス作成ポリシーの設定](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.indexingpolicy) |

インデックス作成の詳細については、「[Azure Cosmos DB インデックス作成ポリシー](index-policy.md)」をご覧ください。

## <a name="query-examples"></a>クエリのサンプル
[DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB の SQL クエリ リファレンスについて知るために、[SQL クエリの例](how-to-sql-query.md)に関する概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [単純なドキュメントのクエリの実行](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments) |
| [単純なドキュメントのクエリの実行と合計 RU コストの検出](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedresponse.getrequestcharge) |
| [単純なドキュメントのクエリの実行、1 ページの読み取り、および返された観測可能対象のサブスクライブ解除](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [単純なドキュメントのクエリの実行と結果のフィルター処理](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [クロス パーティション ドキュメントの order-by クエリの実行](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedoptions.setenablecrosspartitionquery) |

クエリの記述について詳しくは、[Azure Cosmos DB 内の SQL クエリ](how-to-sql-query.md)に関するページをご覧ください。

## <a name="offer-examples"></a>プランのサンプル
[OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [コレクションの作成とスループットの設定](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions.setofferthroughput) |
| [コレクションの読み取りと関連付けられているプランの検索](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.queryoffers) |
| [プランの置き換えによるコレクションのスループットの更新](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>ストアド プロシージャのサンプル
[StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB のサーバー側プログラミングについて知るために、「[ストアド プロシージャ、トリガー、およびユーザー定義関数](stored-procedures-triggers-udfs.md)」の概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [ストアド プロシージャの作成と実行](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.executestoredprocedure) |
| [引数を用いたストアド プロシージャの作成と実行](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [オブジェクト引数を用いたストアド プロシージャの作成と実行](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>一意キー
[UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に、Azure Cosmos DB の一意のキーに関する「[一意のキー制約](unique-keys.md)」の概念記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [一意キーを使用したコレクションの作成](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekey)<br>[UniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekeypolicy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection.setuniquekeypolicy) |
