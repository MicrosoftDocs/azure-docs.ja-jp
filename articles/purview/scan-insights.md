---
title: Azure Purview 内のデータに関するスキャンの分析情報
description: このハウツー ガイドでは、データに関する Purview Insights のスキャン レポートを表示して使用する方法について説明します。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548687"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Azure Purview 内のデータに関するスキャンの分析情報

このハウツー ガイドでは、データに関する Azure Purview のスキャンの分析情報レポートへのアクセス、表示、フィルター処理を行う方法について説明します。

このハウツー ガイドでは、次の方法について説明します。

> [!div class="checklist"]
> * Purview アカウントから分析情報を表示します。
> * スキャンの鳥瞰図を取得します。

## <a name="prerequisites"></a>前提条件

Purview Insights の使用を開始する前に、次の手順を完了していることを確認してください。

* Azure リソースを設定し、アカウントにデータを設定します。
* データ ソースに関するスキャンを設定して完了します。

詳細については、[Azure Purview でのデータ ソースの管理 (プレビュー)](manage-data-sources.md) に関するページを参照してください。

## <a name="use-purview-scan-insights"></a>Purview のスキャンの分析情報を使用する

Azure Purview では、ソースの種類を登録してスキャンできます。 スキャンの状態は、時間の経過と共にスキャンの分析情報に表示できます。 この分析情報は、特定の期間内に何回のスキャンが失敗したか、成功したか、または取り消されたかを示します。

### <a name="view-scan-insights"></a>スキャンの分析情報を表示する

1. Azure portal で **Azure Purview** のインスタンス画面に移動し、Purview アカウントを選択します。

1. **[概要]** ページの **[開始]** セクションで、 **[Purview Studio を開く]** タイルを選択します。

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Azure portal から Purview を起動する":::

1. Purview の **[ホーム]** ページで、 **[分析情報の表示]** タイルを選択して **[分析情報]** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 領域にアクセスします。

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Azure portal で分析情報を表示する":::

1. **[分析情報]** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 領域で、 **[スキャン]** を選択して Purview の **[スキャンの分析情報]** レポートを表示します。

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>状態ごとのスキャンの回数を示す高レベルの KPI を表示して、各スキャンを詳しく調べる
 
1. 高レベルの KPI は、ある期間内に実行された合計のスキャン回数を示します。 この期間の既定値は過去 30 日間です。 ただし、過去 7 日間も選択できます。 時間フィルターに基づいて、KPI 値にはスキャンの回数が適切に反映されます。


1. 選択した時間フィルターの値に基づいて、週または日ごとの成功したスキャン、失敗したスキャン、取り消されたスキャンの分布をグラフで確認できます。

1. グラフの下部には、さらに詳しく調べるための **[詳細表示]** リンクがあります。 このリンクにより、スキャンの分析情報エクスペリエンス内の **[スキャンの状態]** ページが開きます。 ここで、スキャン名と過去 30 日間にそれが成功、失敗、またはキャンセルされた回数を確認できます。

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="時間の経過によるスキャンの状態の表示":::

4. 特定のスキャンをさらに調査するには、**スキャン名** をクリックします。これにより、Azure Purview の **ソース** エクスペリエンス内のスキャン履歴に接続されます。 実行履歴ページからは、エラーのさらなる調査に役立つ実行 ID を取得できます。

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="スキャンの詳細の表示":::

5. 最後に、スキャンの分析情報の **[スキャンの状態]** ページに戻るには、実行履歴ページの左上隅にある階層リンクに従います。

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="スキャン履歴の表示"::: 

## <a name="next-steps"></a>次のステップ

* [データの分析情報](./concept-insights.md)を含む Azure Purview の **分析情報** ついてさらに学習します

* [データ ソースの管理](./manage-data-sources.md)を含む Azure Purview の **ソース** エクスペリエンスついてさらに学習します