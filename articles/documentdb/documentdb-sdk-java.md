---
title: "Azure DocumentDB Java API、SDK、およびリソース | Microsoft Docs"
description: "リリース日、提供終了日、DocumentDB Java SDK の各バージョン間の変更など、Java API と SDK に関するあらゆる詳細を提供します。"
services: documentdb
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/16/2017
ms.author: khdang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 6e558d2b651b9c50bafa5814a18ad360f7beb2f0
ms.lasthandoff: 03/25/2017


---
# <a name="documentdb-java-sdk-release-notes-and-resources"></a>DocumentDB Java SDK: リリース ノートとリソース
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

<tr><td>**SDK のダウンロード**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**API ドキュメント**</td><td>[Java API リファレンス ドキュメント](http://azure.github.io/azure-documentdb-java/)</td></tr>

<tr><td>**SDK への協力**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**作業開始**</td><td>[Java SDK の開始](documentdb-java-get-started.md)</td></tr>

<tr><td>**Web アプリ チュートリアル**</td><td>[DocumentDB による Web アプリケーション開発](documentdb-java-application.md)</td></tr>

<tr><td>**現在サポートされているランタイム**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

### <a name="a-name11001100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb1100"></a><a name="1.10.0"/>[1.10.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.10.0)
* 2,500 RU/秒という低いパーティション分割コレクションと、100 RU/秒ずつ増加するスケールのサポートを有効にしました。
* 一部のクエリで NullRef 例外が発生する可能性があるネイティブ アセンブリのバグを修正しました。

### <a name="a-name196196httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb196"></a><a name="1.9.6"/>[1.9.6](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.6)
* ゲートウェイ モードでクエリに例外を引き起こす可能性のあるクエリ エンジン構成のバグが修正されました。
* コレクションの作成直後の要求に対して "所有者リソースが見つかりません" という例外を引き起こす可能性のある、セッション コンテナー内のいくつかのバグを修正しました。

### <a name="a-name195195httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb195"></a><a name="1.9.5"/>[1.9.5](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.5)
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。 [集計のサポート](documentdb-sql-query.md#Aggregates)に関するトピックを参照してください。
* Change Feed のサポートを追加しました。
* RequestOptions.setPopulateQuotaInfo を介したコレクション クォータ情報のサポートを追加しました。
* RequestOptions.setScriptLoggingEnabled を介したストアド プロシージャ スクリプトのログ記録のサポートを追加しました。
* スロットル エラーが発生した場合に DirectHttps モードのクエリがハングすることがあるバグを修正しました。
* セッションの整合性モードのバグを修正しました。
* 要求レートが高いときに HttpContext の NullReferenceException を引き起こす可能性のあるバグを修正しました。
* DirectHttps モードのパフォーマンスを改善しました。

### <a name="a-name194194httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb194"></a><a name="1.9.4"/>[1.9.4](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.4)
* ConnectionPolicy.setProxy() API による簡易クライアント インスタンス ベースのプロキシのサポートを追加しました。
* DocumentClient インスタンスを適切にシャットダウンするために、DocumentClient.close() API を追加しました。
* ゲートウェイではなく、ネイティブ アセンブリからクエリ プランを派生することで、直接接続モードでのクエリのパフォーマンスを改善しました。
* ユーザーが POJO で JsonIgnoreProperties を定義しなくて済むように、FAIL_ON_UNKNOWN_PROPERTIES = false を設定しました。
* SLF4J を使用するためにログ記録をリファクタリングしました。
* 整合性リーダーの他のいくつかのバグを修正しました。

### <a name="a-name193193httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb193"></a><a name="1.9.3"/>[1.9.3](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.3)
* 直接接続モードでの接続リークを防ぐために、接続管理のバグを修正しました。
* NullReferenece 例外がスローされる場合がある TOP クエリのバグを修正しました。
* 内部キャッシュのネットワーク呼び出しの数を減らすことでパフォーマンスを改善しました。
* トラブルシューティングを適切に行うために、状態コード、ActivityID、要求 URI を DocumentClientException に追加しました。

### <a name="a-name192192httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb192"></a><a name="1.9.2"/>[1.9.2](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.2)
* 安定性を確保するために接続管理の問題を修正しました。

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)
* BoundedStaleness 一貫性レベルのサポートを追加しました。
* パーティション分割コレクションの CRUD 操作のための直接接続のサポートを追加しました。
* SQL を使用したデータベース クエリのバグを修正しました。
* セッション キャッシュでセッション トークンが正しく設定されない場合があるというバグを修正しました。

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)
* クロス パーティションの並列クエリのサポートを追加しました。
* パーティション分割コレクションの TOP/ORDER BY クエリのサポートを追加しました。
* 強力な一貫性のサポートを追加しました。
* 直接接続を使用する際の名前ベースの要求のサポートを追加しました。
* すべての要求再試行で ActivityId の一貫性が維持されるように修正しました。
* 同じ名前のコレクションを再作成する際のセッション キャッシュに関連するバグを修正しました。
* ジオフェンシング空間クエリに対してコレクションのインデックス作成ポリシーを指定する際の Polygon および LineString データ型を追加しました。
* Java 1.8 の Java ドキュメントに関する問題を修正しました。

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
* 単一のパーティション コレクションをキャッシュし、パーティション キーの要求は余計にフェッチしないように、PartitionKeyDefinitionMap のバグを修正しました。
* 無効なパーティション キー値が指定された場合に再試行されないように、バグを修正しました。

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
* 複数リージョンのデータベース アカウントのサポートを追加しました。
* 最大再試行回数と最大再試行待機時間をカスタマイズするオプションと共に、調整された要求での自動再試行のサポートを追加しました。  RetryOptions と ConnectionPolicy.getRetryOptions() をご覧ください。
* IPartitionResolver に基づくカスタム パーティション分割コードを廃止しました。 大量のストレージとスループットを必要とする場合、パーティション分割コレクションをお使いください。

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
* スロットルのための再試行ポリシー サポートを追加しました。  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
* ドキュメントの有効期限 (TTL) サポートを追加しました。

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
* [パーティション分割コレクション](documentdb-partition-data.md)と[ユーザー定義のパフォーマンス レベル](documentdb-performance-levels.md)を実装しました。

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
* 他の SDK と一貫性を維持するため、リトル エンディアンのハッシュ値を生成する HashPartitionResolver のバグを修正しました。

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
* ハッシュおよび範囲パーティション リゾルバーを追加して、複数のパーティションにわたってシャーディング アプリケーションを支援します。

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
* Upsert を実装します。 Upsert 機能をサポートするために新しい upsertXXX メソッドが追加されました。
* ID ベースのルーティングを実装します。 パブリック API の変更なし、すべて内部の変更。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* 他の SDK とバージョン番号をそろえるため、このリリースはスキップされました

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
* 地理空間インデックスをサポートします
* すべてのリソースの id プロパティを検証します。 リソースの ID には ?、/、#、\, 文字を使えず、終わりの文字をスペースにできません。
* ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
* V2 インデックス作成ポリシーを実装する

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
* GA SDK

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

