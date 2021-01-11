---
title: Azure Cosmos DB での Change Feed サポートの使用
description: Azure Cosmos DB の変更フィード サポートを使用して、ドキュメントの変更、トリガーなどのイベント ベースの処理を追跡し、キャッシュや分析システムを最新の状態に維持します。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 4cd0ad1553f04a781349a4664fbb408108015632
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510285"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Azure Cosmos DB の変更フィード

Azure Cosmos DB の変更フィードのサポートは、Azure Cosmos コンテナーの変更をリッスンすることで機能します。 変更されたドキュメントは、変更された順に並べ替えられた一覧に出力されます。 変更は保持され、非同期的に増分処理できます。また、出力を 1 つ以上のコンシューマーに分散させて並列処理することもできます。

[変更フィードの設計パターン](change-feed-design-patterns.md)について、さらに学習します。

## <a name="supported-apis-and-client-sdks"></a>サポートされる API とクライアント SDK

この機能は現在、次の Azure Cosmos DB API とクライアント SDK でサポートされています。

| **クライアント ドライバー** | **SQL API** | **Cassandra 用 Azure Cosmos DB API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API**|**テーブル API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | はい | はい | はい | はい | いいえ |
|Java|はい|はい|はい|はい|いいえ|
|Python|はい|はい|はい|はい|いいえ|
|Node/JS|はい|はい|はい|はい|いいえ|

## <a name="change-feed-and-different-operations"></a>変更フィードとさまざまな操作

現在、変更フィードにはすべての挿入と更新が表示されています。 特定の種類の操作について、変更フィードをフィルター処理することはできません。 他の可能な方法の 1 つとして、更新を表す項目に "ソフト マーカー" を追加し、変更フィード内の項目を処理する際、それに基づいてフィルター処理するという方法があります。

現在、変更フィードでは削除は記録されません。 前の例と同様に、削除される項目にソフト マーカーを追加できます。 たとえば、項目に "deleted" という名前の属性を追加して "true" に設定し、項目に TTL を設定すると、それを自動的に削除できます。 5 年前に追加された項目など、履歴項目の変更フィード (項目に対応する最新の変更点には、中間変更は含まれません) を読み取ることができます。 変更フィードはコンテナーの起点まで遡って読み取ることができますが、項目が削除されると、変更フィードから削除されます。

### <a name="sort-order-of-items-in-change-feed"></a>変更フィードの項目の並べ替え順序

変更フィードの項目の順序は変更時刻順です。 この並べ替え順序は、論理パーティション キーごとに保証されます。

### <a name="consistency-level"></a>整合性レベル

変更フィードを最終的な整合性レベルで使用している場合は、後続の変更フィードの読み取り操作の間に重複するイベントが発生する可能性があります (1 回の読み取り操作の最後のイベントが、次の操作の最初のイベントとして表示されます)。

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>複数リージョンの Azure Cosmos アカウントの変更フィード

複数リージョンの Azure Cosmos アカウントで書き込みリージョンがフェールオーバーすると、変更フィードは手動のフェールオーバー操作をまたいで機能し、隣接になります。

### <a name="change-feed-and-time-to-live-ttl"></a>変更フィードと Time to Live (TTL)

ある項目の TTL (Time to Live) プロパティが -1 に設定されている場合、変更フィードは永久に持続します。 データが削除されなければ、変更フィード内に保持されます。  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>変更フィードと _etag、_lsn、_ts

_etag は内部形式であり、いつでも変更され得るため、これに依存することはできません。 _ts は、変更または作成のタイムスタンプです。 _ts は時系列比較に利用できます。 _lsn は変更フィードに対してのみ追加されるバッチ ID であり、トランザクション ID を表します。 多数の項目に同じ _lsn が付くことがあります。 FeedResponse の ETag は、項目にある _etag とは異なります。 _etag は内部識別子であり、同時実行管理に使用されます。 _etag プロパティは、項目のバージョンを示します。一方、ETag プロパティはフィードの優先順位を設定するために使用します。

