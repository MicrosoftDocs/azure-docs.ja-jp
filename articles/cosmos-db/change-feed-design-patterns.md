---
title: Azure Cosmos DB の変更フィードの設計パターン
description: 一般的な変更フィードの設計パターンの概要
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334607"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Azure Cosmos DB の変更フィードの設計パターン
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB の変更フィードによって、大量の書き込みを伴う大規模なデータセットを効率的に処理できます。 変更フィードは、データセット全体にクエリを実行して変更内容を確認する方法に代わる機能を提供します。 このドキュメントでは、一般的な変更フィードの設計パターン、設計のトレードオフ、および変更フィードの制限事項について重点的に説明します。

Azure Cosmos DB は、IoT、ゲーム、小売、操作ログといったアプリケーションに最適です。 このようなアプリケーションの一般的な設計パターンは、データの変更を使用して、追加のアクションをトリガーする方法です。 以下が追加のアクションの例となります。

* 項目が挿入または更新された場合に通知または API の呼び出しをトリガーする。
* IoT のリアルタイム ストリーム処理または運用データのリアルタイム分析処理。
* キャッシュ、検索エンジン、データ ウェアハウス、またはコールド ストレージとの同期などのデータ移動。

Azure Cosmos DB の変更フィードにより、次の図のようにこれらの各パターンに対応する効率的でスケーラブルなソリューションを構築できます。

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Azure Cosmos DB の変更フィードを使用してリアルタイム分析とイベント ドリブンのコンピューティング シナリオを強化" border="false":::

## <a name="event-computing-and-notifications"></a>イベントのコンピューティングと通知

Azure Cosmos DB の変更フィードでは、特定のイベントに基づいて API に通知をトリガーするか呼び出しを送信する必要のあるシナリオを簡略化することができます。 [変更フィード プロセッサ ライブラリ](change-feed-processor.md)を使用すると、コンテナーの変更を自動的にポーリングし、書き込みや更新が行われるたびに外部 API を呼び出すことができます。

また、特定の条件に基づいて、選択的に通知をトリガーしたり、API の呼び出しを送信したりすることもできます。 たとえば、[Azure Functions](change-feed-functions.md) を使用して変更フィードから読み取る場合、特定の条件が満たされた場合にのみ通知が送信されるように、関数にロジックを組み込むことができます。 Azure Functions コードは書き込みと更新のたびに実行されますが、通知は、特定の条件が満たされた場合にのみ送信されます。

## <a name="real-time-stream-processing"></a>リアルタイム ストリーム処理

Azure Cosmos DB の変更フィードは、IoT のリアルタイム ストリーム処理や運用データのリアルタイム分析処理に使用できます。
たとえば、デバイス、センサー、インフラストラクチャ、アプリケーションからイベント データを受信および格納し、こうしたイベントを [Spark](../hdinsight/spark/apache-spark-overview.md) でリアルタイムに処理できます。 次の図は、変更フィードを使用して、Azure Cosmos DB でラムダ アーキテクチャを実装する方法を示しています。

:::image type="content" source="./media/change-feed/lambda.png" alt-text="取り込みとクエリに対応する Azure Cosmos DB ベースのラムダ パイプライン" border="false":::

多くの場合、ストリーム処理の実装では、最初に大量の受信データを Azure Event Hub や Apache Kafka などの一時メッセージ キューに受信します。 変更フィードは、高いデータ インジェスト率の持続と、読み取りと書き込みの低待機時間の保証をサポートする Azure Cosmos DB の機能により、優れた代替手段となっています。 メッセージ キューに優る Azure Cosmos DB の変更フィードの利点は次のとおりです。

### <a name="data-persistence"></a>データの永続化

Azure Cosmos DB に書き込まれたデータは、変更フィードに表示され、削除されるまで保持されます。 メッセージ キューには通常、最大保持期間が設けられています。 たとえば、[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) には最大 90 日間のデータ保持期間が設けられています。

### <a name="querying-ability"></a>クエリ機能

Cosmos コンテナーの変更フィードからの読み取りに加えて、Azure Cosmos DB に格納されているデータに対して SQL クエリを実行することもできます。 変更フィードは、コンテナー内に既に存在するデータを複製したものではなく、データを読み取る別のメカニズムにすぎません。 そのため、変更フィードからデータを読み取った場合、そのデータは同じ Azure Cosmos DB コンテナーのクエリと常に整合性があります。

### <a name="high-availability"></a>高可用性

