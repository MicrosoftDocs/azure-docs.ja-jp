---
title: チュートリアル:Log Analytics クエリの使用方法
description: このチュートリアルでは、Azure portal で Log Analytics を使用して Azure Monitor ログ クエリを記述および管理する方法について説明します。
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 8917c73474b81ee76e6acdf8c6f274e962ee1215
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874233"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>チュートリアル:Log Analytics クエリの使用方法

このチュートリアルでは、Azure portal で Log Analytics を使用して Azure Monitor ログ クエリを記述、実行、管理する方法について説明します。 Log Analytics クエリを使用すると、語句の検索、傾向の特定、パターンの分析を行って、データから他の多くの分析情報を入手できます。 

このチュートリアルでは、次の目的で Log Analytics を使用する方法について学習します。

> [!div class="checklist"]
> * ログ データのスキーマの確認
> * 単純なクエリの記述と実行、クエリの時間範囲の変更
> * クエリ結果のフィルター処理、並べ替え、グループ化
> * クエリ結果のビジュアルの表示、変更、共有
> * クエリと結果の保存、読み込み、エクスポート、コピー

ログ クエリについて詳しくは、「[Azure Monitor のログ クエリの概要](log-query-overview.md)」をご覧ください。<br/>
ログ クエリの記述に関する詳細なチュートリアルについては、「[Azure Monitor でログ クエリの使用を開始する](get-started-queries.md)」を参照してください。

## <a name="open-log-analytics"></a>Log Analytics を開く
Log Analytics を使用するには、Azure アカウントにサインインする必要があります。 Azure アカウントがない場合は、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

このチュートリアルの大部分の手順は、サンプル データが豊富に含まれる[こちらのデモ環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)を使用して完了できます。 デモ環境では、クエリの保存およびダッシュボードへの結果のピン留めは実行できません。

Azure Monitor を使用して 1 つ以上の Azure リソースに関するログ データを収集している場合は、ご自分の環境を使用してもかまいません。 Log Analytics ワークスペースを開くには、Azure Monitor の左側のナビゲーションで **[ログ]** を選択します。 

## <a name="understand-the-schema"></a>スキーマの概要
 
"*スキーマ*" は、論理カテゴリでグループ分けされたテーブルのコレクションです。 デモ スキーマには、各種監視ソリューションのカテゴリが複数含まれています。 たとえば、**LogManagement** カテゴリには、Windows および Syslog イベント、パフォーマンス データ、エージェントのハートビートが含まれます。

Log Analytics ワークスペースの **[テーブル]** タブには、スキーマ テーブルが表示されます。 このテーブルには複数の列が含まれ、各列では列名の隣にアイコンによりデータ型が示されます。 たとえば、**Event** テーブルには、**Computer** などのテキスト列と **EventCategory** などの数値列が含まれています。

