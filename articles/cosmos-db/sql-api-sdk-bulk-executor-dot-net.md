---
title: Azure Cosmos DB:Bulk Executor .NET API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB BulkExecutor .NET SDK の各バージョン間の変更など、BulkExecutor .NET API と SDK に関するあらゆる詳細を提供します。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: d24481c1364b41f38e4ca7b8f7d726ebc22a1117
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624638"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET Bulk Executor ライブラリ:ダウンロード情報 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **説明**| .Net Bulk Executor ライブラリを使用すると、クライアント アプリケーションは、Azure Cosmos DB アカウント上で一括操作を実行できます。 このライブラリは、BulkImport、BulkUpdate、および BulkDelete の名前空間を提供します。 BulkImport モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括して取り込むことができます。 BulkUpdate モジュールでは、Azure Cosmos コンテナー内の既存のデータをパッチとして一括更新できます。 BulkDelete モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括削除できます。|
|**SDK のダウンロード**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub の Bulk Executor ライブラリ**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**作業開始**|[Bulk Executor ライブラリ .NET SDK の概要](bulk-executor-dot-net.md)|
| **現在サポートされているフレームワーク**| Microsoft .NET Framework 4.5.2、4.6.1、.NET Standard 2.0 |

## <a name="release-notes"></a>リリース ノート

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* グラフの Bulk Executor で頂点とエッジに ttl を受け付けるためのサポートが追加されました。

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* ゲートウェイ モードで実行されているとき Azure Cosmos DB のエラスティック スケーリング中に例外が発生する問題を修正しました。 この修正により 1.4.1 リリースと機能的に同等になります。

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* SQL API アカウントで削除対象のパーティション キーとドキュメント ID のタプルを受け付けるための BulkDelete のサポートが追加されました。 この変更により 1.4.0 リリースと機能的に同等になります。

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* .NET Standard 2.0 をサポートするために、MongoBulkExecutor が含まれています。 この機能により、リリース 1.3.0 と同水準の機能を実現しつつ、ターゲット フレームワークとして .NET Standard 2.0 のサポートが追加されます。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Bulk Executor ライブラリを .NET Core アプリケーションに対応させるため、サポートされているターゲット フレームワークの 1 つとして .NET Standard 2.0 が追加されました。

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* 最新バージョンの Azure Cosmos DB .NET SDK (2.4.0) を使用するように Bulk Executor が更新されました。

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* グラフの Bulk Executor で頂点とエッジに ttl を受け付けるためのサポートが追加されました。

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* ゲートウェイ モードで実行されているとき Azure Cosmos DB のエラスティック スケーリング中に例外が発生する問題を修正しました。

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* SQL API アカウントで削除対象のパーティション キーとドキュメント ID のタプルを受け付けるための BulkDelete のサポートが追加されました。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Bulk Executor で使用されるユーザー エージェントでのフォーマットの問題の原因となった問題を修正しました。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Bulk Executor のインポート API と更新 API を改良し、ストレージが現在の容量を上回ったときでも例外をスローすることなく、Cosmos コンテナーのエラスティックなスケーリングに透過的に対応できるようにしました。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* DocumentDB .NET SDK の依存関係をバージョン 2.1.3 に引き上げました。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 固定されたコレクションに対するインポート中に Bulk Executor で JSRT エラーがスローされる原因となった問題を修正しました。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Azure Cosmos DB SQL API アカウントの BulkDelete 操作のサポートが追加されました。
* Azure Cosmos DB の MongoDB 用 API アカウントの BulkImport 操作のサポートが追加されました。
* DocumentDB .NET SDK の依存関係がバージョン 2.0.0 に引き上げられました。 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Azure Cosmos DB Gremlin API アカウントの BulkImport 操作のサポートが追加されました。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Azure Cosmos DB SQL API アカウントの BulkImport 操作の小さなバグ修正。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Azure Cosmos DB SQL API アカウントの BulkImport 操作および BulkUpdate 操作のサポートが追加されました。

## <a name="next-steps"></a>次の手順

Bulk Executor Java ライブラリの詳細については、次の記事を参照してください。

[Java Bulk Executor ライブラリの SDK とリリースに関する情報](sql-api-sdk-bulk-executor-java.md)
