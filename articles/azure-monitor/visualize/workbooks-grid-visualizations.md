---
title: Azure Monitor ブックのグリッドの視覚化
description: Azure Monitor ブックのグリッドのすべての視覚化について説明します。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100600986"
---
# <a name="grid-visualizations"></a>グリッドの視覚化

グリッドまたはテーブルは、ユーザーにデータを表示する一般的な方法です。 ブックを使用すると、ユーザーはグリッドの列のスタイルを個別に設定して、自分のレポートに豊富な UI を提供できます。

次の例は、アイコン、ヒートマップ、およびスパークバーを組み合わせて複雑な情報を表示するグリッドを示しています。 このブックには、並べ替え、検索ボックス、および [分析に移動] ボタンも用意されています。

[![ログ ベース グリッドのスクリーンショット](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>ログベース グリッドの追加

1. **[編集]** ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[クエリの追加]** リンクを使用して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL (たとえば、メモリがしきい値を下回った VM など) を入力します。
5. 視覚化を **[グリッド]** に設定します
6. 必要に応じて他のパラメーターを設定します (時間範囲、サイズ、カラー パレット、凡例など)。

[![ログ ベース グリッド クエリのスクリーンショット](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>ログ チャートのパラメーター

| パラメーター | 説明 | 例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 使用するクエリの種類。 | ログ、Azure Resource Graph など |
| `Resource Type` | ターゲットとするリソースの種類。 | Application Insights、Log Analytics、または Azure ファースト |
| `Resources` | メトリック値の取得元となるリソースのセット。 | MyApp1 |
| `Time Range` | ログ グラフを表示する時間枠。 | 過去 1 時間、過去 24 時間など |
| `Visualization` | 使用する視覚化。 | グリッド |
| `Size` | コントロールの垂直方向のサイズ。 | 小、中、大、または最大 |
| `Query` | グラフの視覚化で想定される形式でデータを返す任意の KQL クエリ。 | _requests \| summarize Requests = count() by name_ |

## <a name="simple-grid"></a>単純なグリッド

ブックを使用すると、KQL の結果を単純なテーブルとしてレンダリングできます。 次のグリッドは、アプリ内の要求数と要求の種類ごとの一意のユーザー数を示しています。

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![編集モードでのログ ベース グリッドのスクリーンショット](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>グリッドのスタイル設定

単純なテーブルでは、データが表示されても、読みにくく、必ずしも分析情報が得られるとは限りません。 グリッドのスタイル設定を行うと、データの読み取りと解釈が容易になります。

次に示すのは、前のセクションと同じグリッドを、ヒートマップとしてスタイル設定したものです。

[![列をヒートマップとしてスタイル設定したログ ベース グリッドのスクリーンショット](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

次に示すのは、同じグリッドを棒グラフとしてスタイル設定したものです。[![列を棒グラフとしてスタイル設定したログ ベース グリッドのスクリーンショット](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>グリッド列のスタイル設定

1. クエリ コントロール ツールバーの **[Column Setting]\(列設定\)** ボタンを選択します。
2. *[列の設定の編集]* で、スタイル設定する列を選択します。
3. 列レンダラー (たとえば、ヒートマップ、棒グラフ、下の棒グラフなど) および関連する設定を選択して、列のスタイルを設定します。

次に示すのは、*Request* 列を棒グラフとしてスタイル設定する例です。

[![Request 列を棒グラフとしてスタイル設定したログ ベース グリッドのスクリーンショット。](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>列レンダラー

| 列レンダラー | 説明 | 追加オプション |
|:------------- |:-------------|:-------------|
| `Automatic` | 既定値 - 列の種類に基づいて最も適切なレンダラーを使用します。  |  |
| `Text` | 列の値をテキストとしてレンダリングします。 | |
| `Right Aligned` | テキストと同じですが、右揃えになっている点が異なります。 | |
| `Date/Time` | 読み取り可能な日付と時刻の文字列をレンダリングします。 | |
| `Heatmap` | セルの値に基づいてグリッドのセルの色を指定します。 | スケーリングに使用されるカラー パレットと最小/最大値。 |
| `Bar` | セルの値に基づいて、セルの横に棒グラフをレンダリングします。 | スケーリングに使用されるカラー パレットと最小/最大値。 |
| `Bar underneath` | セルの値に基づいて、セルの下部付近に棒グラフをレンダリングします。 | スケーリングに使用されるカラー パレットと最小/最大値。 |
| `Composite bar` | その行の指定された列を使用して、複合棒グラフをレンダリングします。 詳細については、[複合棒グラフ](workbooks-composite-bar.md)に関するページを参照してください。 | 棒グラフをレンダリングするための対応する色を持つ列と、棒グラフの上部に表示されるラベル。 |
| `Spark bars` | セル内の動的配列の値に基づいて、セルにスパークバーをレンダリングします。 たとえば、先頭のスクリーンショットの Trend 列です。 | スケーリングに使用されるカラー パレットと最小/最大値。 |
| `Spark lines` | セル内の動的配列の値に基づいて、セルにスパークラインをレンダリングします。 | スケーリングに使用されるカラー パレットと最小/最大値。 |
| `Icon` | セル内のテキスト値に基づいてアイコンをレンダリングします。 サポートされている値は、`cancelled`、`critical`、`disabled`、`error`、`failed`、`info`、`none`、`pending`、`stopped`、`question`、`success`、`unknown`、`warning` `uninitialized`、`resource`、`up`、`down`、`left`、`right`、`trendup`、`trenddown`、`4`、`3`、`2`、`1`、`Sev0`、`Sev1`、`Sev2`、`Sev3`、`Sev4`、`Fired`、`Resolved`、`Available`、`Unavailable`、`Degraded`、`Unknown`、および `Blank` です。|  |
| `Link` | クリックしたときに構成可能なアクションを実行するリンクをレンダリングします。 項目をリンクにする必要がある場合 "*のみ*"、これを使用してください。  `Make this item a link` 設定を使用することで、その他のすべての型 "*も*" リンクにすることができます。 詳細については、以下の「[リンク アクション](#link-actions)」を参照してください。 |  |
| `Location` | リージョン ID に基づいてわかりやすい Azure リージョン名をレンダリングします。 |  |
| `Resource type` | リソースの種類の ID に基づいて、わかりやすいリソースの種類の文字列をレンダリングします  |  |
| `Resource` | リソース ID に基づいて、わかりやすいリソース名およびリンクをレンダリングします  | リソースの種類アイコンを表示するオプション  |
| `Resource group` | リソース グループ ID に基づいて、わかりやすいリソース グループ名およびリンクをレンダリングします。セルの値がリソース グループでない場合、リソース グループに変換されます。  | リソース グループ アイコンを表示するオプション  |
| `Subscription` | サブスクリプション ID に基づいて、わかりやすいサブスクリプション名とリンクをレンダリングします。セルの値がサブスクリプションでない場合、サブスクリプションに変換されます。  | サブスクリプション アイコンを表示するオプション。  |
| `Hidden` | グリッド内の列を非表示にします。 既定のクエリによって、必要以上に多くの列が返されるが、project-away が望ましくない場合に便利です |  |

### <a name="link-actions"></a>リンク アクション

`Link` レンダラーが選択されている場合、または *[Make this item a link]\(この項目をリンクにする\)* チェックボックスが選択されている場合、作成者は、セルを選択したときに実行されるリンク アクションを構成できます。 これは通常、そのセルからのコンテキストを持つ他のビューにユーザーを移動させるか、または URL を開くことがあります。

### <a name="custom-formatting"></a>カスタム書式設定

ブックでは、ユーザーがセル値の数値の書式を設定することもできます。 この操作を行うには、 *[ カスタム書式設定]* チェックボックスをクリックします (利用可能な場合)。

| 書式設定のオプション | 説明 |
|:------------- |:-------------|
| `Units` | 列の単位 - パーセンテージ、カウント、時間、バイト、カウント/時間、バイト/時間などのさまざまなオプション。たとえば、値 1234 の単位をミリ秒に設定でき、1.234s としてレンダリングされます。 |
| `Style` | レンダリングする形式 - 10 進、通貨、パーセント。 |
| `Show group separator` | グループの区切り記号を表示するチェック ボックス。 米国では 1234 を 1,234 とレンダリングします。 |
| `Minimum integer digits` | 使用する整数の最小桁数 (既定値は 1)。 |
| `Minimum fractional digits` | 使用する小数部の最小桁数 (既定値は 0)。  |
| `Maximum fractional digits` | 使用する小数部の最大桁数。 |
| `Minimum significant digits` | 使用する最小有効桁数 (既定値は 1)。 |
| `Maximum significant digits` | 使用する最大有効桁数。 |
| `Custom text for missing values` | データ ポイントに値がない場合は、空白ではなく、このカスタム テキストを表示します。 |

### <a name="custom-date-formatting"></a>カスタムの日付の書式設定

列を日付/時刻レンダラーに設定することを作成者が指定した場合、作成者は *[カスタム日付の書式設定]* チェックボックスを使用して、カスタムの日付の書式設定オプションを指定できます。

| 書式設定のオプション | 説明 |
|:------------- |:-------------|
| `Style` | 日付を短い、長い、完全な形式で表示するか、または時刻を短いまたは長い時間形式でレンダリングするための形式。 |
| `Show time as` | 作成者は、時刻をローカル時刻 (既定値) として表示するか、UTC として表示するかを決定できます。 選択した日付の書式スタイルによっては、UTC/タイムゾーン情報が表示されない場合があります。 |

## <a name="custom-column-width-setting"></a>カスタムの列幅の設定

作成者は、 *[列の設定]* の *[Custom Column Width]\(カスタム列の幅\)* フィールドを使用して、グリッド内の列の幅をカスタマイズできます。

![カスタム列の幅フィールドが赤色のボックスで示されている列の設定のスクリーンショット](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

フィールドが空白のままの場合、列の文字数および表示される列の数に基づいて、幅が自動的に決定されます。 既定の単位は "ch" (文字) です。

ラベルの青いボタン **([Current Width]\(現在の幅\))** を選択すると、選択した列の現在の幅がテキスト フィールドに挿入されます。 測定の単位のない値がカスタム幅フィールドに存在する場合は、既定値が使用されます。

使用可能な測定の単位は次のとおりです。

| 測定の単位 | 定義           |
|:--------------------|:---------------------|
| ch                  | 文字 (既定値) |
| px                  | ピクセル               |
| fr                  | 分数単位     |
| %                   | percentage           |

入力の検証 - 検証が失敗した場合、フィールドの下に赤いガイダンス メッセージがポップアップ表示されますが、ユーザーは幅を適用できます。 入力に値がある場合は、解析されます。有効な測定の単位が見つからない場合は、既定値が使用されます。

最小/最大幅はありません。これは、作成者の裁量に任されています。 非表示の列については、カスタム列の幅フィールドは無効です。

## <a name="examples"></a>使用例

### <a name="spark-lines-and-bar-underneath"></a>スパーク ラインと下の棒グラフ

次の例では、要求の数と要求名ごとのその傾向を示しています。

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![下の棒グラフとスパーク ラインが表示されているログ ベース グリッドのスクリーンショット](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>共有スケールおよびカスタム書式設定を使用したヒートマップ

この例では、さまざまな要求の持続時間のメトリックとその数を示します。 ヒートマップ レンダラーは、設定に指定されている最小値を使用するか、列の最小値と最大値を計算し、列の最小値と最大値を基準としたセルの値に基づいて、セルに対して選択したパレットから背景色を割り当てます。

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![列をまたいだ共有スケールを持つヒートマップがあるログ ベース グリッドのスクリーンショット](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

上の例では、共有パレット (緑または赤) とスケールを使用して、列 (Mean、Median、p80、p95、および p99) の色が設定されています。 Requests 列には別のパレット (青) が使用されています。

#### <a name="shared-scale"></a>共有スケール

共有スケールを取得するには、以下のようにします。

1. 正規表現を使用して、設定を適用する複数の列を選択します。 たとえば、列名を `Mean|Median|p80|p95|p99` に設定して、それらすべてを選択します。
2. 個々の列の既定の設定を削除します。

これにより、新しい複数列の設定で、その設定が適用されて共有スケールが含まれるようになります。

[![列をまたいだ共有スケールを取得するログ ベース グリッド設定のスクリーンショット](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>状態を表すアイコン

次の例では、p95 の持続時間に基づく要求のカスタム状態を示します。

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![上記のクエリを使用した、列をまたいだ共有スケールを持つヒートマップがあるログ ベース グリッドのスクリーンショット。](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

サポートされているアイコン名は、`cancelled`、`critical`、`disabled`、`error`、`failed`、`info`、`none`、`pending`、`stopped`、`question`、`success`、`unknown`、`warning`、`uninitialized`、`resource`、`up`、`down`、`left`、`right`、`trendup`、`trenddown`、`4`、`3`、`2`、`1`、`Sev0`、`Sev1`、`Sev2`、`Sev3`、`Sev4`、`Fired`、`Resolved`、`Available`、`Unavailable`、`Degraded`、`Unknown`、および `Blank` です。

### <a name="using-thresholds-with-links"></a>しきい値とリンクの使用

次の手順では、しきい値とリンクを使用して、アイコンを割り当てたり別のブックを開いたりする方法について説明します。 グリッド内の各リンクからは、その Application Insights リソースについての別のブック テンプレートが開きます。

1. *[編集]* ツール バー項目を選択して、ブックを編集モードに切り替えます。
2. **[追加]** 、 *[クエリの追加]* の順に選択します。
3. *[データ ソース]* を [JSON] に、 *[視覚化]* を [グリッド] に変更します。
4. 次のクエリを入力します。

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. クエリを実行します。
6. **[列の設定]** を選択して設定を開きます。
7. *[列]* で [名前] を選択します。
8. *[列レンダラー]* で、[しきい値] を選択します。
9.  次の *[しきい値の設定]* を入力して選択します。
   
    | 演算子 | 値   | アイコン   |
    |----------|---------|---------|
    | ==       | warning | 警告 |
    | ==       | error   | 失敗  |

    ![上記の設定を含む [列の設定の編集] タブのスクリーンショット。](./media/workbooks-grid-visualizations/column-settings.png)

    既定の行はそのままにします。 任意のテキストを入力できます。 [Text]\(テキスト\) 列は、入力として文字列形式を取り、列の値と単位 (指定されている場合) を設定します。 たとえば、列の値が "warning" で、テキストが "{0} {1} link!" である場合、"warning link!" と表示されます。
10. *[Make this item a link]\(この項目をリンクにする\)* ボックスを選択します。
    1. *[開くビュー]* で、[ブック (テンプレート)] を選択します。
    2. *[Link value comes from]\(リンク値の取得元\)* で、[リンク] を選択します。
    3. *[コンテキスト ブレードでリンクを開く]* ボックスを選択します。
    4. *[ブック リンク設定]* で、次の設定を選択します
        1. *[テンプレート ID の取得元]* で、[列] を選択します。
        2. *[列]* で、[リンク] を選択します。

    ![上記の設定を含むリンク設定のスクリーンショット。](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. *[列]* から [リンク] を選択します。 *[列レンダラー]* の横にある設定で、 **[(列の非表示)]** を選択します。
1. [名前] 列の表示名を変更するには、 **[ラベル]** タブを選択します。[名前] がその "*列 ID*" である行で、*[列ラベル] に、表示する名前を入力します。
2. **[適用]** を選択します

![上記の設定を使用した [thresholds in grid]\(グリッド内のしきい値\) のスクリーンショット](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>分数単位の割合

分数単位 (fr) は、さまざまな種類のグリッドで一般的に使用される動的な測定単位です。 ウィンドウのサイズや解像度が変化すると、fr の幅も変わります。

次のスクリーンショットは、それぞれの幅が 1fr で幅がすべて同じである 8 つの列を含むテーブルを示しています。 ウィンドウのサイズが変わると、各列の幅も比例して変化します。

[![それぞれの幅の値が 1fr であるグリッド内の列のスクリーンショット](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

次の図は、最初の列が 50% の幅に設定されていることを除いて、同じテーブルを示しています。 これにより、この列はグリッドの合計幅の半分に動的に設定されます。 ウィンドウのサイズを変更しても、ウィンドウのサイズが小さくなりすぎない限り、50% の幅が維持されます。 これらの動的列は、それらの内容に基づいて、最小の幅が設定されます。 グリッドの残りの 50% は、合計 8 個の分数単位によって分割されます。 下の "kind" 列は 2fr に設定されているため、残りの領域の 4 分の 1 を占めます。 他の列はそれぞれ 1fr であるため、グリッドの右半分の 8 分の 1 を占めます。

[![1 つの列幅の値が 50% で、残りがそれぞれ 1fr に設定されているグリッド内の列のスクリーンショット](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

fr、%、px、および ch の各幅を組み合わせることは可能で、前の例と同じように動作します。 静的単位 (ch および px) によって設定される幅は、ウィンドウ/解像度が変更された場合でも変化しないハード定数です。 % によって設定された列は、グリッドの合計幅に基づいて自身のパーセンテージを取ります (以前の最小幅が原因で正確ではない場合があります)。 fr で設定された列は、割り当てられた分数単位の数に基づいて、残りのグリッド領域を単純に分割します。

[![異なる幅の単位が組み合わせて使用されているグリッド内の列のスクリーンショット](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>次の手順

* [ブックでツリー](workbooks-tree-visualizations.md)を作成する方法について学習します。
* [ブックのテキストパラメーター](workbooks-text.md)を作成する方法について学習します。