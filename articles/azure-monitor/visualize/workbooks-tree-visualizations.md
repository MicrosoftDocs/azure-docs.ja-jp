---
title: Azure Monitor ブックのツリーの視覚化
description: Azure Monitor ブックのツリーのすべての視覚化について説明します。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100604351"
---
# <a name="tree-visualizations"></a>ツリーの視覚化

ブックでは、ツリーグリッドを使用した階層ビューがサポートされます。 ツリーを使用すると、ドリルダウン エクスペリエンスのためにいくつかの行を次のレベルに展開できます。

次の例は、ツリー グリッドとして視覚化されたコンテナーの正常性メトリック (ワーキング セット サイズ) を示しています。 この場合の最上位ノードは Azure Kubernetes Service (AKS) ノードで、次のレベルはポッド、最終レベルはコンテナーです。 グリッド (ヒートマップ、アイコン、リンク) の場合と同様に、依然として列の書式を設定できることに注意してください。 この場合の基になるデータ ソースは、AKS ログを含む Log Analytics ワークスペースです。

[![タイルの概要ビューのスクリーンショット](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>ツリー グリッドを追加する
1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[追加]** 、 *[クエリの追加]* の順に選択して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL を入力します
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. 視覚化を **[グリッド]** に設定します
6. **[列の設定]** を選択して、設定ペインを開きます
7. 下部にある **[設定によるツリー/グループ]** セクションで、次のように設定します。
    * ツリーの種類: `Parent/Child`
    * ID フィールド: `Id`
    * 親 ID フィールド: `ParentId`
    * 展開を表示します: `Name`
    * *[ツリーの最上位レベルを展開]* チェック ボックスを選択します。
8. 上部にある _[列]_ セクションで、次のように設定します。
    * _ID_ - 列レンダラー: `Hidden`
    * _親 ID_ - 列レンダラー: `Hidden`
    * _要求_ - 列レンダラー: `Bar`、色: `Blue`、最小値: `0`
9. ペインの下部にある **[保存して閉じる]** ボタンを選択します。

[![上記のクエリと設定を含むタイルの概要ビューのスクリーンショット。](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>ツリー設定

| 設定 | 説明 |
|:------------- |:-------------|
| `Id Field` | グリッド内のすべての行の一意の ID。 |
| `Parent Id Field` | 現在の行の親の ID。 |
| `Show the expander on` | ツリーの展開コントロールを表示する列。 ツリー グリッドでは、その ID フィールドと親 ID フィールドはあまり読みやすくないため、非表示にすることが一般的です。 代わりに、エンティティの名前のように読みやすい値を持つ展開コントロールがフィールドに表示されます。 |
| `Expand the top level of the tree` | オンにすると、ツリー グリッドが最上位レベルに展開されます。 既定でより多くの情報を表示する場合に役立ちます。 |

## <a name="grouping-in-a-grid"></a>グリッドでのグループ化

グループ化を使用すると、上記のような階層ビューを非常に単純なクエリで作成できます。 ツリーの内部ノードでは集計が失われますが、一部のシナリオではこれは許容されます。 基になる結果セットを適切な自由形式 (アラート、正常性、メトリック データなど) に変換できない場合は、*Group By* を使用してツリー ビューを作成します。

## <a name="adding-a-tree-using-grouping"></a>グループ化を使用してツリーを追加する

1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[追加]** 、 *[クエリの追加]* の順に選択して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL を入力します
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. 視覚化を *[グリッド]* に設定します。
2. **[列の設定]** を選択して、設定ペインを開きます。
3. 下部にある *[設定によるツリー/グループ]* セクションで、次のように設定します。
    * ツリーの種類: `Group By`
    * グループ化: `App`
    * 次に優先されるキー: `None`
    * *[ツリーの最上位レベルを展開]* チェック ボックスを選択します。
4. 上部にある *[列]* セクションで、次のように設定します。
    * *アプリ* - 列レンダラー: `Hidden`
    * *要求* - 列レンダラー: `Bar`、色: `Blue`、最小値: `0`
5. ペインの下部にある **[保存して閉じる]** ボタンを選択します。

[![ブックでのツリーの視覚化の作成を示すスクリーンショット](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>次の手順

* [ブックでグラフ](workbooks-graph-visualizations.md)を作成する方法について学習する。
* [ブックでタイル](workbooks-tile-visualizations.md)を作成する方法について学習する。
