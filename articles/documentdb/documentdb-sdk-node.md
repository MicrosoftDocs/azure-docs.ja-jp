---
title: "Azure DocumentDB Node.js API、SDK、およびリソース | Microsoft Docs"
description: "リリース日、提供終了日、DocumentDB Node.js SDK の各バージョン間の変更など、Node.js API と SDK に関するあらゆる詳細を提供します。"
services: documentdb
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/16/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: dd6e6184dd755ea356cae1c4d50a2b7ba39da9fb
ms.lasthandoff: 03/17/2017


---
# <a name="documentdb-nodejs-sdk-release-notes-and-resources"></a>DocumentDB Node.js SDK: リリース ノートとリソース
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

<tr><td>**SDK のダウンロード**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**API ドキュメント**</td><td>[Node.js の API リファレンス ドキュメント](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**SDK のインストール手順**</td><td>[インストール手順](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**SDK への協力**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**サンプル**</td><td>[Node.js コード サンプル](documentdb-nodejs-samples.md)</td></tr>

<tr><td>**Notification Hubs の使用**</td><td>[Node.js SDK の開始](documentdb-nodejs-get-started.md)</td></tr>

<tr><td>**Web アプリ チュートリアル**</td><td>[DocumentDB を使用した Node.js Web アプリケーションの作成](documentdb-nodejs-application.md)</td></tr>

<tr><td>**現在サポートされているプラットフォーム**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

### <a name="1.11.0"/>1.11.0</a>
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。
* クロス パーティション クエリの並列処理の次数を制御するオプションを追加しました。
* DocumentDB エミュレーターに対しての実行時に、SSL 検証を無効にするためのオプションを追加しました。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。
* 単一パーティション コレクションに関する継続トークンのバグを修正しました (github #107)。
* 0 を単一パラメーター として処理する際の executeStoredProcedure バグを修正しました (github #155)。

### <a name="1.10.2"/>1.10.2</a>
* SDK バージョンを含めるようにユーザー エージェント ヘッダーを修正しました。
* コードの軽微なクリーンアップ。

### <a name="1.10.1"/>1.10.1</a>
* SDK を使用して emulator(hostname=localhost) を対象とするときに SSL 検証が無効になります。
* ストアド プロシージャの実行時にスクリプトのログ記録を有効することができるようになりました。

### <a name="1.10.0"/>1.10.0</a>
* クロス パーティションの並列クエリのサポートを追加しました。
* パーティション分割コレクションの TOP/ORDER BY クエリのサポートを追加しました。

### <a name="1.9.0"/>1.9.0</a>
* スロットルされた要求のための再試行ポリシー サポートを追加しました  (スロットルされた要求は、要求レートが大きすぎるという例外をエラー コード 429 で受信します)。既定では、DocumentDB は、エラー コード 429 が発生した場合に、応答ヘッダーの retryAfter 回数に従って要求ごとに 9 回再試行します。 再試行の間にサーバーによって返される retryAfter 回数を無視する場合、固定の再試行間隔の回数を、ConnectionPolicy オブジェクトの RetryOptions プロパティの一部としてここで設定できます。 DocumentDB では、(再試行の回数に関係なく) スロットルされる要求ごとに最大 30 秒待機できるようになり、エラー コード 429 と共に応答を返すようになりました。 この回数は、ConnectionPolicy オブジェクトの RetryOptions プロパティでオーバーライドすることもできます。
* DocumentDB は、スロットルの再試行の回数と再試行の間の要求の累積待機時間を表すために、すべての要求の応答ヘッダーとして x-ms-throttle-retry-count と x-ms-throttle-retry-wait-time-ms を返すようになりました。
* ConnectionPolicy クラスの RetryOptions プロパティを公開する、RetryOptions クラスが追加されました。これは、一部の既定の再試行オプションをオーバーライドするために使用できます。

### <a name="1.8.0"/>1.8.0</a>
* 複数リージョンのデータベース アカウントのサポートを追加しました。

### <a name="1.7.0"/>1.7.0</a>
* ドキュメントの有効期限 (TTL) 機能のサポートを追加しました。

### <a name="1.6.0"/>1.6.0</a>
* [パーティション分割コレクション](documentdb-partition-data.md)と[ユーザー定義のパフォーマンス レベル](documentdb-performance-levels.md)を実装しました。

### <a name="1.5.6"/>1.5.6</a>
* 結果の不適切な連結のためにリンクを返していなかった RangePartitionResolver.resolveForRead のバグを修正しました。

### <a name="1.5.5"/>1.5.5</a>
* hashParitionResolver resolveForRead() を修正しました - 登録済みのすべてのリンクの一覧を返す代わりに、指定したパーティション キーが例外をスローしない問題を修正しました。

### <a name="1.5.4"/>1.5.4</a>
* 問題 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) を修正します - 専用 HTTPS エージェント: DocumentDB 目的用のグローバル エージェントが変更されないようにします。 lib のすべての要求に対して、専用エージェントを使用します。

### <a name="1.5.3"/>1.5.3</a>
* 問題 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) を修正します - メディア ID のダッシュを正しく処理します。

### <a name="1.5.2"/>1.5.2</a>
* 問題 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) を修正します - EventEmitter リスナー リーク警告の問題を修正します。

### <a name="1.5.1"/>1.5.1</a>
* 問題 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) を修正します - 大文字小文字を区別するシステムのため、Hash フォルダーの名前を hash に変更します。

