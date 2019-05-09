---
title: Azure Application Insights を使用したメトリックの探索 | Microsoft Docs
description: メトリック エクスプローラーでグラフを解釈する方法と、メトリック エクスプローラー ブレードをカスタマイズする方法です。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: mbullwin
ms.openlocfilehash: 5c659ca2f40d47450227d16963499a6b27c9e313
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817119"
---
# <a name="exploring-metrics-in-application-insights"></a>Application Insights を使用したメトリックの探索
[Application Insights][start] のメトリックとは、アプリケーションからのテレメトリとして送信される測定値とイベントの数を表します。 メトリックは、パフォーマンスの問題を検出し、アプリケーションの利用に関する傾向を把握するのに役立ちます。 さまざまな標準メトリックが用意されているほか、独自にカスタムのメトリックとイベントを作成することもできます。

> [!NOTE]
> この記事では、現在非推奨で、最終的に廃止されるクラシック メトリック エクスプローラー エクスペリエンスについて説明します。 [この記事](../platform/metrics-charts.md)で説明されている新しいエクスペリエンスを確認することをお勧めします。

メトリックとイベントの数は、合計、平均、カウントなどの集計値のグラフに表示されます。

グラフのサンプル セットを次に示します。

![](./media/metrics-explorer/01-overview.png)

Application Insights ポータルでは、あらゆる場所でメトリック グラフが使用されます。 多くの場合、これらはカスタマイズ可能で、ブレードには複数のグラフを追加できます。 概要ブレードから順番にクリックして、 ("サーバー" などのタイトルが付いた) より詳細なグラフに進んだり、**[メトリックス エクスプローラー]** をクリックして新しいブレードを開き、カスタム グラフを作成したりすることもできます。

## <a name="time-range"></a>時間範囲
任意のブレードのグラフまたはグリッドで使用される時間範囲を変更できます。

![Azure ポータルでアプリケーションの概要ブレードを開きます](./media/metrics-explorer/03-range.png)

想定しているデータがまだ表示されていない場合は、[最新の情報に更新] をクリックします。 グラフは周期的に自動で更新されますが、時間範囲が広い場合、更新間隔も長くなります。 データが分析パイプラインを介してグラフとして表示されるまでに時間がかかる場合があります。

グラフの一部を拡大するには、グラフ上でドラッグします。

![グラフの一部をドラッグします。](./media/metrics-explorer/12-drag.png)

グラフを元に戻すには、[Undo Zoom (ズームを元に戻す)] ボタンをクリックします。

## <a name="granularity-and-point-values"></a>粒度とポイント値
グラフ上にマウス ポインターを置くと、そのポイントのメトリックの値が表示されます。

![グラフ上にマウス ポインターを置きます](./media/metrics-explorer/02-focus.png)

特定のポイントのメトリックの値は、先行するサンプリング間隔の集計です。

サンプリング間隔または "粒度" は、ブレードの上部に表示されます。

![ブレードのヘッダー。](./media/metrics-explorer/11-grain.png)

[時間範囲] ブレードで粒度を調整できます。

![ブレードのヘッダー。](./media/metrics-explorer/grain.png)

使用できる粒度は、選択した時間範囲によって異なります。 明示的な粒度は、時間範囲の "自動" 粒度の代替手段です。


## <a name="editing-charts-and-grids"></a>グラフおよびグリッドの編集
新しいグラフをブレードに追加するには:

![メトリックス エクスプローラーで、[グラフの追加] を選択します](./media/metrics-explorer/04-add.png)

表示内容を編集する既存のグラフまたは新しいグラフで **[編集]** を選択します。

![1 つまたは複数のメトリックを選択します](./media/metrics-explorer/08-select.png)

1 つのグラフに複数のメトリックを表示できますが、一緒に表示できるメトリックの組み合わせに関して制限があります。 1 つのメトリックを選択するとすぐに、他の特定のメトリックが無効になります。

アプリに[カスタム メトリック][track]を組み込んでいる場合 (TrackMetric と TrackEvent の呼び出し) は、これらのメトリックがここに表示されます。

## <a name="segment-your-data"></a>データの分割
プロパティによりメトリックを分割して、クライアントと他のオペレーティング システムでのページ ビューの比較などを行えます。