Azure Cosmos DB には、最大 99.999% の読み取りと書き込みの可用性が備わっています。 多くのメッセージ キューとは異なり、Azure Cosmos DB のデータは簡単にグローバルに分散させ、[RTO (目標復旧時間)](./consistency-levels.md#rto) を 0 にして構成できます。

変更フィードの項目を処理した後で、マテリアライズドビューを作成したり、集計された値を Azure Cosmos DB に戻して保持したりできます。 たとえば、Azure Cosmos DB を使用してゲームを構築する場合、Change Feed を使用して完了したゲームのスコアに基づくリアルタイムのスコアボードを実装できます。

## <a name="data-movement"></a>データの移動

リアルタイムのデータ移動のために変更フィードからデータを読み取ることもできます。

たとえば、変更フィードを使用すると、次のタスクを効率よく実行できます。

* Azure Cosmos DB に格納されているデータで、キャッシュ、検索インデックス、データ ウェアハウスを更新する。

* 異なる論理パーティション キーを持つ別の Azure Cosmos アカウントや Azure Cosmos コンテナーへの移行をダウン タイムなしで実行する。

* アプリケーション レベルのデータの階層化とアーカイブを実装する。 たとえば、"ホット データ" を Azure Cosmos DB に格納し、古くなった "コールド データ" を [Azure Blob Storage](../storage/common/storage-introduction.md) などの他のストレージ システムに格納することができます。

[パーティションとコンテナーの間でデータを非正規化する](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)必要がある場合は、こうしたデータ レプリケーションのソースとして、コンテナーの変更フィードから読み取ることができます。 変更フィードを使用したリアルタイムのデータ レプリケーションでは、最終的な整合性のみを保証できます。 Cosmos コンテナーでの変更の処理中に[変更フィード プロセッサがどのくらい遅れを取るかを監視](how-to-use-change-feed-estimator.md)できます。

## <a name="event-sourcing"></a>イベント ソーシング

[イベント ソーシング パターン](/azure/architecture/patterns/event-sourcing)では、追加専用ストアを使用して、そのデータに対する一連のアクションをすべて記録します。 Azure Cosmos DB の変更フィードは、すべてのデータ インジェストが書き込み (更新や削除なし) としてモデル化されているイベント ソーシング アーキテクチャの中央データ ストアとして最適な選択肢となります。 この場合、Azure Cosmos DB への各書き込みは "イベント" であり、変更フィードには過去のイベントの完全な記録が保持されます。 中央イベント ストアによって発行されるイベントの一般的な用途は、マテリアライズドビューを維持したり、外部システムと統合したりするためです。 変更フィードには保持期間の制限がないため、Cosmos コンテナーの変更フィードの先頭から読み取ることによって、過去のすべてのイベントを再生できます。

[複数の変更フィード コンシューマーが同じコンテナーの変更フィードにサブスクライブする](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)ようにできます。 [リース コンテナー](change-feed-processor.md#components-of-the-change-feed-processor)のプロビジョニングされたスループットの他に、変更フィードを利用する費用はかかりません。 変更フィードは、利用されているかどうかに関係なく、すべてのコンテナーで使用可能です。

Azure Cosmos DB は、その水平スケーラビリティと高可用性の強みにより、イベント ソーシング パターンの優れた追加専用の中央永続データ ストアになります。 さらに、変更フィード プロセッサ ライブラリには ["少なくとも 1 回"](change-feed-processor.md#error-handling) の保証が備わっているため、イベントを処理し損なうことがありません。

## <a name="current-limitations"></a>現在の制限

変更フィードには、理解しておくべき重要な制限事項があります。 Cosmos コンテナーの項目は常に変更フィード内に残りますが、変更フィードは完全な操作ログではありません。 変更フィードを利用するアプリケーションを設計する場合は、考慮すべき重要な領域があります。

### <a name="intermediate-updates"></a>中間の更新

変更フィードには、特定の項目の最新の変更のみが含まれます。 変更を処理するときは、利用可能な最新バージョンの項目を読み取ります。 短時間のうちに同じ項目に対して複数の更新が発生すると、中間の更新を処理し損なう可能性があります。 更新を追跡して、ある項目に対する過去の更新を再生できるようにしたい場合は、代わりにこれらの更新を一連の書き込みとしてモデル化することをお勧めします。

### <a name="deletes"></a>Deletes

変更フィードでは削除はキャプチャされません。 コンテナーから項目を削除すると、その項目は変更フィードからも削除されます。 これに対処する最も一般的な方法は、削除される項目にソフト マーカーを追加することです。 "Deleted" というプロパティを追加し、削除時にそのプロパティを "true" に設定することができます。 このドキュメントの更新は、変更フィードに表示されます。 この項目に TTL を設定して、後で自動的に削除できるようにすることができます。

### <a name="guaranteed-order"></a>保証された順序

変更フィード内の順序は 1 つのパーティション キー値内では保証されますが、複数のパーティション キー値にまたがって保証されることはありません。 意味のある順序保証を提供するパーティション キーを選択するようにしてください。

たとえば、イベント ソーシング設計パターンを使用する小売アプリケーションについて考えてみます。 このアプリケーションでは、さまざまなユーザー操作が、Azure Cosmos DB への書き込みとしてモデル化される "イベント" となります。 たとえば、次の順序でイベントが発生した場合を考えてみましょう。

1. 顧客が商品 A をショッピング カートに追加する
2. 顧客が商品 B をショッピング カートに追加する
3. 顧客が商品 A をショッピング カートから削除する
4. 顧客が支払いをして、ショッピング カートの中身が発送される

現在のショッピング カートの中身のマテリアライズドビューが顧客ごとに保持されます。 このアプリケーションでは、これらのイベントが発生順に処理されるようにする必要があります。 たとえば、商品 A が削除される前にカートの支払いが処理されていた場合、顧客は希望する商品 B ではなく、商品 A を発送させていた可能性があります。これらの 4 つのイベントが発生順に処理されることを保証するためには、それらのイベントが同じパーティション キー値内に存在する必要があります。 **ユーザー名** (顧客ごとに一意のユーザー名がある) をパーティション キーとして選択した場合は、これらのイベントが Azure Cosmos DB に書き込まれるのと同じ順序で変更フィードに表示されることを保証できます。

## <a name="examples"></a>例

ここでは、Microsoft Docs で提供されるサンプルの範囲を超えて、変更フィードの実際のコード例をいくつか紹介します。

- [変更フィードの入門](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [変更フィードを中心とする IoT のユース ケース](https://github.com/AzureCosmosDB/scenario-based-labs)
- [変更フィードを中心とする小売のユース ケース](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>次のステップ

* [変更フィードの概要](change-feed.md)
* [変更フィードを読み取るオプション](read-change-feed.md)
* [変更フィードと Azure Functions の併用](change-feed-functions.md)