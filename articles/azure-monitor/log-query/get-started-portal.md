---
title: Azure Monitor Log Analytics の使用を開始する | Microsoft Docs
description: この記事では、Azure portal で Log Analytics を使用してクエリを記述する方法を説明します。
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.openlocfilehash: 2e2d13e6923535a8993a6477cbbfb921f6092d66
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565603"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Azure Monitor で Log Analytics の使用を開始する

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

このチュートリアルでは、Azure portal で Log Analytics を使用して Azure Monitor ログ クエリを記述する方法について説明します。 以下の方法について説明します。

- Log Analytics を使用して単純なクエリを記述する
- データのスキーマの概要
- 結果のフィルター、並べ替え、グループ化
- 時間の範囲を適用する
- グラフを作成する
- クエリの保存と読み込み
- クエリのエクスポートと共有

ログ クエリの記述に関するチュートリアルについては、「[Azure Monitor でログ クエリの使用を開始する](get-started-queries.md)」を参照してください。<br>
ログ クエリの詳細については、[Azure Monitor でのログ クエリの概要](log-query-overview.md)に関するページを参照してください。

## <a name="meet-log-analytics"></a>Log Analytics について
Log Analytics は、Azure Monitor ログ クエリの記述と実行に使用される Web ツールです。 Azure Monitor のメニューで **[ログ]** を選択し、これを開きます。 新しい空のクエリから開始されます。

