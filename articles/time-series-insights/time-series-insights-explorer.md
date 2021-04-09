---
title: Explorer を使用してデータを調査する - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Explorer を使用して IoT データを表示する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 639f2ef12d190a56e04a9b48d96ea0a6537d243c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020114"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights Gen1 Explorer

> [!CAUTION]
> これは Gen1 の記事です。

この記事では、Azure Time Series Insights Gen1 [Explorer Web アプリ](https://insights.timeseries.azure.com/)の機能とオプションについて説明します。 Azure Time Series Insights Explorer は、サービスによって提供される強力なデータ可視化機能を備え、ご利用の環境からアクセスすることができます。

Azure Time Series Insights は、数十億件の IoT イベントを同時に簡単に調査および分析できるようにする、フル マネージドの分析、ストレージ、および視覚化サービスです。 データの全体像が示され、これを使用してすばやく IoT ソリューションを検証したり、ミッション クリティカルなデバイスに発生するコストのかかるダウンタイムを回避したりできます。 ほぼリアルタイムで隠れた傾向を発見したり、異常を特定したり、根本原因分析を実施したりできます。

> [!TIP]
> デモンストレーション環境を使用したガイド ツアーについては、[Azure Time Series Insights のクイック スタート](time-series-quickstart.md)を参照してください。

## <a name="video"></a>ビデオ

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Azure Time Series Insights Explorer を使用したデータのクエリについて説明します。 </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
><a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">Azure IoT ソリューション アクセラレータを使用して Azure Time Series Insights を開始する方法</a>を示した先行ビデオをご覧ください。

## <a name="prerequisites"></a>前提条件

Azure Time Series Insights Explorer を使用するには、以下が必要です。

- Azure Time Series Insights 環境を作成する。 詳細については、[Azure Time Series Insights の使用を開始する方法](./time-series-insights-get-started.md)に関するページを参照してください。
- 環境内でお使いのアカウントへの[アクセス権を与えます](./concepts-access-policies.md)。
- それに [IoT ハブ](./how-to-ingest-data-iot-hub.md)または[イベント ハブ](./how-to-ingest-data-event-hub.md)のイベント ソースを追加します。

## <a name="explore-and-query-data"></a>データを調査しクエリを実行する

イベント ソースを Azure Time Series Insights 環境に接続してから数分以内に、時系列データを調査し、クエリを実行できます。

1. 開始するには、Web ブラウザーで [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com/) を開きます。 ウィンドウの左側で、環境を選択します。 アクセスできるすべての環境がアルファベット順に一覧表示されます。

1. 環境を選択したら、一番上にある **[From]\(開始\)** と **[To]\(終了\)** の構成を使用するか、または必要な期間を選択してドラッグします。 右上隅にある虫眼鏡を選択するか、または選択された期間を右クリックして **[検索]** を選択します。

1. **[Auto On] (自動オン)** ボタンを選択して、1 分ごとに自動的に可用性を更新することもできます。 **[Auto On] (自動オン)** ボタンは、メインの視覚化の内容ではなく、可用性チャートにのみ適用されます。

1. Azure Cloud アイコンをクリックすると、Azure Portal の環境が表示されます。

   [![Azure Time Series Insights 環境の選択](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. 次に、選択した期間内のすべてのイベントの数を示すグラフが表示されます。 ここでは多くのことを制御できます。

    - **期間編集パネル**: 期間のスペースで環境に対するクエリを実行します。 これは画面の左側にあります。
      - **[MEASURE]\(メジャー\)** :このドロップダウン リストには、すべての数値列 (**Double**) が表示されます。
      - **[SPLIT BY]\(分割基準\)** :このドロップダウン リストには、カテゴリ列 (**String**) が表示されます。
      - **[MEASURE]\(メジャー\)** の横にあるコントロール パネルから、ステップ補間を有効にしたり、最小値と最大値を表示したり、Y 軸を調整したりできます。 また、データの数、平均、または合計のいずれを表示するかを調整することもできます。
      - 最大 5 つの期間を追加して同じ X 軸に表示できます。 新しい期間を追加する場合は **[追加]** を選択し、既存の期間のコピーを追加する場合は **[この期間の複製]** ボタンを使用します。

        [![期間の選択、フィルター処理、およびクエリ パネル](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **[述語]** :次の表に示されているオペランドのセットを使用してイベントをすばやくフィルター処理するには、述語を使用します。 選択またはクリックして検索を実行すると、その検索に基づいて述語が自動的に更新されます。 サポートされるオペランドの型には以下が含まれます。

         |Operation  |サポートされている型  |Notes  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**、**DateTime**、**TimeSpan**       |         |
         |**=** 、 **!=** 、 **<>**     | **String**、**Bool**、**Double**、**DateTime**、**TimeSpan**、**NULL**        |         |
         |**IN**     | **String**、**Bool**、**Double**、**DateTime**、**TimeSpan**、**NULL**        |  すべてのオペランドは同じ型か **NULL** 定数である必要があります。        |
         |**HAS**     | **String**        |  右側には定数文字列リテラルのみが許可されます。 空の文字列や **NULL** は許可されません。       |

      - **クエリの例**

         [![Gen1 クエリの例](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. **間隔サイズ** スライダー ツールを使用すると、同じ期間にわたる間隔を拡大および縮小できます。 このスライダーにより、滑らかな傾向を示す大きな時間スライスから、ミリ秒単位の小さなスライスまでの動きのより正確な制御が可能になるため、きめ細かな高解像度のデータ片を表示して分析できるようになります。 スライダーの既定の開始点は、解像度、クエリ速度、および細分性のバランスを取るように選択された、データの最適なビューとして設定されます。

1. **タイム ブラシ** ツールにより、ある期間から別の期間への移動が簡単になります。

1. 現在のクエリを保存し、それを環境の他のユーザーと共有するには、 **[保存]** アイコンを選択します。 **[開く]** アイコンを選択すると、保存されたすべてのクエリと、アクセスできる環境内の他のユーザーのすべての共有クエリを確認できます。

   [![クエリ](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>データの視覚化

1. 最大 4 つの固有のクエリを同時に表示するには、**パースペクティブ ビュー** ツールを使用します。 **[Perspective View] (パースペクティブ ビュー)** ボタンは、グラフの右上隅にあります。

   [![[パースペクティブ] ペインに追加するクエリの選択](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. グラフを表示してデータを視覚的に調査し、**グラフ** ツールを使用します。

    - 特定の期間または 1 つのデータ系列を **選択** または **クリック** します。
    - 選択した期間の中で、ズームしたりイベントを調査したりできます。
    - データ系列の中で、系列を別の列で分割したり、系列を新しい期間として追加したり、選択した系列だけを表示したり、選択した系列を除外したり、その系列をピン留めしたり、選択した系列からイベントを調査したりできます。
    - グラフの左にあるフィルター領域では、表示されているすべてのデータ系列を確認し、値または名前で並べ替えることができます。 また、すべてのデータ系列、またはピン留めされた系列やピン留めされていない系列を表示することもできます。 1 つのデータ系列を選択してその系列を別の列で分割したり、系列を新しい期間として追加したり、選択された系列のみを表示したり、選択された系列を除外したり、その系列をピン留めしたり、選択された系列のイベントを調査したりできます。
    - 複数の期間を同時に表示する場合は、あるデータ系列に関する追加データを積み重ねたり、積み重ねを解除したり、確認したり、すべての期間にわたって同じ Y 軸を使用したりできます。 グラフの右上隅にあるボタンを使用します。

    [![グラフ ツールの右上隅のオプション設定](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. 特定のクエリ内の固有のデータ系列や異常なデータ系列をすばやく見つけるには、**ヒートマップ** を使用します。 ヒートマップとして視覚化できるのは 1 つの検索期間だけです。

    [Time Series Insights Explorer のヒートマップ グラフ](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. 選択または右クリックしてイベントを調査する場合は、 **[イベント]** パネルが使用可能になります。 ここでは、すべての未加工のイベントを確認したり、イベントを JSON または CSV ファイルとしてエクスポートしたりできます。 Azure Time Series Insights により、すべての生データが格納されます。

    [![イベント](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. イベントを調査した後に **[統計]** タブを選択して、パターンと列の統計を公開します。

    - **[パターン]** : この機能は、選択されたデータ リージョン内の最も統計的に有意なパターンを予防的に表面化します。 どのようなパターンに最も多くの時間やエネルギーが必要かを把握するために数千のイベントを調べる必要はありません。 Azure Time Series Insights を使用すると、これらの統計的に有意なパターンに直接移動して、引き続き分析を実行できます。 この機能は、履歴データの事後分析にも役立ちます。
    - **[列の統計]** :列の統計では、選択された期間にわたる選択されたデータ系列の各列のデータを分類するグラフやテーブルが提供されます。

      [![列の統計のグラフ作成とオプション](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

ここでは、Azure Time Series Insights Explorer Web アプリで使用できる主な機能、構成設定、および表示オプションについて学習しました。

## <a name="next-steps"></a>次のステップ

- Azure Time Series Insights 環境で[問題を診断して解決する](time-series-insights-diagnose-and-solve-problems.md)方法を学習します。

- [Azure Time Series Insights クイック スタート](time-series-quickstart.md)のガイド ツアーを実行します。