---
title: Azure Cosmos DB での Change Feed サポートの使用
description: Azure Cosmos DB の Change Feed サポートを使用して、ドキュメントの変更を追跡し、トリガーなどのイベント ベースの処理を実行したり、キャッシュや分析システムを最新の状態に維持したりします。
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 85a1dad9feb15550cf27cf032802af5055fdf155
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525638"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Azure Cosmos DB の変更フィード - 概要

Azure Cosmos DB の変更フィードのサポートは、Azure Cosmos DB コンテナーの変更をリッスンすることで機能します。 変更されたドキュメントは、変更された順に並べ替えられた一覧に出力されます。 変更は保持され、非同期的に増分処理できます。また、出力を 1 つ以上のコンシューマーに分散させて並列処理することもできます。 

Azure Cosmos DB は、IoT、ゲーム、小売、操作ログといったアプリケーションに最適です。 このようなアプリケーションの一般的な設計パターンは、データの変更を使用して、追加のアクションをトリガーする方法です。 以下が追加のアクションの例となります。

* 項目が挿入または更新された場合に通知または API の呼び出しをトリガーする。
* IoT のリアルタイム ストリーム処理または運用データのリアルタイム分析処理。
* キャッシュ、検索エンジン、データ ウェアハウスと同期するか、データをコールド ストレージにアーカイブすることによる追加のデータ移動。

Azure Cosmos DB の変更フィードにより、次の図のようにこれらの各パターンに対応する効率的でスケーラブルなソリューションを構築できます。