提供終了の SDK で DocumentDB に要求した場合、サービスにより却下されます。

> [!WARNING]
> バージョン **1.0.0** 以前のすべてのバージョンの Azure DocumentDB SDK for Java は **2016 年 2 月 29 日**で提供が終了します。
> 
> 

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.10.0](#1.10.0) |2017 年 3 月 11 日 |--- |
| [1.9.6](#1.9.6) |2017 年 2 月 21 日 |--- |
| [1.9.5](#1.9.5) |2017 年 1 月 31 日 |--- |
| [1.9.4](#1.9.4) |2016 年 11 月 24 日 |--- |
| [1.9.3](#1.9.3) |2016 年 10 月 30 日 |--- |
| [1.9.2](#1.9.2) |2016 年 10 月 28 日 |--- |
| [1.9.1](#1.9.1) |2016 年 10 月 26 日 |--- |
| [1.9.0](#1.9.0) |2016 年 10 月 3 日 |--- |
| [1.8.1](#1.8.1) |2016 年 6 月 30 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.1](#1.7.1) |2016 年 4 月 30 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 27 日 |--- |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1.5.1](#1.5.1) |2015 年 12 月 31 日 |--- |
| [1.5.0](#1.5.0) |2015 年 12 月 4 日 |--- |
| [1.4.0](#1.4.0) |2015 年 10 月 5 日 |--- |
| [1.3.0](#1.3.0) |2015 年 10 月 5 日 |--- |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |--- |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1.0.1](#1.0.1) |2015 年 5 月 12 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |--- |
| 0.9.5-prelease |2015 年 3 月 9 日 |2016 年 2 月 29 日 |
| 0.9.4-prelease |2015 年 2 月 17 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2015 年 1 月 13 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 12 月 10 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>FAQ
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>関連項目
DocumentDB に関する詳細は、 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。


