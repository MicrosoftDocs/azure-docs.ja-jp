---
title: Azure Cosmos DB での操作のスループットの使用状況を監視する
description: Azure Cosmos DB で、操作のスループットまたは要求ユニットの使用状況を監視する方法について説明します。 Azure Cosmos DB アカウントの所有者は、より多くの要求ユニットを使用している操作を把握できます。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: d6f0f30d7437b6f8ecc1d915eb3d3195f2504fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93098248"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Azure Cosmos DB で操作のスループットまたは要求ユニットの使用状況を監視する方法
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor for Azure Cosmos DB では、アカウントを監視したり、ダッシュボードを作成したりするためのメトリック ビューが提供されています。 Azure Cosmos DB のメトリックは既定で収集されるので、この機能を使用するために何かを明示的に有効にしたり構成したりする必要はありません。 さまざまな種類の操作についての要求ユニットの使用状況を取得するために、**合計要求ユニット** メトリックが使用されます。 後で、どの操作がスループットの大半を使用したのか分析できます。 既定では、スループット データは 1 分間隔で集計されます。 ただし、時間の粒度オプションを変更することで集計単位を変更できます。

要求ユニットの使用状況データを分析するには、次の 2 つの方法があります。

* 指定された時間内で、どの操作が多くの要求ユニットを使用しているか。
* 全般的にどの操作が、多くの要求ユニットを消費することによってワークロードを占有しているか。
この分析により、挿入や upsert などの操作に着目し、そのインデックス付けを確認できます。 特定のフィールドのインデックス付けが過剰または不足しているかを確認し、[インデックス作成ポリシー](index-policy.md#include-exclude-paths)を変更してパスを含めたり除外したりすることができます。

特定のクエリが多くの要求ユニットを使用していることに気づいた場合、次のようなアクションを実行できます。

* 要求しているデータの量が適切かどうかを再検討します。
* フィルター句のあるインデックスを使用するようにクエリを変更します。
* 低コストの UDF 関数呼び出しを実行します。
* パーティション キーを定義して、クエリがさまざまなパーティションにファンアウトすることを最小限に抑えます。
* また、呼び出しの応答で返されるクエリ メトリックや診断ログの詳細も使用できます。クエリの実行の詳細については、[クエリ パフォーマンスのチューニング](sql-api-query-metrics.md)に関する記事を参照してください。
* 合計から開始し、適切なディメンションを使用して平均使用量を調べることができます。

## <a name="view-the-total-request-unit-usage-metric"></a>要求ユニットの合計使用状況メトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Azure Monitor のメトリック ウィンドウ":::

1. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な **サブスクリプション** と **リソース グループ** を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="メトリックを表示する Azure Cosmos DB アカウントを選択する":::

1. 次に、使用可能なメトリックの一覧から **[Total Request Units]\(合計要求ユニット\)** メトリックを選択します。 この一覧で使用可能なすべてのメトリックの詳細については、「[カテゴリ別のメトリック](monitor-cosmos-db-reference.md)」の記事を参照してください。 この例では、 **[Total Request Units]\(合計要求ユニット\)** およ集計値として **[Avg]\(平均\)** を選択します。 これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。 選択した期間に消費された要求ユニットの 1 分あたりの平均数を確認できます。  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Azure portal からのメトリックの選択":::

## <a name="filters-for-request-unit-usage"></a>要求ユニットの使用状況をフィルターする

メトリックと、特定の **CollectionName**、**DatabaseName**、**OperationType**、**Region**、**Status**、および **StatusCode** によって表示されるグラフをフィルターすることもできます。 **[フィルターの追加]** および **[Apply splitting]\(分割の適用\)** オプションにより、要求ユニットの使用状況をフィルター処理し、メトリックをグループ化できます。

各操作の要求ユニットの使用状況を合計 (sum) または平均で取得するには、 **[Apply splitting]\(分割の適用\)** を選択し、次の図に示すように **[Operation type]\(操作の種類\)** とフィルター値を選択します。

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Azure Monitor での操作の Cosmos DB 要求ユニット":::

コレクション別の要求ユニットの使用状況を確認するには、 **[Apply splitting]\(分割の適用\)** を選択し、コレクション名をフィルターとして選択します。 ダッシュボード内に、コレクションを選択できる次のようなグラフが表示されます。 次に、特定のコレクション名を選択して詳細を表示できます。

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Azure Monitor でのコレクション別のすべての操作の Cosmos DB 要求ユニット":::

## <a name="next-steps"></a>次のステップ

* Azure の[診断設定](cosmosdb-monitor-resource-logs.md)を使用して Azure Cosmos DB データを監視する
* [Azure Cosmos DB コントロール プレーン操作を監査する](audit-control-plane-logs.md)
