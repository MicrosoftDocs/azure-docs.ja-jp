---
title: Metrics Advisor を使用してインシデントを診断する
titleSuffix: Azure Cognitive Services
description: Metrics Advisor を使用してインシデントを診断し、データ内の異常に関する詳細な表示を取得する方法について説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: ecbfb2d9acf6c62f95c264a14e306442db25e483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "101703424"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>方法: Metrics Advisor を使用してインシデントを診断する

Metrics Advisor には、診断のための機能がいくつか用意されており、検出されたインシデントの詳細な表示を取得し、根本原因の分析を行うことができます。 メトリックで異常のグループが検出されると、Metrics Advisor によって異常が階層に分類され、その最上位で分析が行われます。

> [!NOTE]
> 現在、Metrics Advisor では、少なくとも 1 つのディメンションを持つメトリックと、*numeric* 型のメジャーに対するインシデント診断がサポートされています。 ご利用のメトリックには、ディメンションごとに SUM のようなディメンションの集計値が必要です。これは診断階層を構築するのに使用されます。 Metrics Advisor には、集計値を生成する場合に役立つ [**自動ロール アップ設定**](onboard-your-data.md#automatic-roll-up-settings)が用意されています。 

左側のナビゲーション ウィンドウで **[Incident hub]\(インシデント ハブ\)** をクリックすると、特定のメトリックの下にあるすべてのインシデントが表示されます。 ページの上部で、各種のメトリックを選択すると、それぞれの検出構成および検出結果を確認することができ、時間の範囲を変更することもできます。

> [!TIP]
> **[Incident hub]\(インシデント ハブ\)** を取得するには、次の方法もあります。
> * ご利用のメトリックの視覚化でデータ ポイントをクリックし、表示される **[フィードバックの追加]** ウィンドウの下部にあるリンクを使用する。
> * **[インシデント]** タブでご利用のメトリックにおける異常のいずれかをクリックする。 

**[概要]** セクションには、選択した時間の範囲内での異常およびアラートの個数など、検出結果が表示されます。

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="[Incident hub]\(インシデント ハブ\)" lightbox="../media/diagnostics/incident-hub-overview.png":::

選択したメトリックおよび時間の範囲内で検出されたインシデントは、 **[インシデント一覧]** に一覧表示されます。 インシデントをフィルター処理および順序付けするためのオプションがあります。 たとえば、重要度を使用します。 さらに詳しい診断を行う場合は、インシデントのいずれかをクリックして **[インシデント]** ページに移動します。

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="インシデント一覧" lightbox="../media/diagnostics/incident-list.png":::

**[診断]** セクションでは、インシデントの詳細な分析を実行したり、根本原因を特定するためのツールを使用したりできます。

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="インシデントを診断する" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>根本原因の通知

メトリックで異常のグループが検出され、インシデントが発生すると、Metrics Advisor によってそのインシデントの根本原因の分析が試みられます。 **根本原因の通知** では、インシデントの原因と考えられる候補が自動的に提示されます。 この機能は、ディメンション内に集計値がある場合にのみ使用できます。 メトリックにディメンションがない場合、そのこと自体が根本原因となります。 根本原因は右側のパネルに一覧表示され、いくつかの理由も一覧表示される場合があります。 テーブルにデータがない場合は、分析を実行するための要件をディメンションが満たしていないことを意味します。

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="根本原因の通知":::


根本原因メトリックと一緒に特定のディメンションが表示されている場合は、 **[go to metric]\(メトリックに移動\)** をクリックすることで、メトリックの詳細を表示できます。

## <a name="incident-tree"></a>インシデント ツリー

潜在的な根本原因に対する自動分析と共に、Metrics Advisor では **インシデント ツリー** を使用した根本原因の手動分析もサポートされています。 インシデント ページには、**クイック診断** ツリーおよび **対話型ツリー** という 2 種類のインシデント ツリーがあります。

クイック診断ツリーは、現在のインシデントを診断するためのものであり、ルート ノードは現在のインシデント ルート ノードに限定されます。 ツリー ノードを展開または折りたたむには、それをクリックします。その系列はツリーの上に置かれたグラフに現在のインシデント系列と共に表示されます。

対話型ツリーを使用すると、現在のインシデントだけでなく古いインシデントを診断できるほか、関連するものも診断できます。 対話型ツリーを使用する場合は、ノードを右クリックしてアクション メニューを開きます。ここでは、ルート ノードをドリルアップするディメンション、ノードごとにドリルダウンするディメンションを選択できます。 上部にあるディメンション一覧の [キャンセル] ボタンをクリックすると、このディメンションからのドリルアップまたはドリルダウンを解除できます。 ノードを左クリックすると、それが選択され、その系列がグラフ内に現在のインシデント系列と一緒に表示されます。

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="インシデント ツリー" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>異常のドリルダウン

インシデント情報を表示する場合には、より詳細な情報を取得することが必要になる場合があります (たとえば、さまざまなディメンションやタイムスタンプについて)。 ご利用のデータに 1 つまたは複数のディメンションが含まれている場合は、ドリルダウン関数を使用して、より詳細な表示を取得できます。 

ドリルダウン関数を使用するには、 **[Incident hub]\(インシデント ハブ\)** の **[Metric drilling]\(メトリックの詳細表示\)** タブをクリックします。 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="メトリックの詳細表示":::

**[ディメンション]** 設定は、インシデントに対するディメンションの一覧です。それぞれに対して使用可能な他のディメンション値を選択できます。 ディメンション値が変更された後。 **[タイムスタンプ]** 設定を使用すると、現在のインシデントをさまざまな時点で表示できます。

### <a name="select-drilling-options-and-choose-a-dimension"></a>詳細表示のオプションを選択し、ディメンションを選択します

ドリルダウン オプションには、次の 2 種類があります。**ドリルダウン** と、**水平方向の比較**。

> [!Note]
> 1. ドリルダウンでは、現在選択されているディメンションを除いて、さまざまなディメンション値からデータを探索できます。 
> 2. 水平方向の比較では、合計ディメンションを除いて、さまざまなディメンション値からデータを探索できます。

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="ディメンションのドリルダウン":::

### <a name="value-comparison-for-different-dimension-values"></a>さまざまなディメンション値についての値比較

[ドリルダウン] タブの 2 番目のセクションは、さまざまなディメンション値についての比較が含まれるテーブルです。 これには、値、初期値、差分値、デルタ値、および異常の有無が含まれます。
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="ドリルダウンの比較" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>さまざまなディメンション値についての値および予期される値の比較

[ドリルダウン] タブの 3 番目のセクションは、さまざまなディメンション値に関するヒストグラムであり、値および予期される値が含まれます。 このヒストグラムは、値と予期される値との差に基づいて並べ替えられます。 最も大きな影響のある予期しない値を容易に見つけることができます。 たとえば、上の図では、合計値を除いて、**US7** が異常の最も大きな原因になっているいることがわかります。

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="ドリルダウン テーブル" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>未処理の値の視覚化
[ドリルダウン] タブの最後の部分は、未処理の値の折れ線グラフです。 このグラフを使用すると、メトリック ページに移動して詳細を表示する必要がなくなります。

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="ドリルダウン折れ線グラフ" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>時系列クラスタリングを使用して類似した異常を表示する

インシデントを表示する場合は、それに関連するさまざまな系列を、 **[Similar time-series-clustering]\(類似の時系列クラスタリング\)** タブを使用して表示できます。 1 つのグループ内の系列は一緒にまとめられます。 上の図から、少なくとも 2 つの系列グループがあることがわかります。 この機能を使用できるのは、次の要件が満たされている場合に限られます。

1. メトリックには 1 つまたは複数のディメンションまたはディメンション値が必要です。
2. 1 つのメトリック内の系列の傾向は類似している必要があります。

使用可能なディメンションは、このタブの上部に一覧表示され、系列を指定するための選択を行うことができます。

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="系列グループ":::

## <a name="compare-time-series"></a>時系列の比較

特定の時系列で異常が検出されたとき、それを、1 つの視覚化で他の複数の系列と比較すると役立つ場合があります。 **[比較ツール]** タブをクリックしてから、青色の **[+ 追加]** ボタンをクリックします。 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="比較する系列の追加" lightbox="../media/diagnostics/add-series.png":::

データ フィードから系列を選択します。 同じ細分性を選択することも別の細分性を選択することもできます。 ターゲット ディメンションを選択し、系列の傾向を読み込んでから、 **[OK]** をクリックして、それを前の系列と比較します。 系列は 1 つの視覚化にまとめられます。 引き続き比較対象の系列を追加して、詳細な分析情報を得ることができます。 **[比較ツール]** タブの上部にあるドロップダウン メニューをクリックして、時間がシフトされた期間にわたって時系列データを比較します。  

> [!Warning]
> 比較を行うには、時系列データ分析でデータ ポイントのシフトが必要になる場合があるため、データの細分性によるサポートが必要です。 たとえば、データが週単位である場合、 **[Day over day]\(前日比\)** 比較を使用した場合、結果は得られません。 この例では、 **[Month over month]\(前月比\)** 比較を代わりに使用します。

時間シフトの比較を選択したら、データ値、デルタ値、またはパーセント デルタのどれを比較対象とするのかを選択できます。

> [!Note]
> * **データ値** は未処理データ値です。
> * **デルタ値** は、未処理の値と比較された値との差です。
> * **パーセント デルタ値** は、未処理の値と比較された値との差を、比較された値で割ったものです。

## <a name="related-incidents-across-metrics"></a>メトリック間での関連するインシデント

場合によっては、異なるメトリックのインシデントを同時に確認したり、他のメトリックの関連するインシデントを確認したりすることも必要です。 関連するインシデントの一覧については、**クロス メトリック分析** に関するセクションを参照してください。 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="メトリック間での関連するインシデント":::

現在のメトリックに関連するインシデントを表示するには、事前にメトリック間にリレーションシップを追加する必要があります。 **[Metrics Graph Settings]\(メトリック グラフの設定\)** をクリックして、リレーションシップを追加します。 同じディメンション名を持つメトリック同士のみを関連付けることができます。 次のパラメーターを使用します。

- [現在のデータ フィードとメトリック]: 現在のインシデントのデータ フィードとメトリック
- [方向]: 2 つのメトリック間のリレーションシップの方向 (現在、関連するインシデント一覧には影響しません)。
- [Another Data feed & Metric]\(別のデータ フィードとメトリック\): 現在のメトリックと接続するためのデータフィードとメトリック


## <a name="next-steps"></a>次のステップ 

- [フィードバックを使用して異常検出を調整する](anomaly-feedback.md)
- [メトリックを構成して検出構成を微調整する](configure-metrics.md)
