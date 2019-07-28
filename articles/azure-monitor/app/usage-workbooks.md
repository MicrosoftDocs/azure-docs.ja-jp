---
title: Azure Monitor ブックを使用した対話型レポートの作成 | Microsoft docs
description: 作成済みのブックやパラメーター化されたカスタム ブックを使用して複雑なレポート作成を簡素化します。
services: application-insights
documentationcenter: ''
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6d84ad69b067f730bbfbcad9e46bdc9ae2036ead
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569605"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Azure Monitor ブックを使用した対話型レポートの作成

ブックでは、テキスト、 [分析クエリ](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)、Azure メトリック、パラメーターを組み合わせて内容豊富な対話型レポートを作成します。 ブックは、同じ Azure リソースにアクセスできる他のチーム メンバーが編集できます。

Workbooks は次のようなシナリオで便利です。

* 目的のメトリック (ユーザー数、リテンション率、コンバージョン率など) が事前にわからない状況でアプリの使用方法を探すとき。他の使用状況分析ツールとは異なり、ブックを使用すると複数の種類の視覚化ツールや分析ツールを結合できるため、この種のフリーフォーム探索に適しています。
* 主なやり取りや他のメトリックのユーザー数を示することで、新しくリリースされた機能のパフォーマンスをチームに説明するとき。
* チームの他のメンバーと A/B 実験の結果を共有するとき。 実験の目標をテキストで説明した後に、その実験の評価に使用される使用状況メトリックと Analytics クエリ、および各メトリックが目標を上回ったかまたは下回ったかどうかを明確に提示します。
* 停止がアプリの使用に及ぼす影響を報告し、データ、テキスト説明、および次の手順についての話し合いを組み合わせて将来の停止を防止するとき。

## <a name="starting-with-a-template-or-saved-workbook"></a>テンプレートまたは保存済みブックから始める

ブックは、個別に編集可能なグラフ、テーブル、テキスト、入力コントロールで構成される複数のセクションで構成されます。 ブックの理解を深めるには、ブックを開くのが最良の方法です。 

アプリの Application Insights エクスペリエンス内から、左側のメニューの **[Workbooks]** を選択します。

![ブックへのナビゲーションのスクリーンショット](./media/usage-workbooks/001-workbooks.png)

これにより、作業を開始する際に役立つ多数の作成済みブックが含まれたブック ギャラリーが起動します。

![ブック ギャラリーのスクリーンショット](./media/usage-workbooks/002-workbook-gallery.png)

ここでは、 **[クイック スタート]** という見出しの下にある **[既定のテンプレート]** から始めます。

![ブック ギャラリーのスクリーンショット](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>ブックのセクションを編集、再配置、複製、および削除する

ブックには、**編集モード**と**閲覧モード**の 2 つのモードがあります。 既定のブックを最初に起動したときは、**編集モード**でブックが開きます。 このモードでは、他の方法では非表示になるステップやパラメーターも含め、ブックのすべてのコンテンツが表示されます。 **閲覧モード**では、簡素化されたレポート スタイルのビューが表示されます。 これにより、レポートの作成時に生じた複雑さを排除できます。変更が必要な場合は、数回クリックするだけで元になるメカニズムを表示できます。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/editing-controls-new.png)

1. セクションの編集が完了したら、セクションの左下隅にある **[編集完了]** をクリックします。

2. セクションの複製を作成するには、 **[このセクションの複製]** アイコンをクリックします。 セクションを複製すると、前のイテレーションを失うことなくクエリを繰り返すことができるので便利です。

3. ブックのセクションを移動するには、 **[上へ移動]** アイコンまたは **[下へ移動]** アイコンをクリックします。

4. セクションを完全に削除するには、 **[削除]** アイコンをクリックします。

## <a name="adding-text-and-markdown-sections"></a>テキストと [マークダウン] セクションを追加する

