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
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100603927"
---
# <a name="chart-visualizations"></a>グラフの視覚化

ブックでは、監視データをチャートとして表示できます。 サポートされているグラフの種類には、折れ線、横棒、カテゴリ別の横棒、面、散布図、円、時間などがあります。 作成者は、グラフの設定を使用して、グラフの高さ、幅、カラー パレット、凡例、タイトル、データなしメッセージなど、および軸の種類や系列の色などをカスタマイズできます。

ブックでは、ログとメトリックの両方のデータ ソースのチャートがサポートされています。

## <a name="log-charts"></a>ログ グラフ

リソースの所有者は、Azure Monitor ログを使用することで、アプリやインフラストラクチャの動作に関する詳細情報を得ることができます。 メトリックとは異なり、ログ情報は既定では収集されず、何らかの種類のコレクションのオンボードが必要です。 ただし、ログを使用すると、診断に役立つリソースとデータの状態に関する多くの情報が提供されます。 ブックを使用すると、ユーザーによる分析のための視覚的なグラフとして、ログ データを表示できます。

### <a name="adding-a-log-chart"></a>ログ チャートの追加

次の例では、過去数日について、アプリへの要求の傾向が示されています。

1. **[編集]** ツール バー項目を選択して、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の [KQL](/azure/kusto/query/) (たとえば、要求の傾向など) を入力します。
5. 視覚化を次のいずれかに設定します:**面**、**横棒**、**横棒 (カテゴリ別)** 、**折れ線**、**円**、**散布図**、**時間**。
6. 必要に応じて他のパラメーターを設定します (時間範囲、視覚化、サイズ、カラー パレット、凡例など)。

