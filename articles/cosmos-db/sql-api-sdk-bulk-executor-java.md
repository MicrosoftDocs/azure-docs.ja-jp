---
title: Azure Cosmos DB:Bulk Executor Java API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB Bulk Executor Java SDK の各バージョン間の変更など、Bulk Executor Java API と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 60c2b3e194fc7703ad1c3f1d3138f9a2c6301f33
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585051"
---
# <a name="java-bulk-executor-library-download-information"></a>Java Bulk Executor ライブラリ:ダウンロード情報

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark コネクタ](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**説明**|Bulk Executor ライブラリを使うと、クライアント アプリケーションは、Azure Cosmos DB アカウントで一括操作を実行できます。 Bulk Executor ライブラリは、BulkImport および BulkUpdate 名前空間を提供します。 BulkImport モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括して取り込むことができます。 BulkUpdate モジュールでは、Azure Cosmos コンテナー内の既存のデータをパッチとして一括更新できます。|
|**SDK のダウンロード**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub の Bulk Executor ライブラリ**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API ドキュメント**| [Java API リファレンス ドキュメント](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**開始するには**|[Bulk Executor ライブラリ Java SDK の概要](bulk-executor-java.md)|
|**サポートされている最小ランタイム**|[Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>リリース ノート

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* JSON から入れ子になったパーティション キー値を正しく抽出するように DocumentAnalyzer.java を修正します。

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* 特定のエラーに対して再試行する機能を BulkDelete 操作に追加し、再試行可能なエラーの一覧をユーザーに返します。

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Cosmos SDK バージョン 2.4.7 の更新プログラム。

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* 'id' とパーティション キーの値の後に配置されているすべての修正済みドキュメント プロパティが更新された項目の一覧に追加されるように、'mergeAll' が 'id' とパーティション キーの値で続行するように修正します。

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* コンカレンシーの開始次数を 1 に更新し、minibatch のデバッグ ログを追加します。


