---
title: "Azure DocumentDB .NET Core API、SDK、およびリソース | Microsoft Docs"
description: "リリース日、提供終了日、DocumentDB .NET Core SDK の各バージョン間の変更など、.NET Core API と SDK に関するあらゆる詳細を提供します。"
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/29/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 24b575a3c32c9df963f889954abd152ada4f3102
ms.lasthandoff: 03/30/2017


---
# <a name="documentdb-net-core-sdk-release-notes-and-resources"></a>DocumentDB .NET Core SDK: リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST ()](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK のダウンロード**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API ドキュメント**</td><td>[.NET API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**サンプル**</td><td>[.NET コード サンプル](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**作業開始**</td><td>[DocumentDB .NET Core SDK の使用](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Web アプリ チュートリアル**</td><td>[DocumentDB による Web アプリケーション開発](documentdb-dotnet-application.md)</td></tr>

<tr><td>**現在サポートされているフレームワーク**</td><td>[.NET Standard 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

DocumentDB .NET Core SDK には最新バージョンの [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) と同等の機能が備わっています。

> [!NOTE] 
> DocumentDB .NET Core SDK は、現在のところ、ユニバーサル Windows プラットフォーム (UWP) アプリとの互換性はありません。 UWP アプリをサポートする .NET Core SDK については、[askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) に電子メールでお問い合わせください。

### <a name="a-name121121httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore121"></a><a name="1.2.1"/>[1.2.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.2.1)

* いくつかの非同期 API を ASP.NET のコンテキスト内で使用する場合にデッドロックの原因となった問題を修正しました。

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.2.0)

* 特定の条件で自動フェールオーバーするために SDK の耐障害性を改善しました。

### <a name="a-name112112httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore112"></a><a name="1.1.2"/>[1.1.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.1.2)

* WebException を時々発生させる問題 (リモート名を解決できなかった) を修正しました。
* ReadDocumentAsync API に新しいオーバーロードを追加することで、型指定されたドキュメントを直接読み取るサポートを追加しました。

### <a name="a-name111111httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore111"></a><a name="1.1.1"/>[1.1.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.1.1)

* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) の LINQ サポートを追加しました。
* イベント ハンドラーの使用によって発生した ConnectionPolicy オブジェクトのメモリ リークの問題を修正しました。
* ETag を使用した場合に UpsertAttachmentAsync が動作しなかった問題を修正しました。
* 文字列フィールドの並べ替えで、パーティションを横断する order-by クエリの継続が動作しなかった問題を修正しました。

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.1.0)

* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。 [集計のサポート](documentdb-sql-query.md#Aggregates)に関するトピックを参照してください。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.0.0)

DocumentDB .NET Core SDK を使用すると、Windows、Mac、Linux で実行できるクロスプラットフォーム対応の高速な [ASP.NET Core](https://www.asp.net/core) アプリと [.NET Core](https://www.microsoft.com/net/core#windows) アプリを構築できるようになります。 DocumentDB .NET Core SDK の最新リリースは、[Xamarin](https://www.xamarin.com) との完全な互換性を持ち、iOS、Android、Mono (Linux) を対象とするアプリケーションの構築に使用されます。  

### <a name="a-name010-preview010-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentdbcore010-preview"></a><a name="0.1.0-preview"/>[0.1.0-preview](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/0.1.0-preview)

DocumentDB .NET Core Preview SDK を使用すると、Windows、Mac、Linux で実行できるクロスプラットフォーム対応の高速な [ASP.NET Core](https://www.asp.net/core) アプリと [.NET Core](https://www.microsoft.com/net/core#windows) アプリを構築できるようになります。

DocumentDB .NET Core Preview SDK には最新バージョンの [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) と同等の機能が備わっており、次の機能がサポートされます。
* すべての[接続モード](documentdb-performance-tips.md#networking): ゲートウェイ モード、ダイレクト TCP、ダイレクト HTTP。 
* すべての[一貫性レベル](documentdb-consistency-levels.md): 強固、セッション、有界整合性制約、最終的。
* [パーティション分割コレクション](documentdb-partition-data.md)。 
* [複数リージョンのデータベース アカウントと geo レプリケーション](documentdb-distribute-data-globally.md)。

この SDK に関する質問がある場合は、[StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) に投稿するか、[github リポジトリ](https://github.com/Azure/azure-documentdb-dotnet/issues) に問題を報告してください。 

## <a name="release--retirement-dates"></a>リリース日と提供終了日

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.2.1](#1.2.1) |2017 年 3 月 29 日 |--- |
| [1.2.0](#1.2.0) |2017 年 3 月 25 日 |--- |
| [1.1.2](#1.1.2) |2017 年 3 月 20 日 |--- |
| [1.1.1](#1.1.1) |2017 年 3 月 14 日 |--- |
| [1.1.0](#1.1.0) |2017 年 2 月 16 日 |--- |
| [1.0.0](#1.0.0) |2016 年 12 月 21 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2016 年 11 月 15 日 |2016 年 12 月 31 日 |

## <a name="see-also"></a>関連項目
DocumentDB に関する詳細は、 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。 


