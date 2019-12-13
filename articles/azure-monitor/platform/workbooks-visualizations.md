---
title: Azure Monitor ブックの視覚化
description: テキスト、チャート、グリッド、ツリー、グラフなど、Azure Monitor ブックのすべての視覚化コンポーネントについて説明します。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: bcc60c0f934111f779e3fdedc399881acb16f208
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872793"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor ブックの視覚化

Azure Monitor のブックでは、レポートのニーズに合わせてさまざまなスタイルの視覚化がサポートされています。 この記事では、各種類の視覚化の例を示します。

## <a name="text"></a>Text

ブックでは、作成者が自分のブックにテキスト ブロックを含めることができます。 テキストは、テレメトリ (ユーザーがデータやセクション見出しなどを解釈するのに役立つ情報) の人間による分析にすることができます。

![Apdex のテキストの表のスクリーンショット](./media/workbooks-visualizations/apdex.png)

テキストは、書式を完全に制御できるマークダウン コントロールを通じて追加されます。

![レンダリングされた表を構築する未加工のマークダウンのスクリーンショット](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>テキスト コントロールの追加

1. **[編集]** ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[テキストの追加]** リンクを使用して、テキスト コントロールをブックに追加します。
3. マークダウンをコントロールに追加します。
4. **[編集完了]** をクリックして、書式設定されたテキストを表示します。

> [!TIP]
> さまざまな書式設定オプションについては、こちらの「[マークダウン チートシート](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)」を参照してください。

## <a name="charts"></a>グラフ

ブックでは、監視データをチャートとして表示できます。 サポートされているチャートの種類には、折れ線、横棒、カテゴリ別の横棒、面、散布図、円、時間などがあります。 作成者は、チャートの高さ、幅、カラー パレット、凡例、タイトル、データなしメッセージなどをカスタマイズすることを選択できます。

ブックでは、ログとメトリックの両方のデータ ソースのチャートがサポートされています。 

### <a name="adding-a-log-chart"></a>ログ チャートの追加

1. **[編集]** ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) とターゲットのリソースを選択します。
4. クエリ エディターを使用して、分析用の [KQL](https://docs.microsoft.com/azure/kusto/query/) (たとえば、要求の傾向など) を入力します。
5. 視覚化を次のいずれかに設定します:**面**、**横棒**、**横棒 (カテゴリ別)** 、**折れ線**、**円**、**散布図**、**時間**。
6. 必要に応じて他のパラメーターを設定します (時間範囲、視覚化、サイズ、カラー パレット、凡例など)。

![編集モードでのログ チャートのスクリーンショット](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>ログ チャートのパラメーター

| パラメーター | 説明 | 例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 使用するクエリの種類 | ログ、Azure Resource Graph など |
| `Resource Type` | ターゲットとするリソースの種類 | Application Insights、Log Analytics、または Azure ファースト |
| `Resources` | メトリック値の取得元となるリソースのセット | MyApp1 |
| `Time Range` | ログ チャートを表示する時間枠 | 過去 1 時間、過去 24 時間など |
| `Visualization` | 使用する視覚化 | 面、横棒、折れ線、円、散布図、時間、横棒 (カテゴリ別) |
| `Size` | コントロールの垂直方向のサイズ | 小、中、大、または最大 |
| `Color palette` | チャートで使用するカラー パレット。 マルチメトリック モードまたはセグメント化モードでは無視されます。 | 青、緑、赤など |
| `Legend` | 凡例に使用する集計関数 | 値の合計または平均、または最大、最小、最初、最後の値 |
| `Query` | チャートの視覚化で想定される形式でデータを返す任意の KQL クエリ | _requests \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="adding-a-metric-chart"></a>メトリック チャートの追加

1. **[編集]** ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[メトリックの追加]** リンクを使用して、メトリック コントロールをブックに追加します。
3. リソースの種類 (たとえば、ストレージ アカウント)、ターゲットにするリソース、メトリックの名前空間と名前、および使用する集計を選択します。
4. 必要に応じて他のパラメーターを設定します (時間範囲、分割基準、視覚化、サイズ、カラー パレットなど)。

![編集モードでのメトリック チャートのスクリーンショット](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>メトリック チャートのパラメーター

| パラメーター | 説明 | 例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | ターゲットとするリソースの種類 | ストレージまたは仮想マシン。 |
| `Resources` | メトリック値の取得元となるリソースのセット | MyStorage1 |
| `Namespace` | メトリックがある名前空間 | [ストレージ] > [BLOB] |
| `Metric` | 視覚化するメトリック | [ストレージ] > [BLOB] > [トランザクション] |
| `Aggregation` | メトリックに適用する集計関数 | 合計、カウント、平均など |
| `Time Range` | メトリックを表示する時間枠 | 過去 1 時間、過去 24 時間など |
| `Visualization` | 使用する視覚化 | 面、横棒、折れ線、散布図、グリッド |
| `Split By` | 必要に応じて、ディメンションのメトリックを分割します | Geo の種類別のトランザクション |
| `Size` | コントロールの垂直方向のサイズ | 小、中、大 |
| `Color palette` | チャートで使用するカラー パレット。 `Split by` パラメーターが使用されている場合は無視されます | 青、緑、赤など |

## <a name="grids"></a>グリッド

グリッドまたはテーブルは、ユーザーにデータを表示する一般的な方法です。 ブックを使用すると、ユーザーはグリッドの列のスタイルを個別に設定して、自分のレポートに豊富な UI を提供できます。

次の例は、アイコン、ヒートマップ、およびスパークバーを組み合わせて複雑な情報を表示するグリッドを示しています。 このブックには、並べ替え、検索ボックス、および [分析に移動] ボタンも用意されています。

![ログ ベース グリッドのスクリーンショット](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>ログベース グリッドの追加

1. **[編集]** ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) とターゲットのリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL (たとえば、メモリがしきい値を下回った VM など) を入力します。
5. 視覚化を **[グリッド]** に設定します
6. 必要に応じて他のパラメーターを設定します (時間範囲、サイズ、カラー パレット、凡例など)。

![ログ ベース グリッド クエリのスクリーンショット](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>タイル

タイルは、ブックに概要データを表示するためのとても便利な方法です。 次の図は、タイルの一般的なユース ケースを示しています。詳細グリッドの上部にアプリ レベルの概要があります。

![タイルの概要ビューのスクリーンショット](./media/workbooks-visualizations/tiles-summary.png)

ブックのタイルでは、タイトル、サブタイトル、大きなテキスト、アイコン、メトリック ベースのグラデーション、スパーク ライン/バー、フッターなどの表示がサポートされています。

### <a name="adding-a-tile"></a>タイルの追加

1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。 
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL を入力します
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. サイズを **[Full]\(最大\)** に設定します
6. 視覚化を **[タイル]** に設定します
7. **[タイルの設定]** をクリックして、設定ペインを開きます
8. **[タイルのフィールド]** で、次のように設定します。
    * タイトル: `name`
    * 左: `Requests`、レンダラー: `Big Number`、カラー パレット: `Green to Red`、最小値: `0`
    * 下: `appName`
9. ペインの下部にある **[保存して閉じる]** をクリックします。

![タイルの概要ビューのスクリーンショット](./media/workbooks-visualizations/tile-settings.png)

これは、読み取りモードでのタイルの外観を示しています。

![タイルの概要ビューのスクリーンショット](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>ツリー

ブックでは、ツリーグリッドを使用した階層ビューがサポートされます。 ツリーを使用すると、ドリルダウン エクスペリエンスのためにいくつかの行を次のレベルに展開できます。

次の例は、ツリー グリッドとして視覚化されたコンテナーの正常性メトリック (ワーキング セット サイズ) を示しています。 この場合の最上位ノードは Azure Kubernetes Service (AKS) ノードで、次のレベルはポッド、最終レベルはコンテナーです。 グリッド (ヒートマップ、アイコン、リンク) の場合と同様に、依然として列の書式を設定できることに注意してください。 この場合の基になるデータ ソースは、AKS ログを含む Log Analytics ワークスペースです。

![タイルの概要ビューのスクリーンショット](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>ツリー グリッドの追加
1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。 
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
6. **[列の設定]** をクリックして、設定ペインを開きます
7. 下部にある **[設定によるツリー/グループ]** セクションで、次のように設定します。
    * ツリーの種類: `Parent/Child`
    * ID フィールド: `Id`
    * 親 ID フィールド: `ParentId`
    * 展開を表示します: `Name`
    * ツリーの最上位レベルを展開します: `checked`
8. 上部にある _[列]_ セクションで、次のように設定します。
    * _ID_ - 列レンダラー: `Hidden`
    * _親 ID_ - 列レンダラー: `Hidden`
    * _要求_ - 列レンダラー: `Bar`、色: `Blue`、最小値: `0`
9. ペインの下部にある _[保存して閉じる]_ をクリックします。    

![タイルの概要ビューのスクリーンショット](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>ツリー設定

| Setting | 説明 |
|:------------- |:-------------|
| `Id Field` | グリッド内のすべての行の一意 ID |
| `Parent Id Field` | 現在の行の親の ID |
| `Show the expander on` | ツリーの展開コントロールを表示する列。 ツリー グリッドでは、その ID フィールドと親 ID フィールドはあまり読みやすくないため、非表示にすることが一般的です。 代わりに、エンティティの名前のように読みやすい値を持つ展開コントロールがフィールドに表示されます |
| `Expand the top level of the tree` | オンにすると、ツリー グリッドが最上位レベルに展開されます。 既定でより多くの情報を表示する場合に役立ちます |

## <a name="graphs"></a>グラフ

ブックでは、ログからのデータに基づいて任意のグラフを視覚化し、監視エンティティ間の関係を示すことができます。

次のグラフは、外部コンピューターとの間でのさまざまなポートを介したコンピューターの入出力データ フローを示しています。 種類 (コンピューター、ポート、外部 IP) 別に色分けされており、エッジ サイズは、その間を流れるデータの量に対応します。 基になるデータは、VM 接続をターゲットとする KQL クエリから取得されます。

![タイルの概要ビューのスクリーンショット](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>グラフの追加
1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。 
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL を入力します
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
7. 視覚化を **[グラフ]** に設定します
8. **[グラフの設定]** をクリックして、設定ペインを開きます
9. 下部にある "_レイアウト フィールド_" で、次のように設定します。
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. 上部にある _[Node Format Settings]\(ノード形式の設定\)_ で、次のように設定します。
    * "_Top Content (上部コンテンツ)_ " - 列を使用: `Name`、列レンダラー: `Text`
    * "_Center Content (中央コンテンツ)_ "- 列を使用: `Calls`、列レンダラー: `Big Number`、カラー パレット: `None`
    * "_Bottom Content (下部コンテンツ)_ " - 列を使用: `Kind`、列レンダラー: `Text`
10. ペインの下部にある _[保存して閉じる]_ をクリックします。

![タイルの概要ビューのスクリーンショット](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>次の手順

* Azure Resource Manager を使用してブックを[デプロイ](workbooks-automate.md)します。
* ブック リソースへのアクセスを[制御](workbooks-access-control.md)し、共有します。