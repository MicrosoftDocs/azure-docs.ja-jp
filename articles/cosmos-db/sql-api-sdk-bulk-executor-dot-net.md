---
title: Azure Cosmos DB は:Bulk Executor .NET API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB Bulk Executor .NET SDK の各バージョン間の変更など、Bulk Executor .NET API と SDK に関するあらゆる詳細を提供します。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 4f2d8b3246901f139695998224dfe036cccb9833
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855786"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET Bulk Executor ライブラリ:ダウンロード情報 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **説明**| Bulk Executor ライブラリを使うと、クライアント アプリケーションは、Azure Cosmos DB アカウント上で一括操作を実行できます。 Bulk Executor ライブラリは、BulkImport、BulkUpdate、および BulkDelete 名前空間を提供します。 BulkImport モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括して取り込むことができます。 BulkUpdate モジュールは、Azure Cosmos DB コンテナー内の既存のデータを、パッチとして一括更新できます。 BulkDelete モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括削除できます。|
|**SDK のダウンロード**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub の BulkExecutor ライブラリ**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**作業開始**|[Bulk Executor ライブラリ .NET SDK の概要](bulk-executor-dot-net.md)|
| **現在サポートされているフレームワーク**| Microsoft .NET Framework 4.5.2、4.6.1、.NET Standard 2.0 |

## <a name="release-notes"></a>リリース ノート

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* .NET Standard 2.0 をサポートするために、MongoBulkExecutor が含まれています。 この機能により、リリース 1.3.0 と同水準の機能を実現しつつ、ターゲット フレームワークとして .NET Standard 2.0 のサポートが追加されます。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* BulkExecutor ライブラリを .NET Core アプリケーションに対応させるため、サポートされるターゲット フレームワークの 1 つとして .NET Standard 2.0 を追加しました。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* SQL API アカウント向けに BulkDelete 操作のオーバーロードを追加し、パーティション キー、削除対象のドキュメント ID のタプルを受け取ることができるようにしました。
* SQL API アカウント向けに BulkDelete 操作のオーバーロードを追加し、パーティション キーの値を指定してそのパーティション キーが含まれる RequestOptions を受け取ることができるようにしました。また、削除対象のドキュメントを指定して入力クエリの中でフィルターとして使用することもできます。
* BulkExecutor により使用されるユーザー エージェントにフォーマットの問題が発生する原因となっていた問題を修正しました。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* BulkExecutor のインポート API と更新 API を改良し、ストレージが現在の容量を上回ったときでも例外をスローすることなく、Cosmos DB コンテナーのエラスティックなスケーリングに透過的に対応できるようにしました。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* DocumentDB .NET SDK の依存関係をバージョン 2.1.3 に引き上げました。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 固定されたコレクションに対するインポート中に BulkExecutor に JSRT エラーが発生する原因となっていた問題を修正しました。

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
