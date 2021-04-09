---
title: Azure Monitor ブックのグラフの視覚化
description: Azure Monitor ブックのグラフのすべての視覚化について説明します。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100604630"
---
# <a name="graph-visualizations"></a>グラフの視覚化

ブックでは、ログからのデータに基づいて任意のグラフを視覚化し、監視エンティティ間の関係を示すことができます。

次のグラフは、外部コンピューターとの間でのさまざまなポートを介したコンピューターの入出力データ フローを示しています。 種類 (コンピューター、ポート、外部 IP) 別に色分けされており、エッジ サイズは、その間を流れるデータの量に対応します。 基になるデータは、VM 接続をターゲットとする KQL クエリから取得されます。

[![タイルの概要ビューのスクリーンショット](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>グラフを追加する
1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL を入力します。

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. 視覚化を **[グラフ]** に設定します
6. **[グラフの設定]** を選択して、設定ペインを開きます
7. 下部にある "_レイアウト フィールド_" で、次のように設定します。
    * ノード ID: `Id`
    * ソース ID: `SourceId`
    * ターゲット ID: `TargetId`
    * エッジのラベル: `None`
    * エッジのサイズ: `Calls`
    * ノードのサイズ: `None`
    * 色分けの種類: `Categorical`
    * ノードの色フィールド: `Kind`
    * カラー パレット: `Pastel`
8. 上部にある _[Node Format Settings]\(ノード形式の設定\)_ で、次のように設定します。
    * "_Top Content (上部コンテンツ)_" - 列を使用: `Name`、列レンダラー: `Text`
    * "_Center Content (中央コンテンツ)_"- 列を使用: `Calls`、列レンダラー: `Big Number`、カラー パレット: `None`
    * "_Bottom Content (下部コンテンツ)_" - 列を使用: `Kind`、列レンダラー: `Text`
9. ペインの下部にある _[保存して閉じる]_ ボタンを選択します。

[![上記のクエリと設定を含むタイルの概要ビューのスクリーンショット。](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>グラフの設定

| 設定         | 説明                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | グラフ上のノードの一意の ID を提供する列を選択します。 列の値には、文字列または数値を使用できます。 |
| `Source Id`     | グラフ上のエッジのソース ノードの ID を提供する列を選択します。 値は "_ノード ID_" 列の値にマップされている必要があります。 |
| `Target Id`     | グラフ上のエッジのターゲット ノードの ID を提供する列を選択します。 値は "_ノード ID_" 列の値にマップされている必要があります。 |
| `Edge Label`    | グラフにエッジのラベルを提供する列を選択します。                                                            |
| `Edge Size`     | エッジの幅の基となるメトリックを提供する列を選択します。                                |
| `Node Size`     | ノード領域の基になるメトリックを提供する列を選択します。                                 |
| `Coloring Type` | ノードの色分けスキームを選択するために使用します。                                                                            |

## <a name="node-coloring-types"></a>ノードの色分けの種類

| 色分けの種類 | 説明 |
|:------------- |:------------|
| `None`        | すべてのノードが同じ色になります。 |
| `Categorical` | ノードには、結果セット内の列の値またはカテゴリに基づいて色が割り当てられます。 上の例では、結果セットの _種類_ 列に基づいて色分けされています。 サポートされているパレットは、`Default`、`Pastel`、および `Cool tone`です。  |
| `Field Based` | この種類の列には、ノードに使用する特定の RGB 値が用意されています。 柔軟性が最も高くなりますが、通常、設定するためにより多くの作業が必要となります。  |

## <a name="node-format-settings"></a>ノードの形式の設定

グラフの作成者は、ノードの各部分 (上、左、中央、右、および下) に配置するコンテンツを指定できます。 グラフでは、ブックでサポートされている任意のレンダラー (テキスト、大きな数値、スパーク ライン、アイコンなど) を使用できます。

## <a name="field-based-node-coloring"></a>フィールドベースのノードの色分け

1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL を入力します。

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. 視覚化を *[グラフ]* に設定します
6. **[グラフの設定]** を選択して、設定ペインを開きます。
7. 下部にある "*レイアウト フィールド*" で、次のように設定します。
    * ノード ID: `Id`
    * ソース ID: `SourceId`
    * ターゲット ID: `TargetId`
    * エッジのラベル: `None`
    * エッジのサイズ: `Calls`
    * ノードのサイズ: `Node`
    * 色分けの種類: `Field Based`
    * ノードの色フィールド: `Color`
8. 上部にある *[ノード形式の設定]* で、次の内容を入力します。
    * *[上部のコンテンツ]* で、次のように設定します。
        * 使用する列: `Name`。
        * 列レンダラー: `Text`。
    * *[中央のコンテンツ]* で、次のように設定します。
        * 使用する列: `Calls`
        * 列レンダラー: `Big Number`
        * カラー パレット: `None`
    * *[下部のコンテンツ]* で、次のように設定します。
        * 使用する列: `Kind`
        * 列レンダラー: `Text`。
9. ペインの下部にある **[保存して閉じる]** を選択します。

[![フィールド ベースのノードの色分けによるグラフの視覚化の作成を示すスクリーンショット。](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>次の手順

* グラフでは、複合棒グラフのレンダラーもサポートされています。 詳細については、[複合棒グラフのドキュメント](workbooks-composite-bar.md)を参照してください。
* ブックで使用できる[データ ソース](workbooks-data-sources.md)の詳細を確認してください。
