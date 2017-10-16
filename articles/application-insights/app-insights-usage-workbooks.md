---
title: "Azure Application Insights のインタラクティブな Workbooks で使用状況データを調査して共有する | Microsoft docs"
description: "Web アプリのユーザーの統計分析について説明します。"
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: bwren
ms.openlocfilehash: 75028b4fbda43d90f56690a33c7eb624fce049c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Application Insights のインタラクティブなブックで使用状況データを調査して共有する

Workbooks は、[Azure Application Insights](app-insights-overview.md) のデータ視覚化、[分析クエリ](app-insights-analytics.md)、およびテキストをインタラクティブなドキュメントに結合します。 Workbooks は同じ Azure リソースにアクセスできる他のチーム メンバーが編集できます。 つまり、ブックの作成に使用するクエリやコントロールはそのブックを読んでいる他のユーザーも使用できるため、検索や拡張および校正を簡単に行うことができます。

Workbooks は次のようなシナリオで便利です。

* 目的のメトリック (ユーザー数、リテンション率、コンバージョン率など) が事前にわからない状況でアプリの使用方法を探すとき。Application Insights の他の使用状況分析ツールとは異なり、ブックを使用すると複数の種類の視覚化ツールや分析ツールを結合できるため、この種類のフリーフォーム探索には便利です。
* 主なやり取りや他のメトリックのユーザー数を示することで、新しくリリースされた機能のパフォーマンスをチームに説明するとき。
* チームの他のメンバーと A/B 実験の結果を共有するとき。 実験の目標をテキストで説明した後に、その実験の評価に使用される使用状況メトリックと Analytics クエリ、および各メトリックが目標を上回ったかまたは下回ったかどうかを明確に提示します。
* 停止がアプリの使用に及ぼす影響を報告し、データ、テキスト説明、および次の手順についての話し合いを組み合わせて将来の停止を防止するとき。

> [!NOTE]
> Application Insights のリソースには、ブックを使用するためのページ ビューやカスタム イベントが含まれる必要があります。 [アプリをセットアップし、Application Insights JavaScript SDK を使用してページ ビューを自動的に収集する方法について説明します](app-insights-javascript.md)。
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>ブックのセクションを編集、再配置、複製、および削除する

ブックはセクションで構成されます。使用状況の視覚化、グラフ、テーブル、テキスト、Analytics クエリの結果などのセクションは独立して編集できます。

ブックのセクションのコンテンツを編集するには、下およびブックのセクションの右にある **[編集]** ボタンをクリックします。

![Application Insights の [ブック] セクションの編集コントロール](./media/app-insights-usage-workbooks/editing-controls.png)

1. セクションの編集が完了したら、セクションの左下隅にある **[編集完了]** をクリックします。

2. セクションの複製を作成するには、**[このセクションの複製]** アイコンをクリックします。 セクションを複製すると、前のイテレーションを失うことなくクエリを繰り返すことができるので便利です。

3. ブックのセクションを移動するには、**[上へ移動]** アイコンまたは **[下へ移動]** アイコンをクリックします。

4. セクションを完全に削除するには、**[削除]** アイコンをクリックします。

## <a name="adding-usage-data-visualization-sections"></a>利用状況データを視覚化するセクションを追加する

Workbooks では、4 つの方法で利用状況を分析して視覚化できます。 それぞれがアプリの利用状況に関する一般的な質問に対する回答を提供します。 これら 4 つのセクション以外にテーブルやグラフを追加するには、[分析クエリ] セクションを追加します (下記参照)。

ブックに [ユーザー]、[セッション]、[イベント]、または [保持] セクションを追加するには、**[ユーザーの追加]** ボタン、またはブックの下やセクションの下にある対応するボタンを使用します。

![Workbooks の [ユーザー] セクション](./media/app-insights-usage-workbooks/users-section.png)

**[ユーザー]** セクションは、「サイトのあるページを閲覧またはある機能を使用したユーザーは何人か?」という質問に対する回答を提供します。

**[セッション]** セクションは、「サイトのあるページを閲覧またはある機能を使用したときにユーザーが消費したセッションの数はいくつか?」という質問に対する回答を提供します。

**[イベント]** セクションは、「サイトのあるページをユーザーが何回閲覧またはある機能を何回使用したか?」という質問に対する回答を提供します。

これら 3 種類のセクションは、それぞれ同じセットのコントロールや視覚化を提供します。