### <a name="1.5.0"/>1.5.0</a>
* ハッシュと範囲パーティション リゾルバーを追加することでシャーディングのサポートを実装。

### <a name="1.4.0"/>1.4.0</a>
* Upsert を実装します。 documentClient の新しい upsertXXX メソッド

### <a name="1.3.0"/>1.3.0</a>
* 他の SDK とバージョン番号をそろえるため、このリリースはスキップされました。

### <a name="1.2.2"/>1.2.2</a>
* Q promises ラッパーを新しいリポジトリに分割します。
* 更新し、npm レジストリのファイルをパッケージ化します。

### <a name="1.2.1"/>1.2.1</a>
* ID ベースのルーティングを実装します。
* 問題 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) を修正します - 現在のプロパティがメソッド current() と競合します。

### <a name="1.2.0"/>1.2.0</a>
* 地理空間インデックスのサポートを追加しました。
* すべてのリソースの id プロパティを検証します。 リソースの ID には ?、/、#、&#47;&#47; 文字を使えず、終わりの文字をスペースにできません。
* ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="1.1.0"/>1.1.0</a>
* V2 インデックス作成ポリシーを実装します。

### <a name="1.0.3"/>1.0.3</a>
* 問題 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - core および promise SDK で eslint および grunt 構成を実装しました。

### <a name="1.0.2"/>1.0.2</a>
* 問題 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - promise ラッパーには、エラーのあるヘッダーが含まれません。

### <a name="1.0.1"/>1.0.1</a>
* readConflicts、readConflictAsync、queryConflicts を追加し、競合に関して問い合わせる機能を実装しました。
* API ドキュメントを更新しました。
* 問題 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync エラー。

### <a name="1.0.0"/>1.0.0</a>
* GA SDK。

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

提供終了の SDK で DocumentDB に要求した場合、サービスにより却下されます。

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.11.0](#1.11.0) |2017 年 3 月 16 日 |--- |
| [1.10.2](#1.10.2) |2017 年 1 月 27 日 |--- |
| [1.10.1](#1.10.1) |2016 年 12 月 22 日 |--- |
| [1.10.0](#1.10.0) |2016 年 10 月 3 日 |--- |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |--- |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1.5.6](#1.5.6) |2016 年 3 月 8 日 |--- |
| [1.5.5](#1.5.5) |2016 年 2 月 2 日 |--- |
| [1.5.4](#1.5.4) |2016 年 2 月 1 日 |--- |
| [1.5.2](#1.5.2) |2016 年 1 月 26 日 |--- |
| [1.5.2](#1.5.2) |2016 年 1 月 22 日 |--- |
| [1.5.1](#1.5.1) |2016 年 1 月 4 日 |--- |
| [1.5.0](#1.5.0) |2015 年 12 月 31 日 |--- |
| [1.4.0](#1.4.0) |2015 年 10 月 6 日 |--- |
| [1.3.0](#1.3.0) |2015 年 10 月 6 日 |--- |
| [1.2.2](#1.2.2) |2015 年 9 月 10 日 |--- |
| [1.2.1](#1.2.1) |2015 年 8 月 15 日 |--- |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |--- |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1.0.3](#1.0.3) |2015 年 6 月 4 日 |--- |
| [1.0.2](#1.0.2) |2015 年 5 月 23 日 |--- |
| [1.0.1](#1.0.1) |2015 年 5 月 15 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |--- |

## <a name="faq"></a>FAQ
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>関連項目
DocumentDB に関する詳細は、 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。


