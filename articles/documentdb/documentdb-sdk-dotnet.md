---
title: "Azure DocumentDB .NET SDK およびリソース | Microsoft Docs"
description: "リリース日、提供終了日、DocumentDB .NET SDK の各バージョン間の変更など、.NET API と SDK に関するあらゆる詳細を提供します。"
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
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
ms.openlocfilehash: c953615ef370dc89ed09bb8a32dc2caf7a7bcc3b
ms.lasthandoff: 03/30/2017


---
# <a name="documentdb-net-sdk-download-and-release-notes"></a>DocumentDB .NET SDK: ダウンロードおよびリリース ノート
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

<tr><td>**SDK のダウンロード**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**API ドキュメント**</td><td>[.NET API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**サンプル**</td><td>[.NET コード サンプル](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**作業開始**</td><td>[DocumentDB .NET SDK の使用](documentdb-get-started.md)</td></tr>

<tr><td>**Web アプリ チュートリアル**</td><td>[DocumentDB による Web アプリケーション開発](documentdb-dotnet-application.md)</td></tr>

<tr><td>**現在サポートされているフレームワーク**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

### <a name="a-name11311131httpswwwnugetorgpackagesmicrosoftazuredocumentdb1131"></a><a name="1.13.1"/>[1.13.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.13.1)
* いくつかの非同期 API を ASP.NET のコンテキスト内で使用する場合にデッドロックの原因となった問題を修正しました。

### <a name="a-name11301130httpswwwnugetorgpackagesmicrosoftazuredocumentdb1130"></a><a name="1.13.0"/>[1.13.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.13.0)
* 特定の条件で自動フェールオーバーするために SDK の耐障害性を改善しました。

### <a name="a-name11221122httpswwwnugetorgpackagesmicrosoftazuredocumentdb1122"></a><a name="1.12.2"/>[1.12.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.2)
* WebException を時々発生させる問題 (リモート名を解決できなかった) を修正しました。
* ReadDocumentAsync API に新しいオーバーロードを追加することで、型指定されたドキュメントを直接読み取るサポートを追加しました。

### <a name="a-name11211121httpswwwnugetorgpackagesmicrosoftazuredocumentdb1121"></a><a name="1.12.1"/>[1.12.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.1)
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) の LINQ サポートを追加しました。
* イベント ハンドラーの使用によって発生した ConnectionPolicy オブジェクトのメモリ リークの問題を修正しました。
* ETag を使用した場合に UpsertAttachmentAsync が動作しなかった問題を修正しました。
* 文字列フィールドの並べ替えで、パーティションを横断する order-by クエリの継続が動作しなかった問題を修正しました。

### <a name="a-name11201120httpswwwnugetorgpackagesmicrosoftazuredocumentdb1120"></a><a name="1.12.0"/>[1.12.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.0)
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。 [集計のサポート](documentdb-sql-query.md#Aggregates)に関するトピックを参照してください。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。

### <a name="a-name11141114httpswwwnugetorgpackagesmicrosoftazuredocumentdb1114"></a><a name="1.11.4"/>[1.11.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.4)
* 32 ビット ホスト プロセスでクロス パーティション クエリの一部が失敗する問題を修正しました。
* ゲートウェイ モード中に失敗した要求のトークンでセッション コンテナーが更新されない問題を修正しました。
* プロジェクションでの UDF 呼び出しによるクエリが場合によっては失敗する問題を修正しました。
* クライアント側のパフォーマンスを修正し、要求の読み取りと書き込みのスループットを向上させました。

### <a name="a-name11131113httpswwwnugetorgpackagesmicrosoftazuredocumentdb1113"></a><a name="1.11.3"/>[1.11.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.3)
* 失敗した要求のトークンでセッション コンテナーが更新されない問題を修正しました。
* 32 ビット ホスト プロセスで動作する SDK のサポートが追加されました。 クロス パーティション クエリを使用する場合は、パフォーマンスを向上させるために 64 ビット ホスト処理をお勧めします。
* IN 式で大量のパーティション キー値を使用するクエリを含むシナリオのパフォーマンスを改善しました。
* PopulateQuotaInfo 要求オプションが設定されている場合、ドキュメント コレクションの読み取り要求の ResourceResponse に、リソース クォータのさまざまな統計情報が含まれるようになりました。

### <a name="a-name11111111httpswwwnugetorgpackagesmicrosoftazuredocumentdb1111"></a><a name="1.11.1"/>[1.11.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.1)
* 1.11.0 で導入された CreateDocumentCollectionIfNotExistsAsync API のパフォーマンスが少し改善されました。
* 高度な同時要求を伴うシナリオのために、SDK のパフォーマンスが改善されました。

### <a name="a-name11101110httpswwwnugetorgpackagesmicrosoftazuredocumentdb1110"></a><a name="1.11.0"/>[1.11.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.0)
* コレクションに含まれるドキュメントの [Change Feed](documentdb-change-feed.md) を処理する新しいクラスと方法をサポートしました。
* クロス パーティション クエリの継続と、クロス パーティション クエリの一部のパフォーマンスの向上をサポートしました。
* CreateDatabaseIfNotExistsAsync と CreateDocumentCollectionIfNotExistsAsync メソッドを追加しました。
* LINQ でシステム関数 IsDefined、IsNull、IsPrimitive がサポートされるようになりました。
* project.json ツールがあるプロジェクトの Nuget パッケージを使用する際の、Microsoft.Azure.Documents.ServiceInterop.dll と DocumentDB.Spatial.Sql.dll アセンブリのアプリケーションのビン フォルダーへの自動 Binplace 処理を修正しました。
* デバッグ シナリオで役に立つクライアント側の ETW トレースの出力をサポートしました。

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)
* パーティション分割コレクションへの直接接続のサポートを追加しました。
* "Bounded Staleness"一貫性レベルのパフォーマンスが向上しました。
* ジオフェンシング空間クエリに対してコレクションのインデックス作成ポリシーを指定する際の Polygon および LineString データ型を追加しました。
* 述語変換中の StringEnumConverter、IsoDateTimeConverter、および UnixDateTimeConverter に対する LINQ のサポートを追加しました。
* さまざまなバグを修正しました。

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)
* 次の NotFoundException の原因となった問題が修正されました。認証トークンは現在の時刻で無効です。 地理的に分散したアカウントの読み取り領域にクエリを実行すると、例外が発生する場合があります。
* ResourceResponse クラスで公開されている ResponseStream により、応答から基になるストリームへの直接アクセスが有効になります。

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)
* ResourceResponse、FeedResponse、StoredProcedureResponse、および MediaResponse クラスが変更され、テスト駆動型デプロイ (TDD) の手本にできるように、対応するパブリック インターフェイスが実装されました。
* カスタム オブジェクトの JsonSerializerSettings をデータのシリアル化に使用するときにパーティション キー ヘッダーの形式が正しくなくなる原因となっていた問題を修正しました。

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)
* 実行時間の長いクエリがエラーのために失敗した原因となった問題を修正しました。認証トークンは現在の時刻で無効です。
* クロス パーティションの TOP クエリおよび ORDER BY クエリから元の SqlParameterCollection を削除する問題が修正されました。

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)
* パーティション分割コレクションの並列クエリのサポートを追加しました。
* パーティション分割コレクションのクロス パーティションの ORDER BY および TOP クエリに対するサポートを追加しました。
* DocumentDB NuGet パッケージについての参照で、 DocumentDB プロジェクトを参照するときに必要なDocumentDB.Spatial.Sql.dll と Microsoft.Azure.Documents.ServiceInterop.dll への参照の不足を修正しました。
* LINQ でのユーザー定義関数の使用時に各種パラメーターを使用する機能を修正しました。 
* Upsert コールが書き込み専用ロケーションではなく、読み取り専用ロケーションにダイレクトされる、グローバルにレプリケートされたアカウントのバグを修正しました。
* IDocumentClient インターフェイスに、不足していた次のメソッドを追加しました。 
  * mediaStream とオプションをパラメーターとして受け取る UpsertAttachmentAsync メソッド
  * オプションをパラメーターとして受け取る CreateAttachmentAsync メソッド
  * querySpec をパラメーターとして受け取る CreateOfferQuery メソッド