* [[ユーザー]、[セッション]、[イベント] の各セクションの編集について説明します](app-insights-usage-segmentation.md)。
* 各セクションの上部にある **[グラフの表示]**、**[グリッドの表示]**、**[洞察の表示]**、および **[これらのユーザーのサンプル]** の各チェックボックスを使用して、メインのグラフ、ヒストグラム グリッド、自動洞察、サンプル ユーザーの視覚化を切り替えます。

![Workbooks の [保持] セクション](./media/app-insights-usage-workbooks/retention-section.png)

**[保持]** セクションは、「ある日またはある週であるページを閲覧またはある機能を使用したユーザーのうち、翌日または翌週に戻ってきたユーザーは何人か?」という質問に対する回答を提供します。

* [[保持] セクションの編集について説明します](app-insights-usage-retention.md)。
* セクションの上部にある **[保有期間全体のグラフを表示する]** チェックボックスを使用して、オプションの保有期間全体のグラフを切り替えます。

## <a name="adding-application-insights-analytics-sections"></a>Application Insights の [分析] セクションを追加する

![Workbooks の [分析] セクション](./media/app-insights-usage-workbooks/analytics-section.png)

Application Insights の [分析クエリ] セクションをブックに追加するには、ブックの下部またはセクションの下部にある **[分析クエリの追加]** ボタンを使用します。

[分析クエリ] セクションでは、ブックに Application Insights のデータの上から任意のクエリを追加できます。 この柔軟性は、[分析クエリ] セクションは前述の [ユーザー]、[セッション]、[イベント]、[保持] セクションのサイトに関する質問以外のすべての質問に無条件で回答することを意味します。たとえば、次のような質問です。

* 使用状況が減少したのと同じ期間にサイトが投げた例外の数はいくつですか?
* あるページを閲覧しているユーザーのページ読み込み時間の分布は何でしたか?
* サイトのある一連のページを閲覧したユーザーのうち、ある別の一連のページを閲覧しなかったユーザーの数は? これは、サイトの機能の異なるサブセットを使用する (Log Analytics クエリ言語で `join` 演算子を `kind=leftanti` 修飾子とともに使用する) ユーザーのクラスターが存在する場合に理解するのに便利です。

クエリの記述について詳しくは、[Log Analytics クエリ言語のリファレンス](https://docs.loganalytics.io/)に関するページをご覧ください。

## <a name="adding-text-and-markdown-sections"></a>テキストと [マークダウン] セクションを追加する

ブックに見出し、説明、およびコメントを追加すると、テーブルやグラフのセットをナレーションに変えるのに役立ちます。 ブックのテキスト セクションは、見出し、ボールド、斜体、箇条書きなどのテキスト形式として、[マークダウン構文](https://daringfireball.net/projects/markdown/)をサポートします。

ブックにテキスト セクションを追加するには、ブックの下部またはセクションの下部にある **[テキストの追加]** ボタンを使用します。

## <a name="saving-and-sharing-workbooks-with-your-team"></a>ブックを保存してチームと共有する

Workbooks は Application Insights リソース内の、ユーザー個人の **[個人用レポート]** セクションか、Application Insights リソースにアクセスできるユーザーであれば誰でもアクセスできる **[共有レポート]** セクションのいずれかに保存されます。 リソース内のすべてのブックを表示するには、操作バーの **[開く]** ボタンをクリックします。

**[個人用レポート]** にあるブックを共有するには:

1. 操作バーで、**[開く]** をクリックします。
2. 共有するブックの横にある [...] ボタンをクリックします。
3. **[Move to Shared Reports]\(共有レポートに移動\)** をクリックします。

リンクまたは電子メールでブックを共有するには、操作バーで **[共有]** をクリックします。 リンクの受信者がブックを表示するには、Azure Portal でこのリソースにアクセスできる必要があります。 受信者が編集するには、そのリソースに対する共同作成者以上のアクセス許可が必要です。

ブックへのリンクを Azure Dashboard にピン留めするには:

1. 操作バーで、**[開く]** をクリックします。
2. ピン留めするブックの横にある [...] ボタンをクリックします。
3. **[ダッシュボードにピン留め]** をクリックします。

## <a name="next-steps"></a>次のステップ

## <a name="next-steps"></a>次のステップ
- 使用状況を把握できるようにするには、[カスタム イベント](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)または[ページ ビュー](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [ユーザー、セッション、イベント](app-insights-usage-segmentation.md)
    - [ファネル](usage-funnels.md)
    - [保持](app-insights-usage-retention.md)
    - [ユーザー フロー](app-insights-usage-flows.md)
    - [ユーザー コンテキストの追加](app-insights-usage-send-user-context.md)
    