[![編集モードでのログ チャートのスクリーンショット](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>ログ チャートのパラメーター

| パラメーター | 説明 | 例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 使用するクエリの種類。 | ログ、Azure Resource Graph など |
| `Resource Type` | ターゲットとするリソースの種類。 | Application Insights、Log Analytics、または Azure ファースト |
| `Resources` | メトリック値の取得元となるリソースのセット。 | MyApp1 |
| `Time Range` | ログ グラフを表示する時間枠。 | 過去 1 時間、過去 24 時間など |
| `Visualization` | 使用する視覚化。 | 面、横棒、折れ線、円、散布図、時間、横棒 (カテゴリ別) |
| `Size` | コントロールの垂直方向のサイズ。 | 小、中、大、または最大 |
| `Color palette` | チャートで使用するカラー パレット。 マルチメトリック モードまたはセグメント化モードでは無視されます。 | 青、緑、赤など |
| `Legend` | 凡例に使用する集計関数。 | 値の合計または平均、または最大、最小、最初、最後の値 |
| `Query` | グラフの視覚化で想定される形式でデータを返す任意の KQL クエリ。 | _requests \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="time-series-charts"></a>時系列グラフ

面グラフ、棒グラフ、折れ線グラフ、散布図、時間グラフなどの時系列グラフは、ブック内のクエリ コントロールを使用して簡単に作成できます。 キーには、結果セット内の時間とメトリックの情報が使用されます。

#### <a name="simple-time-series"></a>単純な時系列

次のクエリでは、*timestamp* と *Requests* の 2 つの列を含むテーブルが返されます。 クエリ コントロールによって、*timestamp* が X 軸に使用され、*Requests* が Y 軸に使用されます。

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![単純な時系列ログ折れ線グラフのスクリーンショット。](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>複数のメトリックを持つ時系列

次のクエリでは、3 つの列 *timestamp*、*Requests*、*Users* を含むテーブルが返されます。 クエリ コントロールにより、*timestamp* が X 軸に使用され、*Requests* と *Users* が Y 軸の異なる系列として使用されます。

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![複数の時系列のメトリック ログ折れ線グラフのスクリーンショット。](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>セグメント化時系列

次のクエリでは、3 つの列 *timestamp*、*Requests*、*RequestName* を含むテーブルが返され、*RequestName* は要求の名前が含まれるカテゴリ列です。 ここでのクエリ コントロールによって、*timestamp* が X 軸に使用され、*RequestName* の値ごとに系列が追加されます。

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![セグメント化された時系列ログ折れ線グラフのスクリーンショット。](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>summarize と make-series

前のセクションの例では、わかりやすいように `summarize` 演算子が使用されています。 しかし、集計には、バケット内に項目がない場合に結果行が省略されるという大きな制限があります。 時間範囲の前または後に空のバケットがあるかどうかにより、グラフの時間枠がシフトするという影響があります。

通常は、空のバケットに対して既定値を指定するオプションがある `make-series` 演算子を使用して、時系列データを作成することをお勧めします。

次のクエリでは、`make-series` 演算子が使用されています。

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

次のクエリでは、`summarize` 演算子を使用した同様のグラフを示します。

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

基になる結果セットが異なる場合でも、 ユーザーが行う必要があるのは視覚化を面、線、棒、または時間に設定することだけであり、後の処理はブックによって行われます。

[![make-series クエリから作成されたログの折れ線グラフのスクリーンショット](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>カテゴリ棒グラフまたはヒストグラム

カテゴリ グラフを使用すると、ユーザーはグラフの X 軸にディメンションまたは列を表すことができ、これはヒストグラムで特に便利です。 次の例では、結果コードによる要求の分布が示されています。

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

このクエリでは次の 2 つの列が返されます: *Requests* メトリックと *Result* カテゴリ。 *Result* 列の各値に対して、"*Requests メトリック*" に比例した高さを持つ固有の棒が表示されます。

[![結果コード別の要求のカテゴリ別棒グラフのスクリーンショット](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>円グラフ

円グラフを使用すると、数値の比率を視覚化できます。 次の例では、結果コードによる要求の割合が示されています。

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

このクエリでは次の 2 つの列が返されます: *Requests* メトリックと *Result* カテゴリ。 *Result* 列の各値に対して、*Requests* メトリックに比例したサイズを持つ固有のスライスが円に表示されます。

[![結果コードを表すスライスを含む円グラフのスクリーンショット](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>メトリック グラフ

ほとんどの Azure リソースでは、状態と正常性に関するメトリック データが生成されます (CPU 使用率、ストレージの可用性、データベース トランザクションの数、失敗したアプリ要求の数など)。 ブックを使用すると、このデータを時系列グラフとして視覚化できます。

### <a name="adding-a-metric-chart"></a>メトリック チャートの追加

次の例では、過去 1 時間におけるストレージ アカウントでのトランザクション数が表示されます。 これを使用して、ストレージ所有者はトランザクションの傾向を確認し、動作の異常を調べることができます。

1. **[編集]** ツール バー項目を選択して、ブックを編集モードに切り替えます。
2. **[メトリックの追加]** リンクを使用して、メトリック コントロールをブックに追加します。
3. リソースの種類 (たとえば、ストレージ アカウント)、ターゲットにするリソース、メトリックの名前空間と名前、および使用する集計を選択します。
4. 必要に応じて他のパラメーターを設定します (時間範囲、分割基準、視覚化、サイズ、カラー パレットなど)。

[![編集モードでのメトリック チャートのスクリーンショット](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>メトリック チャートのパラメーター

| パラメーター | 説明 | 例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | ターゲットとするリソースの種類。 | ストレージまたは仮想マシン。 |
| `Resources` | メトリック値の取得元となるリソースのセット。 | MyStorage1 |
| `Namespace` | メトリックが含まれる名前空間。 | [ストレージ] > [BLOB] |
| `Metric` | 視覚化するメトリック。 | [ストレージ] > [BLOB] > [トランザクション] |
| `Aggregation` | メトリックに適用する集計関数。 | 合計、カウント、平均など |
| `Time Range` | メトリックを表示する時間枠。 | 過去 1 時間、過去 24 時間など |
| `Visualization` | 使用する視覚化。 | 面、横棒、折れ線、散布図、グリッド |
| `Split By` | 必要に応じて、ディメンションのメトリックを分割します。 | Geo の種類別のトランザクション |
| `Size` | コントロールの垂直方向のサイズ。 | 小、中、大 |
| `Color palette` | チャートで使用するカラー パレット。 `Split by` パラメーターが使用されている場合は無視されます。 | 青、緑、赤など |

### <a name="examples"></a>使用例

折れ線グラフとして API 名によって分割されたトランザクション:

[![API 名によって分割されたストレージ トランザクションのメトリック折れ線グラフのスクリーンショット](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

大きな棒グラフとして応答の種類別に分割されたトランザクション:

[![応答の種類によって分割されたストレージ トランザクションの大きいメトリック棒グラフのスクリーンショット](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

散布図としての平均待機時間:

[![ストレージ待機時間のメトリック散布図のスクリーンショット](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>グラフの設定

作成者はグラフの設定を使用して、グラフの軸で使用されるフィールド、軸の単位、カスタムの書式設定、範囲、グループ化の動作、凡例、系列の色をカスタマイズできます。

### <a name="the-settings-tab"></a>[設定] タブ

[設定] タブでは以下のものが制御されます。

- 軸の設定。フィールド、ユーザーが軸の値とカスタム範囲に対して数値の書式を設定できるようにするカスタム書式設定など。
- グループ化の設定。フィールド、"その他" グループが作成される前の限界など。
- 凡例の設定。下端へのメトリックの表示 (系列名、色、数値)、凡例 (系列名、色) など。

![グラフの設定のスクリーンショット。](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>カスタム書式設定

数値の書式設定のオプションは次のとおりです。

| 書式設定のオプション             | 説明                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | 列の単位 - パーセンテージ、カウント、時間、バイト、カウント/時間、バイト/時間などのさまざまなオプション。たとえば、値 1234 の単位をミリ秒に設定でき、1.234s としてレンダリングされます。                                  |
| `Style`                      | レンダリングする形式 - 10 進、通貨、パーセント。                                               |
| `Show group separator`       | グループの区切り記号を表示するチェック ボックス。 米国では 1234 を 1,234 とレンダリングします。                                    |
| `Minimum integer digits`     | 使用する整数の最小桁数 (既定値は 1)。                                                   |
| `Minimum fractional digits`  | 使用する小数部の最小桁数 (既定値は 0)。                                                |
| `Maximum fractional digits`  | 使用する小数部の最大桁数。                                                            |
| `Minimum significant digits` | 使用する最小有効桁数 (既定値は 1)。                                               |
| `Maximum significant digits` | 使用する最大有効桁数。                                                           |

![X 軸の設定のスクリーンショット。](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>系列タブ

系列設定タブでは、グラフの系列に対して表示されるラベルと色を調整できます。

- [`Series name`] フィールドは、データ内の系列と一致させるために使用され、一致している場合は、表示ラベルと色が表示されます。
- [`Comment`] フィールドは、テンプレートの作成者に便利です。翻訳者はこのコメントを使用して表示ラベルをローカライズできます。

![系列設定のスクリーンショット。](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>次の手順

- [ブックでタイル](workbooks-tile-visualizations.md)を作成する方法について学習する。
- [対話型ブック](workbooks-interactive.md)を作成する方法について学習する。