* IDocumentClient インターフェースで公開されているパブリック クラスのシールを解除しました。

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
* 複数リージョンのデータベース アカウントのサポートを追加しました。
* 調整された要求での再試行のサポートを追加しました。  ユーザーは、ConnectionPolicy.RetryOptions プロパティを構成することで、再試行の回数と最大待機時間をカスタマイズできます。
* すべての DocumenClient プロパティおよびメソッドの署名を定義できる新しい IDocumentClient インターフェイスを追加しました。  また、この変更の一環として、IQueryable と IOrderedQueryable を作成する拡張メソッドを DocumentClient クラス自体のメソッドに変更しました。
* 特定の DocumentDB エンドポイント URI の ServicePoint.ConnectionLimit を設定する構成オプションを追加しました。  ConnectionPolicy.MaxConnectionLimit を使用して既定値 (50) を変更できます。
* IPartitionResolver とその実装を廃止しました。  IPartitionResolver のサポートは廃止されました。 大量のストレージとスループットを必要とする場合は、パーティション分割コレクションの使用をお勧めします。

### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
* RequestOptions をパラメーターとして受け取る Uri ベースの ExecuteStoredProcedureAsync メソッドにオーバーロードを追加しました。

### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
* ドキュメントの有効期限 (TTL) サポートを追加しました。

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
* .NET SDK を Azure クラウド サービス ソリューションの一部としてパッケージ化するための Nuget パッケージ化のバグを修正しました。

### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
* [パーティション分割コレクション](documentdb-partition-data.md)と[ユーザー定義のパフォーマンス レベル](documentdb-performance-levels.md)を実装しました。 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
* **[修正済み]** DocumentDB エンドポイントのクエリを実行すると、"System.Net.Http.HttpRequestException: コンテンツをストリームにコピーしている間にエラーが発生しました" というエラーがスローされます。

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
* ページング、条件付きの式、および範囲比較用の新しい演算子を使用できるように LINQ を拡張しました。
  * LINQ で SELECT TOP 動作を有効にする Take 演算子
  * 文字列の範囲の比較を有効にする CompareTo 演算子
  * 条件付き (?) および合体演算子 (??)
* **[修正済み]** linq クエリでモデル プロジェクションを Where-In と組み合わせると ArgumentOutOfRangeException が発生します。  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
* **[修正済み]** Select が最後の式ではない場合、LINQ プロバイダーはプロジェクションがないものと想定し、SELECT * を不適切に生成しました。  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
* 実装された Upsert、追加された UpsertXXXAsync メソッド
* すべての要求のパフォーマンス改善
* LINQ プロバイダーによる文字列の条件、結合、CompareTo メソッドのサポート
* **[修正済み]** LINQ プロバイダー --> List に Contains メソッドを実行し、IEnumerable や Arraymethod の場合と同じ SQL を生成
* **[修正済み]** BackoffRetryUtility は、再試行時に新規作成する代わりに、同じ HttpRequestMessage をもう一度使用します
* **[廃止]** UriFactory.CreateCollection --> 今後は UriFactory.CreateDocumentCollection を使用する必要があります

### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
* **[修正済み]** nl-NL など、非英語文化情報の使用時のローカリゼーション問題 

### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
* ID ベースのルーティング
  * ID ベースのリソース リンクの構築に役立つ新しい UriFactory ヘルパー
  * URI に取り入れる DocumentClient の新しいオーバーロード
