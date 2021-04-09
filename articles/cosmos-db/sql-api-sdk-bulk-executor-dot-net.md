---
title: Azure Cosmos DB:Bulk Executor .NET API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB BulkExecutor .NET SDK の各バージョン間の変更など、BulkExecutor .NET API と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 91fcfc614fed95c40aa3fed4368d17399255e8b5
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423887"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET Bulk Executor ライブラリ:ダウンロード情報 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **説明**| .NET Bulk Executor ライブラリを使用すると、クライアント アプリケーションでは、Azure Cosmos DB アカウント上で一括操作を実行できます。 このライブラリは、BulkImport、BulkUpdate、および BulkDelete の名前空間を提供します。 BulkImport モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括して取り込むことができます。 BulkUpdate モジュールでは、Azure Cosmos コンテナー内の既存のデータをパッチとして一括更新できます。 BulkDelete モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括削除できます。|
|**SDK のダウンロード**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub の Bulk Executor ライブラリ**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**開始するには**|[Bulk Executor ライブラリ .NET SDK の概要](bulk-executor-dot-net.md)|
| **現在サポートされているフレームワーク**| Microsoft .NET Framework 4.5.2、4.6.1、.NET Standard 2.0 |

> [!NOTE]
> 一括実行プログラムを使用している場合は、SDK に一括実行プログラムが組み込まれている [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md) の最新バージョン 3.x を参照してください。 

## <a name="release-notes"></a>リリース ノート

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* BulkDelete の応答で TotalElapsedTime を修正し、再試行を含む合計時間を正しく測定できるようになりました。

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-preview

* SDK 依存関係を >= 2.5.1 に変更しました

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* グラフの Bulk Executor で頂点とエッジに ttl を受け付けるためのサポートが追加されました。

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* ゲートウェイ モードで実行されているとき Azure Cosmos DB のエラスティック スケーリング中に例外が発生する問題を修正しました。 この修正により 1.4.1 リリースと機能的に同等になります。

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* SQL API アカウントに、パーティション キーとドキュメント ID の組を削除できる BulkDelete のサポートが追加されました。 この変更により 1.4.0 リリースと機能的に同等になります。

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* .NET Standard 2.0 をサポートするために、MongoBulkExecutor が含まれています。 この機能により、リリース 1.3.0 と同水準の機能を実現しつつ、ターゲット フレームワークとして .NET Standard 2.0 のサポートが追加されます。

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* Bulk Executor ライブラリを .NET Core アプリケーションに対応させるため、サポートされているターゲット フレームワークの 1 つとして .NET Standard 2.0 が追加されました。

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* エスケープされた引用符がある値が入力パラメーターとして渡された場合の BulkDeleteAsync の問題を修正しました。

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* 埋め込みが追加されることで、場合によってはドキュメントの最大サイズの制限を超えて、ドキュメントのサイズが予想外に増加する MongoBulkExecutor の問題を修正しました。

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* コレクションに入れ子になったパーティション キー パスがある場合の BulkDeleteAsync に関する問題を修正しました。

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor は IDisposable を実装するようになりました。これは、使用後に破棄されることが想定されています。

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* SDK バージョンのロックを解除しました。 今後は、パッケージが依存する SDK バージョンは 2.5.1 以上となります。

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* 数値を含む POCO オブジェクトのリストを使用して BulkImport を呼び出すときの識別子の処理を修正しました。

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* BulkDelete の応答で TotalElapsedTime を修正し、再試行を含む合計時間を正しく測定できるようになりました。

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* 特定のシナリオで CPU 使用率が高い問題を修正しました。
* トレースで TraceSource が使用されるようになりました。 ユーザーは、`BulkExecutorTrace` ソースのリスナーを定義できます。
* サイズが 2MB に近いドキュメントの送信時にロックがまれに発生する問題を修正しました。

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* 最新バージョンの Azure Cosmos DB .NET SDK (2.4.0) を使用するように Bulk Executor が更新されました。

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* グラフの Bulk Executor で頂点とエッジに ttl を受け付けるためのサポートが追加されました。

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* ゲートウェイ モードで実行されているとき Azure Cosmos DB のエラスティック スケーリング中に例外が発生する問題を修正しました。

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* SQL API アカウントに、パーティション キーとドキュメント ID の組を削除できる BulkDelete のサポートが追加されました。

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Bulk Executor で使用されるユーザー エージェントでのフォーマットの問題の原因となった問題を修正しました。

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Bulk Executor のインポート API と更新 API を改良し、ストレージが現在の容量を上回ったときでも例外をスローすることなく、Cosmos コンテナーのエラスティックなスケーリングに透過的に対応できるようにしました。

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* DocumentDB .NET SDK の依存関係をバージョン 2.1.3 に引き上げました。

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* 固定されたコレクションに対するインポート中に Bulk Executor で JSRT エラーがスローされる原因となった問題を修正しました。

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Azure Cosmos DB SQL API アカウントの BulkDelete 操作のサポートが追加されました。
* Azure Cosmos DB の MongoDB 用 API アカウントの BulkImport 操作のサポートが追加されました。
* DocumentDB .NET SDK の依存関係がバージョン 2.0.0 に引き上げられました。 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Azure Cosmos DB Gremlin API アカウントの BulkImport 操作のサポートが追加されました。

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Azure Cosmos DB SQL API アカウントの BulkImport 操作の小さなバグ修正。

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Azure Cosmos DB SQL API アカウントの BulkImport 操作および BulkUpdate 操作のサポートが追加されました。

## <a name="next-steps"></a>次のステップ

Bulk Executor Java ライブラリの詳細については、次の記事を参照してください。

[Java Bulk Executor ライブラリの SDK とリリースに関する情報](sql-api-sdk-bulk-executor-java.md)