## <a name="working-with-change-feed"></a>変更フィードの操作

次のオプションを使用して変更フィードを操作できます。

* [変更フィードと Azure Functions の併用](change-feed-functions.md)
* [変更フィードと変更フィード プロセッサの併用](change-feed-processor.md) 

変更フィードは、コンテナー内の論理パーティション キーごとに利用できるため、次の図のように 1 つまたは複数のコンシューマーに分散して並列処理できます。

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Azure Cosmos DB の Change Feed の分散処理" border="false":::

## <a name="features-of-change-feed"></a>変更フィードの特徴

* 変更フィードは、すべての Azure Cosmos アカウントで既定で有効になっています。

* 他の Azure Cosmos DB の操作と同様、Azure Cosmos データベースに関連付けられているどのリージョンにおいても、変更フィードからの読み取りには、[プロビジョニング スループット](request-units.md)を使用できます。

* 変更フィードには、コンテナー内の項目に対して行われた挿入操作と更新操作が含まれています。 削除の代わりに、ドキュメントなどの項目内で "論理的な削除" フラグを設定することで削除をキャプチャできます。 または、[TTL 機能](time-to-live.md)を使用して項目の有効期間を設定することもできます。 たとえば、24 時間に設定し、そのプロパティの値を使用して削除をキャプチャします。 この場合は、TTL の有効期限よりも短い期間に含まれる変更を処理する必要があります。

* 項目に加えられた変更はそれぞれ変更フィード内に 1 回だけ出現し、クライアントがそれらのチェックポイント処理ロジックを管理しなければなりません。 チェックポイントの管理の複雑さを回避する必要がある場合は、変更フィード プロセッサによって、自動チェックポイント処理と "最低 1 回" というセマンティクスが提供されます。 [変更フィードと変更フィード プロセッサの併用](change-feed-processor.md)に関するページを参照してください。

* 変更ログには、特定の項目の最新の変更のみが含まれます。 途中の変更は利用できない場合があります。

* 変更フィードは論理パーティション キーの値ごとに変更日時順に並べ替えられます。 パーティション キーの値が異なる場合、順序は保証されません。

* 変更の同期は任意の時点から行うことが可能です。つまり、変更内容を利用できるデータ保持期間は固定されていません。

* Azure Cosmos コンテナーのすべての論理パーティション キーで並行して変更を使用可能です。 この機能により、大規模なコンテナーの変更を複数のコンシューマーで並行処理できるようになります。

* アプリケーションは、同じコンテナーに対して複数の変更フィードを同時に要求できます。 ChangeFeedOptions.StartTime を使用して、最初の始点を指定できます。 たとえば、設定した時刻に対応する継続トークンを検出する場合です。 ContinuationToken を指定している場合、StartTime と StartFromBeginning の値よりも優先されます。 ChangeFeedOptions.StartTime の精度は 5 秒以下です。

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra と MongoDB の API の変更フィード

変更フィード機能は、MongoDB API では変更ストリームとして表示され、Cassandra API では述語を含むクエリとして表示されます。 MongoDB API の実装の詳細については、[MongoDB 用の Azure Cosmos DB API の変更ストリーム](mongodb-change-streams.md)に関するページを参照してください。

ネイティブ Apache Cassandra には、変更データ キャプチャ (CDC) が用意されています。CDC は、特定のテーブルに対してアーカイブのフラグを設定し、CDC ログ用に構成可能なディスク上のサイズに達すると、そのテーブルへの書き込みを拒否するメカニズムです。 Azure Cosmos DB API for Cassandra の変更フィード機能により、CQL を介して述語を使用して変更をクエリする機能が向上します。 実装の詳細については、[Cassandra 用の Azure Cosmos DB API の変更フィード](cassandra-change-feed.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

以下の記事で、変更フィードに関してさらに詳しく知ることができます。

* [変更フィードを読み取るオプション](read-change-feed.md)
* [変更フィードと Azure Functions の併用](change-feed-functions.md)
* [変更フィード プロセッサの使用](change-feed-processor.md)