* 地理空間の LINQ で IsValid() と IsValidDetailed() を追加
* LINQ プロバイダー サポートの機能強化
  * **数値演算** - Abs、Acos、Asin、Atan、Ceiling、Cos、Exp、Floor、Log、Log10、Pow、Round、Sign、Sin、Sqrt、Tan、Truncate
  * **文字列** - Concat、Contains、EndsWith、IndexOf、Count、ToLower、TrimStart、Replace、Reverse、TrimEnd、StartsWith、SubString、ToUpper
  * **配列** - Concat、Contains、Count
  * **IN** 演算子

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
* インデックス作成ポリシーを変更するためのサポートを追加
  * DocumentClient の新しい ReplaceDocumentCollectionAsync メソッド
  * インデックス ポリシー変更の進捗状況 (%) を追跡するための ResourceResponse<T> の新しい IndexTransformationProgress プロパティ
  * DocumentCollection.IndexingPolicy は変化可能になりました
* 空間インデックス作成とクエリのサポートを追加
  * 点や多角形など、空間型のシリアル化/逆シリアル化するための新しい Microsoft.Azure.Documents.Spatial 名前空間
  * DocumentDB に格納されている GeoJSON データにインデックスを作成するための新しい SpatialIndex クラス
* **[修正済み]** : 間違った SQL クエリが linq 式から生成される [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
* Newtonsoft.Json v5.0.7 の依存関係 
* Order By 対応になりました
  
  * LINQ プロバイダーによる OrderBy() または OrderByDescending() のサポート
  * Order By をサポートするための IndexingPolicy 
    
    **NB: 考えられる重大な変更** 
    
    カスタム インデックス作成ポリシーを使用してコレクションをプロビジョニングする既存のコードがある場合、新しい IndexingPolicy クラスをサポートするために既存のコードを更新する必要があります。 カスタム インデックス作成ポリシーがない場合は、この変更による影響はありません。

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
* 新しい HashPartitionResolver クラス、RangePartitionResolver クラス、IPartitionResolver を使用し、データをパーティショニング
* DataContract シリアル化
* LINQ プロバイダーの GUID サポート
* LINQ の UDF サポート

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
* GA SDK

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了の SDK で DocumentDB に要求した場合、サービスにより却下されます。

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.13.1](#1.13.1) |2017 年 3 月 29 日 |--- |
| [1.13.0](#1.13.0) |2017 年 3 月 24 日 |--- |
| [1.12.2](#1.12.2) |2017 年 3 月 20 日 |--- |
| [1.12.1](#1.12.1) |2017 年 3 月 14 日 |--- |
| [1.12.0](#1.12.0) |2017 年 2 月 15 日 |--- |
| [1.11.4](#1.11.4) |2017 年 2 月 6 日 |--- |
| [1.11.3](#1.11.3) |2017 年 1 月 26 日 |--- |
| [1.11.1](#1.11.1) |2016 年 12 月 21 日 |--- |
| [1.11.0](#1.11.0) |2016 年 12 月 08 日 |--- |
| [1.10.0](#1.10.0) |2016 年 9 月 27 日 |--- |
| [1.9.5](#1.9.5) |2016 年 9 月 1 日 |--- |
| [1.9.4](#1.9.4) |2016 年 8 月 24 日 |--- |
| [1.9.3](#1.9.3) |2016 年 8 月 15 日 |--- |
| [1.9.2](#1.9.2) |2016 年 7 月 23 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.1](#1.7.1) |2016 年 5 月 6 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |--- |
| [1.6.3](#1.6.3) |2016 年 4 月 8 日 |--- |
| [1.6.2](#1.6.2) |2016 年 3 月 29 日 |--- |
| [1.5.3](#1.5.3) |2016 年 2 月 19 日 |--- |
| [1.5.2](#1.5.2) |2015 年 12 月 14 日 |--- |
| [1.5.1](#1.5.1) |2015 年 11 月 23 日 |--- |
| [1.5.0](#1.5.0) |2015 年 10 月 5 日 |--- |
| [1.4.1](#1.4.1) |2015 年 8 月 25 日 |--- |
| [1.4.0](#1.4.0) |2015 年 8 月 13 日 |--- |
| [1.3.0](#1.3.0) |2015 年 8 月 5 日 |--- |
| [1.2.0](#1.2.0) |2015 年 7 月 6 日 |--- |
| [1.1.0](#1.1.0) |2015 年 4 月 30 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>関連項目
DocumentDB に関する詳細は、 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。 