![ホーム ページ](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>ファイアウォールの要件
Log Analytics を使用するには、ブラウザーが次のアドレスにアクセスできる必要があります。 ブラウザーがファイアウォールを介して Azure Portal にアクセスしている場合は、これらのアドレスへのアクセスを有効にする必要があります。

| Uri | IP | Port |
|:---|:---|:---|
| portal.loganalytics.io | 動的 | 80,443 |
| api.loganalytics.io | 動的 | 80,443 |
| docs.loganalytics.io | 動的 | 80,443 |

## <a name="basic-queries"></a>基本的なクエリ
クエリを使用すると、用語を検索し、傾向を特定し、パターンを分析し、データに基づいて他の多くの分析情報を表示できます。 基本的なクエリから始めてみましょう。

```Kusto
Event | search "error"
```

このクエリは、_Event_ テーブルの任意のプロパティに _error_ という用語が含まれるレコードを検索します。

クエリは、テーブル名または [search](/azure/kusto/query/searchoperator) コマンドから始めることができます。 上の例は、テーブル名 _Event_ で始まり、Event テーブルのすべてのレコードが取得されます。 複数のコマンドは、パイプ (|) 文字で区切ります。そのため、最初のコマンドの出力は次のコマンドの入力になります。 1 つのクエリに任意の数のコマンドを追加できます。

同じクエリは、次のように記述することもできます。

```Kusto
search in (Event) "error"
```

この例では、**search** はスコープが _Event_ テーブルに設定されているため、そのテーブル内のすべてのレコードで _error_ という用語が検索されます。

## <a name="running-a-query"></a>Running a query
クエリを実行するには、 **[実行]** ボタンをクリックするか、**Shift キーを押しながら Enter キー**を押します。 実行されるコードと返されるデータを決定する次の詳細情報を考慮してください。

- 改行:1 つの改行でクエリが読みやすくなります。 複数の改行で、別々のクエリに分割されます。
- カーソル:クエリ内のどこかにカーソルを置いて実行します。 現在のクエリは、空白行が見つかるまでのコードと見なされます。
- 時間の範囲: 既定では _過去 24 時間_ の時間の範囲が設定されています。 別の範囲を使用するには、時刻の選択ツールを使用するか、明示的な時間の範囲フィルターをクエリに追加します。


## <a name="understand-the-schema"></a>スキーマの概要
スキーマは、論理カテゴリで視覚的にグループ化されたテーブルのコレクションです。 カテゴリの一部は、監視ソリューションのカテゴリです。 _LogManagement_ カテゴリには、Windows および Syslog イベント、パフォーマンス データ、エージェントのハートビートなどの一般的なデータが含まれます。

![スキーマ](media/get-started-portal/schema.png)

各テーブルのデータは、列名の横のアイコンで示されるように、さまざまなデータ型の列に整理されます。 たとえば、スクリーンショットに示されている _Event_ テーブルには、テキスト型の _Computer_、数値型の _EventCategory_、日時型の _TimeGenerated_ があります。

## <a name="filter-the-results"></a>結果をフィルター処理する
まず、_Event_ テーブルのすべてを取得します。

```Kusto
Event
```

Log Analytics では、以下によって結果の範囲が自動的に調整されます。

- 時間の範囲:既定で、クエリは過去 24 時間に制限されます。
- 結果の数:結果は最大 10,000 レコードに制限されます。

このクエリはとても汎用的であり、返される結果が多すぎて不便です。 結果をフィルター処理するには、テーブル要素を使用するか、明示的にフィルターをクエリに追加します。 テーブル要素による結果のフィルター処理は、既存の結果セットに適用されますが、クエリ自体に対するフィルターは新しいフィルター済み結果セットを返すため、より正確な結果が得られます。

### <a name="add-a-filter-to-the-query"></a>クエリにフィルターを追加する
各レコードの左には矢印があります。 この矢印をクリックすると、そのレコードの詳細が表示されます。

列名にカーソルを移動すると、[+] アイコンと [-] アイコンが表示されます。 同じ値のレコードのみを返すフィルターを追加するには、[+] 記号をクリックします。 この値を含むレコードを除外するには、[-] をクリックし、 **[実行]** をクリックしてもう一度クエリを実行します。

![クエリにフィルターを追加する](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>テーブル要素をフィルター処理する
次に、重大度が _Error_ のイベントに注目してみましょう。 これは _EventLevelName_ という名前の列で指定されています。 この列を表示するには、右にスクロールする必要があります。

列タイトルの横にある [フィルター] アイコンをクリックし、ポップアップ ウィンドウで [テキスト フィルター] の _[指定の値で始まる]_ を選択し、「_error_」というテキストを指定します。

![filter](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>結果の並べ替えとグループ化
結果は、過去 24 時間で作成された SQL Server のエラー イベントのみを含むように絞り込まれています。 ただし、結果はどのような方法でも並べ替えられていません。 たとえば、_timestamp_ のような特定の列で結果を並べ替えるには、列のタイトルをクリックします。 1 回のクリックで昇順に並べ替えられ、2 回目のクリックで降順に並べ替えられます。

![列の並べ替え](media/get-started-portal/sort-column.png)

結果は、グループで整理することもできます。 列ヘッダーを他の列の上にドラッグするだけで、特定の列で結果をグループ化できます。 サブグループを作成する場合も、他の列を上部のバーにドラッグします。

![グループ](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>表示する列を選択する
多くの場合、結果のテーブルには多数の列が含まれています。 返される列の一部が既定で表示されない場合や、表示されている列の一部を非表示にする場合があります。 表示する列を選択するには、[列] ボタンをクリックします。

![Select columns](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>時間の範囲を選択する
既定では、Log Analytics では _過去 24 時間_ の時間範囲が適用されます。 別の範囲を使用するには、時刻の選択ツールで別の値を選択し、 **[実行]** をクリックします。 事前設定されている値に加えて、 _[カスタムの時間の範囲]_ オプションを使用して、クエリの絶対範囲を選択することができます。

![時刻の選択ツール](media/get-started-portal/time-picker.png)

カスタムの時間の範囲を選択すると、選択した値は UTC で表示されます。これは、ローカルのタイム ゾーンとは異なる場合があります。

クエリに _TimeGenerated_ のフィルターが明示的に含まれている場合は、時刻の選択ツールのタイトルに "_Set in query_" と表示されます。 競合を避けるため、手動の選択は無効になります。


## <a name="charts"></a>グラフ
クエリの結果はテーブルに返されるだけでなく、クエリ結果をビジュアル形式で表示することができます。 次のクエリを例として使用します。

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

既定では、結果はテーブルに表示されます。 _[グラフ]_ をクリックすると、結果がグラフィック ビューで表示されます。

![横棒グラフ](media/get-started-portal/bar-chart.png)

結果は、積み上げ横棒グラフで表示されます。 _[積み上げ縦棒]_ をクリックし、 _[円]_ を選択して結果を別のビューで表示します。

![円グラフ](media/get-started-portal/pie-chart.png)

x 軸や y 軸などのビューのさまざまなプロパティ、またはグループ化と分割の設定は、コントロール バーから手動で変更できます。

レンダリング演算子を使用して、クエリ自体に優先ビューを設定することもできます。

### <a name="smart-diagnostics"></a>スマート診断
時間グラフでデータの急激な増加や変化がある場合、線上に強調表示された点が表示されることがあります。 これは、_Smart Diagnostics_ によって、急激な変化をフィルターで除外するプロパティの組み合わせが特定されたことを示しています。 点をクリックしてフィルターの詳細を取得し、フィルター処理されたバージョンを確認します。 これにより、変化の原因を特定できる場合があります。

![スマート診断](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>[ダッシュボードにピン留めする]
いずれかの共有ダッシュボードにダイアグラムまたはテーブルをピン留めするには、ピン アイコンをクリックします。

![[ダッシュボードにピン留めする]](media/get-started-portal/pin-dashboard.png)

グラフをダッシュボードにピン留めする際は、特定の簡略化が適用されます。

- テーブルの列と行:テーブルをダッシュボードにピン留めするには、列数が 4 つ以下である必要があります。 最初の 7 行のみが表示されます。
- 時間の制限:クエリの対象は自動的に過去 14 日間のものに制限されます。
- ビン数の制限:多数の不連続ビンが表示されているグラフを表示すると、割合の低いビンは _その他_ のビンに自動的にグループ化されます。

## <a name="save-queries"></a>クエリの保存
便利なクエリを作成したら、クエリを保存したり、他のユーザーと共有したりすることができます。 **[保存]** アイコンは上部バーにあります。

クエリ ページ全体または 1 つのクエリを関数として保存することができます。 関数は他のクエリからも参照できるクエリです。 クエリを関数として保存するには、関数のエイリアスを指定する必要があります。このエイリアスは、別のクエリから参照されるときに、このクエリの呼び出しに使用される名前です。

![関数を保存する](media/get-started-portal/save-function.png)

Log Analytics のクエリは、常に選択したワークスペースに保存され、そのワークスペースの他のユーザーと共有されます。

## <a name="load-queries"></a>クエリを読み込む
クエリ エクスプローラー アイコンは、右上の領域にあります。 クリックすると、すべての保存済みクエリがカテゴリ別に一覧表示されます。 また、後で見つけやすいように、特定のクエリを [お気に入り] にマークすることもできます。 保存済みクエリをダブルクリックすると、現在のウィンドウに追加されます。

![クエリ エクスプローラー](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>エクスポートとリンクとしての共有
Log Analytics では、いくつかのエクスポート方法がサポートされています。

- Excel:結果を CSV ファイルとして保存します。
- Power BI:結果を Power BI にエクスポートします。 詳細については、[Azure Monitor ログ データを Power BI にインポートする](../../azure-monitor/platform/powerbi.md)ことに関するページを参照してください。
- リンクの共有:クエリ自体をリンクとして共有できます。そのリンクは、同じワークスペースにアクセスできる他のユーザーが送信および実行することができます。

## <a name="next-steps"></a>次の手順

- [Azure Monitor ログ クエリの記述](get-started-queries.md)についてさらに学習します。