ブックに見出し、説明、およびコメントを追加すると、テーブルやグラフのセットをナレーションに変えるのに役立ちます。 ブックのテキスト セクションは、見出し、ボールド、斜体、箇条書きなどのテキスト形式として、[マークダウン構文](https://daringfireball.net/projects/markdown/)をサポートします。

ブックにテキスト セクションを追加するには、ブックの下部またはセクションの下部にある **[テキストの追加]** ボタンを使用します。

## <a name="adding-query-sections"></a>クエリ セクションを追加する

![ブックのクエリ セクション](./media/usage-workbooks/analytics-section-new.png)

ブックにクエリ セクションを追加するには、ブックの下部またはセクションの下部にある **[クエリの追加]** ボタンを使用します。

クエリ セクションは柔軟性が高く、次のような質問の回答を得るために使用できます。

* 使用状況が減少したのと同じ期間にサイトが投げた例外の数はいくつですか?
* あるページを閲覧しているユーザーのページ読み込み時間の分布は何でしたか?
* サイトのある一連のページを閲覧したユーザーのうち、ある別の一連のページを閲覧しなかったユーザーの数は? これは、サイトの機能の異なるサブセットを使用する ([Kusto クエリ言語](/azure/kusto/query/)で `join` 演算子を `kind=leftanti` 修飾子とともに使用する) ユーザーのクラスターが存在するかどうかを理解するのに便利です。

ブックを起動したアプリケーションのコンテキストからのクエリだけに限定されているわけではありません。 Application Insight によって監視される複数のアプリや Log Analytics ワークスペースでクエリを実行できます (ただし、これらのリソースへのアクセス許可が必要です)。

その他の外部の Application Insight リソースからクエリを実行するには、**app** 識別子を使用します。

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

このクエリは、3 つの異なるアプリケーションからの要求を組み合わせています。 app01 という名前のアプリ、app02 という名前のアプリ、ローカルの Application Insights リソースからの要求です。

外部の Log Analytics ワークスペースからデータを取得するには、**workspace** 識別子を使用します。

リソース間のクエリの詳細については、[公式のガイダンス](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)をご覧ください。

### <a name="advanced-analytic-query-settings"></a>分析クエリの詳細設定

各セクションには独自の詳細設定があります。これらの設定には、 **[パラメーターの追加]** ボタンの右側にある設定アイコン ![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/005-settings.png) からアクセスできます。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **任意の幅**    | 項目を任意のサイズにするには、この設定を使用します。多数の項目を 1 行に収めることができるので、グラフやテーブルを内容豊富な対話型レポートに効果的に整理できます。  |
   | **条件付きで表示する** | 閲覧モード時にパラメーター値に基づいてステップを非表示にするには、この設定を使用します。 |
   | **パラメーターをエクスポートする**| グリッドまたはグラフ内で選択した行について、後のステップで値を変更したり、表示したりすることができます。  |
   | **編集中でないときにクエリを表示する** | 閲覧モードのときでも、グラフまたはテーブルの上にクエリが表示されます。
   | **編集中でないときに、[分析で開く] ボタンを表示する** | グラフの右隅に青色の Analytics アイコンが追加され、ワンクリックでアクセスできるようになります。|

これらの設定のほとんどは非常に直感的ですが、**パラメーターのエクスポート**を理解するには、この機能を使用しているブックを確認することをお勧めします。

作成済みのブックの 1 つで、アクティブ ユーザーに関する情報が提供されます。

ブックの最初のセクションは、分析クエリ データに基づいています。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/003-active-users.png)

2 番目のセクションも分析クエリ データに基づいていますが、最初のテーブルで行を選択すると、グラフの内容が対話形式で更新されます。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/004-active-users-trend.png)

 これは、テーブルの分析クエリの詳細設定で、 **[When an item is selected, export a parameter]\(項目が選択されているときに、パラメーターをエクスポートする\)** を有効にすることによって可能になります。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/007-settings-export.png)

行を選択すると、2 番目の分析クエリで、エクスポートされた値が使用されます。 行が選択されていない場合は、全体的な値を表す行が既定で使用されます。 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>メトリック セクションを追加する

メトリック セクションでは、Azure Monitor のメトリック データを対話型レポートに組み込むためのフル アクセスが提供されます。 作成済みのブックの多くには、分析クエリ データとメトリック データの両方が含まれるので、両方の機能の利点をすべて 1 か所で最大限に活用できます。 また、アクセス可能なサブスクリプションのリソースからメトリック データを取得することもできます。

