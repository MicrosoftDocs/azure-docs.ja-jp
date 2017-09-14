---
title: "Azure Cosmos DB .NET Core API、SDK、およびリソース | Microsoft Docs"
description: "リリース日、提供終了日、Azure Cosmos DB .NET Core SDK の各バージョン間の変更など、.NET Core API と SDK に関するあらゆる詳細を提供します。"
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/11/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a7ce4d771e9c655687f72f4b46c7405cf64aeb74
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---
# <a name="azure-cosmos-db-net-core-sdk-release-notes-and-resources"></a>Azure Cosmos DB .NET Core SDK: リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Change Feed](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST ()](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK のダウンロード**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API ドキュメント**</td><td>[.NET API リファレンス ドキュメント](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**サンプル**</td><td>[.NET コード サンプル](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**概要**</td><td>[Azure Cosmos DB .NET Core SDK を開始する](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Web アプリ チュートリアル**</td><td>[Azure Cosmos DB を使用した Web アプリケーションの開発](documentdb-dotnet-application.md)</td></tr>

<tr><td>**現在サポートされているフレームワーク**</td><td>[.NET Standard 1.6 および .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

Azure Cosmos DB .NET Core SDK には最新バージョンの [Azure Cosmos DB .NET SDK](documentdb-sdk-dotnet.md) と同等の機能が備わっています。

> [!NOTE] 
> Azure Cosmos DB .NET Core SDK は、現在のところ、ユニバーサル Windows プラットフォーム (UWP) アプリとの互換性はありません。 UWP アプリをサポートする .NET Core SDK については、[askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) に電子メールでお問い合わせください。

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* クエリの結果をパーティション キーの特定の範囲の値にスコープするための FeedOption として PartitionKeyRangeIdresults のサポートが追加されました。 
* 指定時刻以後の変更の検索を開始するための ChangeFeedOption として StartTime のサポートが追加されました。 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   スタック オーバーフローの例外を引き起こす可能性のある JsonSerializable クラスの問題を修正しました。

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) のインスタンス化時にカスタム JsonSerializerSettings を指定するためのサポートが追加されました。

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   ターゲット フレームワークの 1 つとして .NET Standard 1.5 をサポートします。

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   SSE4 命令をサポートせず、Azure Cosmos DB クエリの実行時に SEHException をスローする x64 マシンに影響を与える問題を修正しました。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   ConsistentPrefix と呼ばれている新しい一貫性レベルに対応するようになりました。
*   個別のパーティションに対するメトリックのクエリに対応するようになりました。
*   クエリの継続トークンのサイズ制限に対応するようになりました。
*   失敗した要求の詳細なトレースに対応するようになりました。
*   SDK でのパフォーマンスが向上しています。

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* 集計クエリの FeedOptions 内に指定された PartitionKey 値が無視される問題を修正しました。
* 中間クロス パーティション Order By クエリの実行時のパーティション管理の透過的な処理の問題を修正しました。

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* いくつかの非同期 API を ASP.NET のコンテキスト内で使用する場合にデッドロックの原因となった問題を修正しました。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 特定の条件で自動フェールオーバーするために SDK の耐障害性を改善しました。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* WebException を時々発生させる問題 (リモート名を解決できなかった) を修正しました。
* ReadDocumentAsync API に新しいオーバーロードを追加することで、型指定されたドキュメントを直接読み取るサポートを追加しました。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) の LINQ サポートを追加しました。
* イベント ハンドラーの使用によって発生した ConnectionPolicy オブジェクトのメモリ リークの問題を修正しました。
* ETag を使用した場合に UpsertAttachmentAsync が動作しなかった問題を修正しました。
* 文字列フィールドの並べ替えで、パーティションを横断する order-by クエリの継続が動作しなかった問題を修正しました。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。 [集計のサポート](documentdb-sql-query.md#Aggregates)に関するトピックを参照してください。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK を使用すると、Windows、Mac、Linux で実行できるクロスプラットフォーム対応の高速な [ASP.NET Core](https://www.asp.net/core) アプリと [.NET Core](https://www.microsoft.com/net/core#windows) アプリを構築できるようになります。 Azure Cosmos DB .NET Core SDK の最新リリースは、[Xamarin](https://www.xamarin.com) との完全な互換性を持ち、iOS、Android、Mono (Linux) を対象とするアプリケーションの構築に使用されます。  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Azure Cosmos DB .NET Core Preview SDK を使用すると、Windows、Mac、Linux で実行できるクロスプラットフォーム対応の高速な [ASP.NET Core](https://www.asp.net/core) アプリと [.NET Core](https://www.microsoft.com/net/core#windows) アプリを構築できるようになります。

Azure Cosmos DB .NET Core Preview SDK には最新バージョンの [Azure Cosmos DB .NET SDK](documentdb-sdk-dotnet.md) と同等の機能が備わっており、次の機能がサポートされます。
* すべての[接続モード](performance-tips.md#networking): ゲートウェイ モード、ダイレクト TCP、ダイレクト HTTP。 
* すべての[一貫性レベル](consistency-levels.md): 強固、セッション、有界整合性制約、最終的。
* [パーティション分割コレクション](partition-data.md)。 
* [複数リージョンのデータベース アカウントと geo レプリケーション](distribute-data-globally.md)。

この SDK に関する質問がある場合は、[StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) に投稿するか、[github リポジトリ](https://github.com/Azure/azure-documentdb-dotnet/issues) に問題を報告してください。 

## <a name="release--retirement-dates"></a>リリース日と提供終了日

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.5.0](#1.5.0) |2017 年 8 月 10 日 |--- | 
| [1.4.1](#1.4.1) |2017 年 8 月 7 日 |--- |
| [1.4.0](#1.4.0) |2017 年 8 月 2 日 |--- |
| [1.3.2](#1.3.2) |2017 年 6 月 12 日 |--- |
| [1.3.1](#1.3.1) |2017 年 5 月 23 日 |--- |
| [1.3.0](#1.3.0) |2017 年 5 月 10 日 |--- |
| [1.2.2](#1.2.2) |2017 年 4 月 19 日 |--- |
| [1.2.1](#1.2.1) |2017 年 3 月 29 日 |--- |
| [1.2.0](#1.2.0) |2017 年 3 月 25 日 |--- |
| [1.1.2](#1.1.2) |2017 年 3 月 20 日 |--- |
| [1.1.1](#1.1.1) |2017 年 3 月 14 日 |--- |
| [1.1.0](#1.1.0) |2017 年 2 月 16 日 |--- |
| [1.0.0](#1.0.0) |2016 年 12 月 21 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2016 年 11 月 15 日 |2016 年 12 月 31 日 |

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。 


