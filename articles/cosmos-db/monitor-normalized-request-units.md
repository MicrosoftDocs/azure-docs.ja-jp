---
title: Azure Cosmos コンテナーまたはアカウントの正規化された RU/秒を監視する
description: Azure Cosmos DB で、操作の正規化された要求ユニットの使用状況を監視する方法について説明します。 Azure Cosmos DB アカウントの所有者は、より多くの要求ユニットを消費している操作を把握できます。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 01/07/2021
ms.openlocfilehash: ec82532b54e7834b62fcc03d3ee7de1345a0f546
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027799"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Azure Cosmos コンテナーまたはアカウントの正規化された RU/秒を監視する方法
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor for Azure Cosmos DB では、アカウントを監視したり、ダッシュボードを作成したりするためのメトリック ビューが提供されています。 Azure Cosmos DB のメトリックは既定で収集されるので、この機能を使用するために何かを明示的に有効にしたり構成したりする必要はありません。

**[Normalized RU Consumption]\(正規化された RU 消費量\)** メトリックは、トラフィックに関して、パーティション キー範囲がどの程度いっぱいになっているかを確認するために使用されます。 Azure Cosmos DB では、スループットがすべてのパーティション キー範囲にわたって均等に分散されます。 このメトリックは、パーティション キー範囲の最大スループット使用率の 1 秒あたりのビューを示します。 このメトリックを使用して、特定のコンテナーのパーティション キー範囲にわたって RU/秒の使用量を計算します。 このメトリックを使用して、Azure Monitor のすべてのパーティション キー範囲にわたって要求ユニットの使用率が高くなっていることを確認した場合に、ワークロードのニーズに合わせてスループットを増やす必要があります。 例 - 正規化された使用率は、すべてのパーティション キー範囲における RU/秒の使用率の最大値として定義されます。 たとえば、最大スループットが 20,000 RU/秒で、P_1 および P_2 という 2 つのパーティション キー範囲があり、それぞれが 10,000 RU/秒にスケーリングできるとします。 ある 1 秒間で、P_1 が 6000 RU を使用し、P_2 が 8000 RU を使用した場合、正規化された使用率は MAX(6000 RU / 10,000 RU, 8000 RU / 10,000 RU) = 0.8 になります。

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>正規化された RU/秒が高い場合に想定して実行する内容

特定のパーティション キーの範囲で正規化された RU/秒の消費量が 100% に達したときに、クライアントが 1 秒間の時間枠内に、その特定のパーティション キー範囲への要求をまだ行っている場合は、レート制限エラーが発生します。 クライアントは、提案された待機時間を考慮して、要求を再度試みる必要があります。 SDK を使用すると、適切に待機して事前に構成された時間を再試行することで、この状況に簡単に対処できます。  正規化された RU が100% に達したため、RU レート制限エラーを確認する必要はありません。 これは、正規化された RU がすべてのパーティション キー範囲にわたる最大使用量を表す 1 つの値であり、あるパーティション キー範囲がビジー状態でも、他のパーティション キー範囲で問題なく要求に対応できるためです。 たとえば、パーティション キー範囲のすべての RU/秒を消費するストアド プロシージャなどの 1 回の操作によって、正規化された RU/秒消費量の短時間のスパイクが発生します。 このような場合、要求レートが低い場合や、別のパーティション キー範囲で他のパーティションへの要求が行われている場合は、即時にレート制限エラーは発生しません。 

Azure Monitor メトリックでは、 **[合計要求数]** メトリックを使用して、SQL API の状態コードごとに操作を確認できます。 後で、429 状態コードによってこれらの要求をフィルターし、**操作の種類** 別に分割することができます。  

レート制限されている要求を確認するには、診断ログからこの情報を取得することをお勧めします。

複数のパーティション キー範囲にわたって正規化された RU/秒消費量が 100% または100% に近いピークのまま続く場合は、スループットを増やすことをお勧めします。 負荷の大きい操作とそのピーク使用率を確認するには、Azure Monitor 診断メトリックと Azure Monitor ログを利用します。

要約すると、 **[Normalized RU Consumption]\(正規化された RU 消費量\)** メトリックを使用して、使用状況に関して、よりウォームなパーティション キー範囲を確認します。 そのため、パーティション キー範囲に対するスループットの傾斜が得られます。 後で、追跡して Azure Monitor ログの **PartitionKeyRUConsumption** ログを確認し、使用率がホットになっている論理パーティション キーに関する情報を取得できます。 これにより、パーティション キー選択の変更やアプリケーション ロジックの変更が発生します。 レート制限を解決するには、複数のパーティション間でデータの負荷を分散させるか、必要に応じてスループットを引き上げます。 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>正規化された要求ユニット消費量メトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor のメトリック ペイン":::

3. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な **サブスクリプション** と **リソース グループ** を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="メトリックを表示する Azure Cosmos アカウントを選択する":::

4. 次に、使用可能なメトリックの一覧からメトリックを選択できます。 要求ユニット、ストレージ、待機時間、可用性、Cassandra などに固有のメトリックを選択できます。 この一覧で使用可能なすべてのメトリックの詳細については、「[カテゴリ別のメトリック](monitor-cosmos-db-reference.md)」の記事を参照してください。 この例では、 **[Normalized RU Consumption]\(正規化された RU 消費量\)** メトリックを選択し、集計値として **[最大]** を選択します。

   これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Azure portal からのメトリックの選択":::

### <a name="filters-for-normalized-request-unit-consumption"></a>正規化された要求ユニット消費量をフィルターする

メトリックと、特定の **CollectionName**、**DatabaseName**、**PartitionKeyRangeID**、**Region** によって表示されるグラフをフィルターすることもできます。 メトリックをフィルターするには **[フィルターの追加]** を選択し、調べたい **CollectionName** などの必要なプロパティと対応する値を選択します。 これで、グラフには、選択した期間中にコンテナーで消費された、正規化された RU 消費量のユニットが表示されます。  

**[Apply splitting]\(分割の適用\)** オプションを使用すると、メトリックをグループ化できます。 共有スループット データベースの場合、正規化された RU メトリックではデータベースの粒度でのみデータが表示され、コレクションごとのデータは表示されません。 そのため、共有スループット データベースでは、コレクション名による分割を適用してもデータは表示されません。

次の図に示すように、各コンテナーの正規化された要求ユニット消費量のメトリックが表示されます。

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="正規化された要求ユニット消費量メトリックにフィルターを適用する":::

## <a name="next-steps"></a>次のステップ

* Azure の[診断設定](cosmosdb-monitor-resource-logs.md)を使用して Azure Cosmos DB データを監視する
* [Azure Cosmos DB コントロール プレーン操作を監査する](audit-control-plane-logs.md)