仮想マシンのデータをブックに取り込んで、CPU のパフォーマンスをグリッドで表示している例を次に示します。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>パラメーター セクションを追加する

ブックのパラメーターを使用すると、クエリ セクションやテキスト セクションを手動で編集しなくても、ブックの値を変更できます。  これにより、基になる分析クエリ言語を理解する必要がなくなり、ブックベースのレポート作成の潜在的な対象ユーザーが大幅に拡大されます。

パラメーターの値は、``{parameterName}`` のようにパラメーター名を中かっこで囲むことによって、クエリ、テキスト、または他のパラメーター セクションで置き換えられます。  パラメーター名には、JavaScript 識別子と同様の規則が適用され、基本的に英文字またはアンダースコアの後に英数字またはアンダースコアが続きます。 たとえば、**a1** は許可されますが、**1a** は許可されません。

パラメーターは線形であり、ブックの先頭から始まり、後のステップへと流れます。  ブックの後の部分で宣言されたパラメーターは、前の部分で宣言されたパラメーターをオーバーライドできます。  これにより、クエリを使用するパラメーターは、前の部分で定義されたパラメーターの値にアクセスすることもできます。  パラメーターのステップ自体の中でも、パラメーターは線形 (左から右) であり、右側のパラメーターはその同じステップで以前に宣言されたパラメーターに依存することができます。
 
現在、次の 4 種類のパラメーターがサポートされています。

  |         |          |
   | ---------------- |:-----|
   | **テキスト**    | ユーザーはテキスト ボックスを編集します。必要に応じて、既定値を入力するためのクエリを提供できます。 |
   | **ドロップダウン** | ユーザーは一連の値の中から選択します。 |
   | **時間の範囲の選択**| ユーザーは、時間の範囲の値の定義済みセットから選択するか、カスタムの時間の範囲から選択します。|
   | **リソースの選択** | ユーザーは、そのブック用に選択されたリソースの中から選択します。|

### <a name="using-a-text-parameter"></a>テキスト パラメーターを使用する

ユーザーがテキスト ボックスに入力した値は、エスケープしたり、引用符で囲んだりせずに、クエリで直接置き換えられます。 必要な値が文字列の場合、クエリでは **'{parameter}'** のようにパラメーターを引用符で囲む必要があります。

これにより、テキスト ボックス内の値を任意の場所で使用できるようになります。 値には、テーブル名、列名、関数名、演算子などを指定できます。

テキスト パラメーターには、 **[Get default value from analytics query]\(分析クエリから既定値を取得する\)** という設定があります。この設定を使用すると、ブックの作成者はクエリを使用してそのテキスト ボックスの既定値を設定できます。

分析クエリから取得した既定値を使用する場合、最初の行の最初の値 (行 0、列 0) だけが既定値として使用されます。 そのため、1 行および 1 列だけを返すようにクエリを制限することをお勧めします。 クエリによって返されるその他のデータは無視されます。 

クエリから返される値は、エスケープしたり引用符で囲んだりせずに直接置き換えられます。 クエリから行が返されない場合、パラメーターの結果は空の文字列になるか (パラメーターが必須でない場合)、未定義になります (パラメーターが必須の場合)。

### <a name="using-a-dropdown"></a>ドロップダウンを使用する

ドロップダウン パラメーターを使用すると、1 つまたは複数の値を選択できるドロップダウン コントロールを作成できます。

ドロップダウンは分析クエリによって設定されます。 クエリから 1 つの列が返される場合、その列の値はドロップダウン コントロールの**値**と**ラベル**の両方になります。 クエリから 2 つの列が返される場合は、最初の列が**値**になり、2 番目の列がドロップダウンに表示される**ラベル**になります。  クエリから 3 つの列が返される場合は、3 番目の列を使用して、そのドロップダウンの既定の選択が示されます。  この列には任意の型を指定できますが、最も簡単なのはブール型または数値型を使用することです。この場合、0 が false、1 が true です。

 列が文字列型の場合、null/空の文字列は false とみなされ、その他の値は true とみなされます。 単一選択ドロップダウンの場合、値が true である最初の値が既定の選択として使用されます。  複数選択ドロップダウンの場合、値が true であるすべての値が既定の選択セットとして使用されます。 ドロップダウンの項目は、クエリから行が返された順序で表示されます。 

