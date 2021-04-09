---
title: Azure Cosmos DB での操作のサーバー側の待機時間を監視する方法
description: Azure Cosmos DB アカウントまたはコンテナーでの操作に対するサーバー待機時間を監視する方法について説明します。 Azure Cosmos DB アカウントの所有者は、Azure Cosmos アカウントでのサーバー側の待機時間の問題を把握できます。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: ec6a9db63504958640137fcd0fcfc904eb01afa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074731"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Azure Cosmos DB のコンテナーまたはアカウントでの操作に対するサーバー側の待機時間を監視する方法
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor for Azure Cosmos DB では、アカウントを監視したり、ダッシュボードを作成したりするためのメトリック ビューが提供されています。 Azure Cosmos DB のメトリックは既定で収集されるので、この機能を使用するために何かを明示的に有効にしたり構成したりする必要はありません。 操作のサーバー側の待機時間を表示するには、サーバー側待機時間メトリックを使用します。 Azure Cosmos DB では、直接接続でのポイント読み取りおよび書き込み操作に対して、10 ミリ秒未満の SLA が提供されています。 ポイント読み取りおよび書き込み操作の場合、SLA は [SLA のドキュメント](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)で詳しく説明されているように計算されます。

次のようなポイント操作で待ち時間が異常に長引く場合、サーバー側の待ち時間を監視できます。

* 直接接続モードでパーティション キーと ID を使用した取得または設定操作
* 読み取り操作または書き込み操作
* クエリ

診断ログを調べて、返されたデータのサイズを確認できます。 クエリ操作で待ち時間が長引く場合、診断ログで[スループットや毎秒 RU](cosmosdb-monitor-resource-logs.md#diagnostic-queries) が高くないか調べます。 サーバー側の待ち時間には、データがクライアントに返されるまでバックエンド インフラストラクチャで費やされた時間が表示されます。 バックエンドの待ち時間問題をなくすためには、この指標を確認することが重要です。

## <a name="view-the-server-side-latency-metric"></a>サーバー側待機時間メトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Azure Monitor のメトリック ウィンドウ":::

1. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な **サブスクリプション** と **リソース グループ** を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="メトリックを表示する Azure Cosmos DB アカウントを選択する":::

1. 次に、使用可能なメトリックの一覧から **[Server Side Latency]\(サーバー側待機時間\)** メトリックを選択します。 この一覧で使用可能なすべてのメトリックの詳細については、「[カテゴリ別のメトリック](monitor-cosmos-db-reference.md)」の記事を参照してください。 この例では、 **[Server Side Latency]\(サーバー側待機時間\)** および集計値として **[平均]** を選択します。 これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。 選択された期間の 1 分あたりのサーバー側の待機時間を確認できます。  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Azure portal からサーバー側待機時間メトリックを選択する":::

## <a name="filters-for-server-side-latency"></a>サーバー側の待機時間のフィルター

メトリックを特定の **CollectionName**、**ConnectionMode**、**DatabaseName**、**OperationType**、**Region**、**PublicAPIType** でフィルター処理してグラフを表示することもできます。 

メトリックをフィルター処理するには、 **[フィルターの追加]** を選択し、**PublicAPIType** などの必要なプロパティを選択して、値 **sql** を選択します。 **OperationType** に対して別のフィルターを追加します。 その後、グラフには、選択した期間におけるさまざまな操作のサーバー側待機時間が表示されます。 ストアド プロシージャを介して実行された操作は、ログに記録されないため、OperationType メトリックでは使用できません。

次の図に示すように、各操作に対する **サーバー側待機時間** メトリックが表示されます。

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="サーバー側待機時間メトリックのフィルター":::

**[Apply splitting]\(分割の適用\)** オプションを使用して、メトリックをグループ化することもできます。  

## <a name="next-steps"></a>次のステップ

* Azure の[診断設定](cosmosdb-monitor-resource-logs.md)を使用して Azure Cosmos DB データを監視する
* [Azure Cosmos DB コントロール プレーン操作を監査する](audit-control-plane-logs.md)