![Azure Cosmos DB の Change Feed を使用してリアルタイム分析とイベント ドリブンのコンピューティング シナリオを強化](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>サポートされる API とクライアント SDK

この機能は現在、次の Azure Cosmos DB API とクライアント SDK でサポートされています。

| **クライアント ドライバー** | **Azure CLI** | **SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API**|**テーブル API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | NA | はい | いいえ  | いいえ  | はい | いいえ  |
|Java|NA|はい|いいえ |いいえ |はい|いいえ |
|Python|NA|はい|いいえ |いいえ |はい|いいえ |
|Node/JS|NA|はい|いいえ |いいえ |はい|いいえ |

## <a name="change-feed-and-different-operations"></a>変更フィードとさまざまな操作

現在、変更フィードにはすべての操作が反映されます。 特定の操作の変更フィードを操作できるようにする機能 (たとえば、更新だけを対象とし、挿入は対象から除く、など) はまだありません。 更新を表す項目に "ソフト マーカー" を追加し、変更フィード内の項目を処理する際、それに基づいてフィルター処理することができます。 現在、変更フィードは削除をログに記録していません。 前の例と同様に、削除する項目にソフト マーカーを追加できます。たとえば、"deleted" という属性を項目に追加して "true" に設定し、その項目に TTL を設定できます。これで、その項目を自動的に削除できます。 5 年前に追加された項目など、古い項目の変更フィードを読み取ることができます。 項目が削除されていなければ、コンテナーの始まりまでさかのぼって変更フィードを読み取ることができます。

### <a name="sort-order-of-items-in-change-feed"></a>変更フィードの項目の並べ替え順序

変更フィードの項目の順序は変更時刻順です。 この並べ替え順序は、論理パーティション キーごとに保証されます。

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>複数リージョンの Azure Cosmos アカウントの変更フィード

複数リージョンの Azure Cosmos アカウントで書き込みリージョンがフェールオーバーすると、変更フィードは手動のフェールオーバー操作をまたいで機能し、隣接になります。

### <a name="change-feed-and-time-to-live-ttl"></a>変更フィードと Time to Live (TTL)

ある項目の TTL (Time to Live) プロパティが -1 に設定されている場合、変更フィードは永久に持続します。 データが削除されなければ、変更フィード内に保持されます。  

### <a name="change-feed-and-etag-lsn-or-ts"></a>変更フィードと _etag、_lsn、_ts

_etag は内部形式であり、いつでも変更され得るため、これに依存することはできません。 _ts は、変更または作成のタイムスタンプです。 _ts は時系列比較に利用できます。 _lsn は変更フィードに対してのみ追加されるバッチ ID であり、トランザクション ID を表します。 多数の項目に同じ _lsn が付くことがあります。 FeedResponse の ETag は、項目にある _etag とは異なります。 _etag は内部識別子であり、コンカレンシー制御に使用され、項目のバージョンを伝えますが、ETag はフィードのシーケンス処理に使用されます。

## <a name="change-feed-use-cases-and-scenarios"></a>変更フィードのユース ケースとシナリオ

変更フィードによって、大量の書き込みを伴う大規模なデータセットを効率的に処理できます。 変更フィードは、データセット全体にクエリを実行して変更内容を確認する方法に代わる機能を提供します。

### <a name="use-cases"></a>ユース ケース

たとえば、変更フィードを使用すると、次のタスクを効率よく実行できます。

* Azure Cosmos DB に格納されたデータに従って、キャッシュの更新、検索インデックスの更新、データ ウェアハウスの更新を行う。

* アプリケーション レベルのデータの階層化とアーカイブを実装する (例: "ホット データ" を Azure Cosmos DB に格納し、"コールド データ" を [Azure Blob Storage](../storage/common/storage-introduction.md) などの他のストレージ システムにエイジアウトする)。

* 異なる論理パーティション キーを持つ別の Azure Cosmos アカウントや Azure Cosmos コンテナーへの移行をダウン タイムなしで実行する。

* Azure Cosmos DB を使用して[ラムダ アーキテクチャ](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)を実装する。Azure Cosmos DB はリアルタイム レイヤーと、バッチのクエリ処理レイヤーの両方をサポートするので、低 TCO でラムダ アーキテクチャを使用可能です。

* デバイス、センサー、インフラストラクチャ、アプリケーションからイベント データを受信および格納し、こうしたイベントを [Spark](../hdinsight/spark/apache-spark-overview.md) などでリアルタイムに処理する。  次の図は、変更フィードを使用して、Azure Cosmos DB でラムダ アーキテクチャを実装する方法を示しています。

![取り込みとクエリに対応する Azure Cosmos DB ベースのラムダ パイプライン](./media/change-feed/lambda.png)

### <a name="scenarios"></a>シナリオ

変更フィードで簡単に実装できるシナリオの例を以下にいくつか示します。

* [サーバーレス](https://azure.microsoft.com/solutions/serverless/) Web やモバイル アプリ内で、お客様のプロファイル、設定、場所のすべての変更などのイベントを追跡し、[Azure Functions](change-feed-functions.md) を使用して各デバイスにプッシュ通知を送信するなど、特定のアクションをトリガーできます。

* たとえば、Azure Cosmos DB を使用してゲームを構築する場合、Change Feed を使用して完了したゲームのスコアに基づくリアルタイムのスコアボードを実装できます。


## <a name="working-with-change-feed"></a>変更フィードの操作

次のオプションを使用して変更フィードを操作できます。

* [変更フィードと Azure Functions の併用](change-feed-functions.md)
* [変更フィードと変更フィードプロセッサ ライブラリの併用](change-feed-processor.md) 

変更フィードは、コンテナー内の論理パーティション キーごとに利用できるため、次の図のように 1 つまたは複数のコンシューマーに分散して並列処理できます。

![Azure Cosmos DB の Change Feed の分散処理](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>変更フィードの特徴

* 変更フィードは、すべての Azure Cosmos アカウントで既定で有効になっています。

* 他の Azure Cosmos DB の操作と同様、Azure Cosmos データベースに関連付けられているどのリージョンにおいても、変更フィードからの読み取りには、[プロビジョニング スループット](request-units.md)を使用できます。

* 変更フィードには、コンテナー内の項目に対して行われた挿入操作と更新操作が含まれています。 削除の代わりに、ドキュメントなどの項目内で "論理的な削除" フラグを設定することで削除をキャプチャできます。 または、[TTL 機能](time-to-live.md)を使用して項目の有効期間を設定することもできます。 たとえば、24 時間に設定し、そのプロパティの値を使用して削除をキャプチャします。 この場合は、TTL の有効期限よりも短い期間に含まれる変更を処理する必要があります。 

* 項目に加えられた変更はそれぞれ変更フィード内に 1 回だけ出現し、クライアントがそれらのチェックポイント処理ロジックを管理しなければなりません。 チェックポイントの管理の複雑さを回避できるよう、変更フィード プロセッサ ライブラリは、自動チェックポイント処理と "最低 1 回" というセマンティクスを備えています。 [変更フィードと変更フィード プロセッサ ライブラリの併用](change-feed-processor.md)についてご確認ください。

* 変更ログには、特定の項目の最新の変更のみが含まれます。 途中の変更は利用できない場合があります。

* 変更フィードは論理パーティション キーの値ごとに変更日時順に並べ替えられます。 パーティション キーの値が異なる場合、順序は保証されません。

* 変更の同期は任意の時点から行うことが可能です。つまり、変更内容を利用できるデータ保持期間は固定されていません。

* Azure Cosmos コンテナーのすべての論理パーティション キーで並行して変更を使用可能です。 この機能により、大規模なコンテナーの変更を複数のコンシューマーで並行処理できるようになります。

* アプリケーションは、同じコンテナーに対して複数の変更フィードを同時に要求できます。 ChangeFeedOptions.StartTime を使用して、最初の始点を指定できます。 たとえば、設定した時刻に対応する継続トークンを検出する場合です。 ContinuationToken を指定している場合、StartTime 値と StartFromBeginning 値よりも優先されます。 ChangeFeedOptions.StartTime の精度は 5 秒以下です。 

## <a name="next-steps"></a>次の手順

以下の記事で、変更フィードに関してさらに詳しく知ることができます。

* [変更フィードを読み取るオプション](read-change-feed.md)
* [変更フィードと Azure Functions の併用](change-feed-functions.md)
* [Change Feed Processor ライブラリの使用](change-feed-processor.md)