アクティブ ユーザー レポートに表示されるパラメーターを見てみましょう。 **[時間範囲]** の横の編集記号をクリックします。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/009-time-range.png)

これにより、[パラメーターの編集] メニュー項目が表示されます。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/010-time-range-edit.png)

このクエリでは、分析クエリ言語の **datatable** という機能を使用しています。この機能を使用すると、何もない状態から豊富な内容の任意のテーブルを生成できます。 たとえば、次のような分析クエリがあるとします。

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

次のような結果が生成されます。

![Application Insights の [ブック] セクションの編集コントロール](./media/usage-workbooks/011-data-table.png)

より適切な例として、ドロップダウンを使用した名前での国や地域の選択があります。

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

このクエリでは、次のような結果が表示されます。

![国ドロップダウン](./media/usage-workbooks/012-country-dropdown.png)

ドロップダウンは、対話型レポートをカスタマイズおよび作成するための非常に強力なツールです。

### <a name="time-range-parameters"></a>時間の範囲のパラメーター

ドロップダウン パラメーターを使用して独自のカスタムの時間の範囲パラメーターを作成できますが、同程度の柔軟性を必要としない場合は、すぐに使用できる時間の範囲パラメーターを使用することもできます。 

時間の範囲パラメーターには、5 分から過去 90 日間まで、15 個の既定の範囲があります。 カスタムの時間の範囲を選択できるオプションもあります。このオプションでは、レポートを操作するユーザーが時間の範囲の明示的な開始値と停止値を選択できます。

### <a name="resource-picker"></a>リソースの選択

リソースの選択パラメーターを使用すると、レポートの範囲を特定の種類のリソースに設定できます。 リソースの選択を活用している作成済みのブックの例として、**エラーの分析情報**ブックがあります。

![国ドロップダウン](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>ブックを保存してチームと共有する

Workbooks は Application Insights リソース内の、ユーザー個人の **[個人用レポート]** セクションか、Application Insights リソースにアクセスできるユーザーであれば誰でもアクセスできる **[共有レポート]** セクションのいずれかに保存されます。 リソース内のすべてのブックを表示するには、操作バーの **[開く]** ボタンをクリックします。

**[個人用レポート]** にあるブックを共有するには:

1. 操作バーで、 **[開く]** をクリックします。
2. 共有するブックの横にある [...] ボタンをクリックします。
3. **[Move to Shared Reports]\(共有レポートに移動\)** をクリックします。

リンクまたは電子メールでブックを共有するには、操作バーで **[共有]** をクリックします。 リンクの受信者がブックを表示するには、Azure Portal でこのリソースにアクセスできる必要があります。 受信者が編集するには、そのリソースに対する共同作成者以上のアクセス許可が必要です。

ブックへのリンクを Azure Dashboard にピン留めするには:

1. 操作バーで、 **[開く]** をクリックします。
2. ピン留めするブックの横にある [...] ボタンをクリックします。
3. **[ダッシュボードにピン留め]** をクリックします。

## <a name="contributing-workbook-templates"></a>ブック テンプレートを提供する

優れたブック テンプレートを作成したら、コミュニティと共有できます。 詳細については、[GitHub リポジトリ](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)を参照してください。

## <a name="next-steps"></a>次の手順
- 使用状況を把握できるようにするには、[カスタム イベント](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)または[ページ ビュー](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [ユーザー、セッション、イベント](../../azure-monitor/app/usage-segmentation.md)
    - [ファネル](../../azure-monitor/app/usage-funnels.md)
    - [保持](../../azure-monitor/app/usage-retention.md)
    - [ユーザー フロー](../../azure-monitor/app/usage-flows.md)
    - [ユーザー コンテキストの追加](../../azure-monitor/app/usage-send-user-context.md)