グラフまたはグリッドを選択します。[グループ化] をオンにし、グループ化のために使用するプロパティを選択します。

![[グループ化] をオンにし、[グループ化] でプロパティを選択します](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> グループ化を使用すると、面グラフや棒グラフで積み上げ表示を指定できます。 これは、集計方法が "合計" である場合に適しています。 集計の種類が "平均値" の場合は、表示タイプを "行" または "グリッド" にします。
>
>

アプリに組み込んだ[カスタム メトリック][track]がプロパティ値を持つ場合は、この一覧でそのプロパティを選択できます。

分割されたデータを表示するのにグラフが小さすぎる場合は、 高さを調整することができます。

![スライダーを調整します](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>集計タイプ
既定でグラフの横にある凡例は、通常、グラフの期間における集計値を示します。 グラフ上にマウス ポインターを置くと、その時点での値が表示されます。

グラフの各データ ポイントは、前のサンプリング間隔または "粒度" で受信したデータ値の集計です。 粒度はブレードの上部に表示され、グラフの全体的な時間スケールに応じて異なります。

メトリックは、次のようなさまざまな方法で集計できます。

* **カウント** は、サンプリング間隔の間に受信したイベントの数です。 要求などのイベントに使用されます。 グラフの高さの変化は、イベントの発生率の変化を示します。 サンプリング間隔を変更すると、数値が変わることに注意してください。
* **合計** は、サンプリング間隔 (グラフの期間) 中に受信したすべてのデータ ポイントの値を合計します。
* **平均** は、合計を、間隔中に受信したデータ ポイントの数で除算します。
* **一意の** 数は、ユーザーとアカウントの数に使用されます。 サンプリング間隔中 (グラフの期間中)、数値はその期間のさまざまなユーザーの数を示します。
* **%** - 各集計の割合バージョンは、セグメント化されたグラフでのみ使用されます。 合計は常に 100% となり、グラフには、合計に含まれるさまざまなコンポーネントの相対的寄与率が示されます。

    ![割合集計](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>集計タイプの変更

![グラフを編集した後、集計を選択します](./media/metrics-explorer/05-aggregation.png)

新しいグラフを作成したとき、またはすべてのメトリックを選択解除したときに、各メトリックの既定の方法が表示されます。

![すべてのメトリックを選択解除して既定値を表示します](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Y 軸の固定 
既定では、グラフの Y 軸にはゼロからデータ範囲の最大値までが表示され、クォンタム値が視覚的に示されますが。 値の変化を、クォンタムを超えて視覚的に調査する必要が出てくることもあります。 このようなカスタマイズでは、Y 軸の範囲の編集機能を使用して、Y 軸の最小値または最大値を必要な場所に固定します。
"詳細設定" のチェックボックスをオンにすると、Y 軸の範囲の設定が表示されます

!["詳細設定" をクリックし、[カスタム] の範囲を選択して、最小値と最大値を指定します](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>データのフィルター選択
選択したプロパティ値のセットのメトリックのみを表示するには:

![[フィルター] をクリックした後、プロパティを展開し、目的の値にチェック マークを付けます](./media/metrics-explorer/19-filter.png)

特定のプロパティの値を選択しない場合、すべての値を選択するのと同じ意味になり、そのプロパティに基づくフィルターはオフになります。

各プロパティ値の横に表示されるイベントの数に注意してください。 あるプロパティの値を選択すると、その他のプロパティの値の横に表示される数値が調整されます。

フィルターは、ブレードのすべてのグラフに適用されます。 グラフごとに異なるフィルターを適用する場合は、それぞれ異なるメトリック ブレードを作成して保存します。 その場合、グラフを並べて表示できるように、各ブレードのグラフをダッシュボードに固定できます。

### <a name="remove-bot-and-web-test-traffic"></a>Bot と Web テストのトラフィックの除外
**[実際のトラフィックまたは合成トラフィック]** フィルターを使用し、**[実際]** チェック ボックスをオンにします。

**[人工トラフィックのソース]** フィルターを適用することもできます。

### <a name="to-add-properties-to-the-filter-list"></a>プロパティをフィルター一覧に追加するには
自分が選んだカテゴリでテレメトリをフィルターしたい場合があります。 たとえば、ユーザーをさまざまなカテゴリに分類している場合、それらのカテゴリでデータをセグメント化するとします。

[独自のプロパティを作成](../../azure-monitor/app/api-custom-events-metrics.md#properties)します。 さまざまな SDK モジュールによって送信される標準テレメトリも含め、すべてのテレメトリで表示されるように、 [テレメトリ初期化子](../../azure-monitor/app/api-custom-events-metrics.md#defaults) でそのプロパティを設定します。

## <a name="edit-the-chart-type"></a>グラフの種類の編集
グリッドとグラフを切り替えることができます。

![グリッドまたはグラフを選択し、グラフの種類を選択します](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>メトリック ブレードの保存
作成したグラフはお気に入りとして保存できます。 組織のアカウントを使用している場合は、これを他のチーム メンバーと共有するかどうかを選択できます。

![[お気に入り] を選択します](./media/metrics-explorer/21-favorite-save.png)

このブレードをもう一度表示するには、 **概要ブレードに移動** し、[お気に入り] を開きます。

![概要ブレードで [お気に入り] を選択します](./media/metrics-explorer/22-favorite-get.png)

相対的な時間範囲を選択して保存した場合、ブレードは最新のメトリックで更新されます。 絶対的な時間範囲を選択した場合は、毎回同じデータが表示されます。

## <a name="reset-the-blade"></a>ブレードのリセット
ブレードを編集した後で元の保存済みセットに戻すには、[リセット] をクリックします。

![メトリックス エクスプローラーの上部にあるボタン](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live Metrics Stream

テレメトリの他の即時ビューを見るには、[Live Stream](live-stream.md) を開きます。 集計処理のため、ほとんどのメトリックは表示に数分かかります。 これに対し、ライブ メトリックは待ち時間が短くなるように最適化されています。 

## <a name="set-alerts"></a>アラートの設定
すべてのメトリックの異常な値を電子メールで通知するには、アラートを追加します。 アカウント管理者または特定の電子メール アドレスのいずれかに電子メールを送信することを選択できます。

![メトリックス エクスプローラーで、[アラート ルール]、[アラートの追加] の順に選択します](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[アラートの詳細についてはこちらをご覧ください][alerts]。


## <a name="continuous-export"></a>連続エクスポート
データを外部で処理できるように連続してエクスポートする場合は、 [連続エクスポート](../../azure-monitor/app/export-telemetry.md)の使用を検討してください。

### <a name="power-bi"></a>Power BI
機能がより豊富なビューでデータを表示する場合は、 [Power BI にエクスポート](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)することができます。

## <a name="analytics"></a>Analytics
[Analytics](../../azure-monitor/app/analytics.md) は、強力なクエリ言語を使用してテレメトリを分析する汎用性に優れた方法です。 メトリックの結果を組み合わせたり計算したりする場合や、アプリの最近のパフォーマンスを詳しく調べる場合は、Analytics を使用します。 

メトリックのグラフで Analytics アイコンをクリックすると、同等の Analytics クエリを直接取得することができます。

## <a name="troubleshooting"></a>トラブルシューティング
*グラフにデータが表示されない。*

* フィルターは、ブレードのすべてのグラフに適用されます。 あるグラフに注目するときに、別のグラフですべてのデータを除外するフィルターを設定していないことを確認します。

    グラフごとに異なるフィルターを設定する場合は、それぞれ異なるブレードでグラフを作成し、個別のお気に入りとして保存します。 その場合、グラフを並べて表示できるように、グラフをダッシュボードに固定できます。
* メトリックで定義されていないプロパティでグラフをグループ化すると、グラフに何も表示されなくなります。 "group by" をクリアするか、別のグループ化プロパティを選択してみます。
* パフォーマンス データ (CPU、IO レートなど) は、Java Web サービス、Windows デスクトップ アプリ、[IIS Web アプリおよびサービス (Status Monitor がインストールされている場合)](../../azure-monitor/app/monitor-performance-live-website-now.md)、[Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md) で使用できます。 Azure Web サイトでは利用できません。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>次の手順
* [Application Insights による使用状況の監視](../../azure-monitor/app/usage-overview.md)
* [診断検索の使用](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