![スキーマ](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>基本的なクエリの記述と実行

Log Analytics を開くと、**クエリ エディター**に空白の新規クエリが表示されます。

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>クエリを記述する

Azure Monitor ログ クエリでは、あるバージョンの Kusto クエリ言語を使用します。 クエリの先頭には、テーブル名または [search](/azure/kusto/query/searchoperator) コマンドを指定します。 

次のクエリでは、**Event** テーブルのすべてのレコードを取得します。

```Kusto
Event
```

パイプ (|) 文字で複数のコマンドを区切ると、最初のコマンドの出力が次のコマンドの入力になります。 1 つのクエリに任意の数のコマンドを追加できます。 次のクエリでは、**Event** テーブルのすべてのレコードを取得し、その中から、すべてのプロパティを対象として **error** という語句を検索します。

```Kusto
Event 
| search "error"
```

改行を 1 つ入力すると、クエリが読みやすくなります。 改行を複数入力すると、クエリが個別のクエリに分割されます。

同じクエリを次のように記述することもできます。

```Kusto
search in (Event) "error"
```

2 つ目の例では、**search** コマンドにより、**Events** テーブルのレコードのみを対象として **error** という語句を検索しています。

既定では、Log Analytics のクエリの時間範囲は過去 24 時間に制限されています。 異なる時間範囲を設定するには、クエリに **TimeGenerated** フィルターを明示的に追加するか、 **[時間の範囲]** コントロールを使用します。

### <a name="use-the-time-range-control"></a>[時間の範囲] コントロールを使用する
**[時間の範囲]** コントロールを使用するには、上部バーにあるこのコントロールを選択して、ドロップダウン リストから値を選択します。カスタムの時間範囲を作成する場合は、 **[カスタム]** を選択します。

![時刻の選択ツール](media/get-started-portal/time-picker.png)

- 時間の範囲の値は UTC で表示されます。これは、ローカルのタイム ゾーンとは異なる場合があります。
- クエリで **TimeGenerated** のフィルターを明示的に設定した場合、時刻の選択コントロールには "**クエリに設定します**" と表示され、コントロールは競合防止のために無効化されます。

### <a name="run-a-query"></a>クエリを実行する
クエリを実行するには、上部バーの **[実行]** を選択するか、**Shift**+**Enter** キーを押します。 クエリは、空白行に到達するまで実行されます。

## <a name="filter-results"></a>結果のフィルター処理
Log Analytics の結果は、上限の 10,000 件に制限されます。 `Event` のような汎用のクエリでは、返される結果が多すぎて不便です。 クエリでテーブル要素を制限するか、結果にフィルターを明示的に追加することで、クエリ結果をフィルター処理できます。 テーブル要素を使用してフィルター処理を行った場合は新しい結果セットが返されますが、明示的なフィルターは既存の結果セットに適用されます。

### <a name="filter-by-restricting-table-elements"></a>テーブル要素を制限して絞り込む
次のように `Event` クエリのテーブル要素を制限することで、クエリ結果を **Error** イベントのみに絞り込みます。

1. クエリ結果で、**EventLevelName** 列が **Error** であるいずれかのレコードの隣にあるドロップダウン矢印を選択します。 
   
1. 展開表示された詳細で、**EventLevelName** の隣に表示されている **[...]** にカーソルを合わせて選択し、 **["Error" を含める]** を選択します。 
   
   ![クエリにフィルターを追加する](media/get-started-portal/add-filter.png)
   
1. **クエリ エディター**のクエリが次のように変更されます。
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. **[実行]** を選択して、新しいクエリを実行します。

### <a name="filter-by-explicitly-filtering-results"></a>結果を明示的にフィルター処理することで絞り込む
次のように `Event` クエリ結果をフィルター処理することで、このクエリの結果を **Error** イベントのみに絞り込みます。

1. クエリ結果で、列見出し **EventLevelName** の隣にある**フィルター** アイコンを選択します。 
   
1. ポップアップ ウィンドウの最初のフィールドで **[次の値に等しい]** を選択して、次のフィールドに「*error*」と入力します。 
   
1. **[フィルター]** を選択します。
   
   ![Assert](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>列の並べ替え、グループ分け、選択を行う
**TimeGenerated [UTC]** などの特定の列を基準にしてクエリ結果を並べ替えるには、目的の列見出しを選択します。 もう一度列見出しを選択すると、昇順と降順が切り替わります。

![列の並べ替え](media/get-started-portal/sort-column.png)

結果は、グループで整理することもできます。 特定の列を基準にして結果をグループ分けするには、結果テーブル上部にある、 **[Drag a column header and drop it here to group by that column]\(列でグループ分けするにはその列見出しをここにドラッグ アンド ドロップしてください\)** というラベル付きのバーに列見出しをドラッグします。 サブグループを作成する場合は、他の列を上部のバーにドラッグします。 バー内のグループとサブグループの階層および並び順は変更できます。

![グループ](media/get-started-portal/groups.png)

結果の列の非表示と表示を切り替えるには、テーブル上部の **[列]** を選択してから、ドロップダウン リストで目的の列を選択または選択解除します。

![Select columns](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>グラフを表示および変更する
クエリ結果はビジュアル形式で表示することもできます。 例として、次のクエリを入力します。

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

既定では、結果はテーブルに表示されます。 テーブル上部にある **[グラフ]** を選択すると、結果がグラフ形式で表示されます。

![横棒グラフ](media/get-started-portal/bar-chart.png)

結果は、積み上げ横棒グラフで表示されます。 結果を別のビューで表示するには、 **[積み上げ縦棒グラフ]** や **[円グラフ]** などの他のオプションを選択します。

![円グラフ](media/get-started-portal/pie-chart.png)

x 軸や y 軸などのビューのプロパティ、グループ化と分割の設定は、コントロール バーから手動で変更できます。

[render](/azure/kusto/query/renderoperator) 演算子を使用して、クエリ自体に優先ビューを設定することもできます。

## <a name="pin-results-to-a-dashboard"></a>ダッシュ ボードに結果をピン留めする

Log Analytics の結果テーブルまたはグラフを Azure の共有ダッシュボードにピン留めするには、上部バーの **[ダッシュボードにピン留めする]** を選択します。 

![[ダッシュボードにピン留めする]](media/get-started-portal/pin-dashboard.png)

**[別のダッシュボードにピン留め]** ペインで、ピン留め先の共有ダッシュボードを選択または作成して、 **[適用]** を選択します。 選択した Azure ダッシュボードにテーブルまたはグラフが表示されます。

![ダッシュボードにピン留めされたグラフ](media/get-started-portal/pin-dashboard2.png)

テーブルまたはグラフを共有ダッシュボードにピン留めすると、次のように簡略化されます。 

- データは過去 14 日以内のものに制限されます。
- テーブルに表示される列は 4 つまでであり、行は先頭の 7 つのみが表示されます。
- グラフのカテゴリが多い場合、割合の低いカテゴリは単一の **[その他]** ビンに自動的にグループ化されます。

## <a name="save-load-or-export-queries"></a>クエリの保存、読み込み、エクスポートを行う

クエリの作成後、クエリまたは結果を保存することも、他のユーザーと共有することもできます。 

### <a name="save-queries"></a>クエリの保存

クエリを保存するには、次の手順に従います。

1. 上部バーにある **[保存]** を選択します。
   
1. **[保存]** ダイアログで、アルファベット (大文字と小文字)、0 から 9 までの数字、スペース、ハイフン、アンダーバー、ピリオド、かっこ、またはパイプを使用してクエリに**名前**を付けます。 
   
1. クエリの保存形式を **[クエリ]** と **[関数]** から選択します。 関数は、他のクエリから参照できるクエリです。 
   
   クエリを関数として保存する場合は、他のクエリでこのクエリを参照するための短い名前となる **[関数のエイリアス]** を指定します。
   
1. Log Analytics ワークスペース内で作業している場合は、クエリに使用する**クエリ エクスプローラー**で **[カテゴリ]** を指定します (Application Insights クエリでは、カテゴリを使用できません)。
   
1. **[保存]** を選択します。
   
   ![関数を保存する](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>クエリを読み込む
保存済みのクエリを読み込むには、右上にある **[クエリ エクスプローラー]** を選択します。 **[クエリ エクスプローラー]** ペインが開き、すべてのクエリがカテゴリ別に一覧表示されます。 カテゴリを展開するか検索バーにクエリ名を入力して、クエリを選択すると、**クエリ エディター**にクエリが読み込まれます。 クエリ名の隣に表示される星を選択すると、そのクエリを**お気に入り**としてマークできます。

![クエリ エクスプローラー](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>クエリのエクスポートと共有
クエリをエクスポートするには、上部バーで **[エクスポート]** を選択してから、ドロップダウン リストで **[CSV へエクスポート - すべての列]** 、 **[CSV へエクスポート - 表示されている列]** 、または **[Power BI へエクスポート (M Query)]** を選択します。

次のビデオでは、Log Analytics を Excel と統合する方法について説明しています。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

クエリへのリンクを共有するには、上部バーで **[リンクのコピー]** を選択してから、 **[クエリへのリンクのコピー]** 、 **[クエリ テキストのコピー]** 、または **[Copy query results]\(クエリの結果のコピー\)** を選択してクリップボードにコピーします。 クエリ リンクは、同じワークスペースにアクセス可能な他のユーザーに送信できます。

## <a name="next-steps"></a>次のステップ

次のチュートリアルに進み、Azure Monitor ログ クエリの記述方法の詳細について学習してください。
> [!div class="nextstepaction"]
> [Azure Monitor ログ クエリを記述する](get-started-queries.md)
