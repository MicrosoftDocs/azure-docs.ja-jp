---
title: Azure Monitor ブックの蜂の巣の視覚化
description: Azure Monitor ブックの蜂の巣の視覚化について説明します。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: c91912f711b912c54c1673f2f92e998b4d9ea9db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736935"
---
# <a name="honey-comb-visualizations"></a>蜂の巣の視覚化

蜂の巣を使用すると、必要に応じてクラスターとしてグループ化できるメトリックまたはカテゴリの高密度ビューを表示できます。 これらは、ホットスポットを視覚的に特定し、さらに詳しく調べる際に役立ちます。

下の画像は、2 つのサブスクリプションにまたがる仮想マシンの CPU 使用率を示しています。 各セルは仮想マシンを表し、色とラベルはその平均 CPU 使用率を表します (赤はホットなマシンです)。 仮想マシンはサブスクリプションによってクラスター化されます。

[![2 つのサブスクリプションにまたがる仮想マシンの CPU 使用率を示すスクリーンショット](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>蜂の巣の追加

1. [編集] ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. 下部にある **[追加]** を使用し、 *[クエリの追加]* を使用して、ログ クエリ コントロールをブックに追加します。
3. *[データ ソース]* として [ログ] を選択し、 *[リソースの種類]* として [Log Analytics] を選択し、 *[リソース]* については、仮想マシンのパフォーマンス ログがあるワークスペースを指定します。
4. クエリ エディターを使用して、分析用の KQL を入力します。

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. クエリを実行します。
6. *視覚化* を [グラフ] に設定します。
7. **[グラフの設定]** を選択します。
    1. 下部にある "*レイアウト フィールド*" で、次のように設定します。
        1. グラフの種類:**Hive クラスター**。
        2. ノード ID: `Id`。
        3. グループ化: `None`。
        4. ノード サイズ: 100。
        5. 六角形間の余白: `5`。
        6. 色の種類:**ヒートマップ**。
        7. ノードの色フィールド: `CouterValue`。
        8. カラー パレット: `Red to Green`。
        9. 最小値: `100`。
        10. 最大値: `2000`。
    2. 上部にある *[Node Format Settings]\(ノード形式の設定\)* で、次のように設定します。
        1. コンテンツの上揃え:
            1. 使用する列: `Computer`。
            2. 列レンダラー: `Text`。
        9. コンテンツの中央揃え:
            1. 使用する列: `CounterValue`。
            2. 列レンダラー: `Big Number`。
            3. カラー パレット: `None`。
            4. *[カスタムの数値の書式設定]* ボックスをオンにします。
            5. 単位: `Megabytes`。
            6. 小数点以下の最大桁数: `1`。
8. ペインの下部にある **[保存して閉じる]** ボタンを選択します。

[![クエリ制御、グラフ設定、および上記のクエリと設定を使用した蜂の巣のスクリーンショット](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>蜂の巣のレイアウト設定

| 設定 | 説明 |
|:------------- |:-------------|
| `Node Id` | ノードの一意の ID を提供する列を選択します。 列の値には、文字列または数値を使用できます。 |
| `Group By Field` | ノードをクラスター化する列を選択します。 |
| `Node Size` | 六角形のセルのサイズを設定します。 `Margin between hexagons` プロパティと共に使用して、蜂の巣グラフの外観をカスタマイズします。 |
| `Margin between hexagons` | 六角形のセル間の間隔を設定します。 `Node size` プロパティと共に使用して、蜂の巣グラフの外観をカスタマイズします。 |
| `Coloring type` | ノードの色設定に使用するスキームを選択します。 |
| `Node Color Field` | ノード領域の基になるメトリックを提供する列を選択します。 |

## <a name="node-coloring-types"></a>ノードの色分けの種類

| 色分けの種類 | 説明 |
|:------------- |:-------------|
| `None` | すべてのノードが同じ色になります。 |
| `Categorical` | ノードには、結果セット内の列の値またはカテゴリに基づいて色が割り当てられます。 上の例では、結果セットの _種類_ 列に基づいて色分けされています。 サポートされているパレットは、`Default`、`Pastel`、および `Cool tone`です。  |
| `Heatmap` | この種類のセルは、メトリック列とカラー パレットに基づいて色分けされています。 これにより、セル全体にわたるメトリックを簡単に強調表示できます。 |
| `Thresholds` | この種類のセルの色は、しきい値ルールによって設定されます (たとえば、_CPU > 90% => 赤、60% > CPU > 90% => 黄、CPU < 60% => 緑_) |
| `Field Based` | この種類の列には、ノードに使用する特定の RGB 値が用意されています。 柔軟性が最も高くなりますが、通常、設定するためにより多くの作業が必要となります。  |
      
## <a name="node-format-settings"></a>ノードの形式の設定

蜂の巣の作成者は、ノードの各部分 (上、左、中央、右、および下) に配置するコンテンツを指定できます。 作成者は、ブックでサポートされているレンダラー (テキスト、多数、スパーク ライン、アイコンなど) を自由に使用できます。

## <a name="next-steps"></a>次の手順

- [ブックで複合バー レンダラー](workbooks-composite-bar.md)を作成する方法を確認します。
- [Azure Monitor ログ データを Power BI にインポートする](./powerbi.md)方法を確認します。