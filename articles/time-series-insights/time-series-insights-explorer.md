---
title: Azure Time Series Insights エクスプローラーを使用してデータを調査する | Microsoft Docs
description: この記事では、Web ブラウザーで Azure Time Series Insights エクスプローラーを使用して、ビッグ データの全体像をすばやく表示したり、IoT 環境を検証したりする方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: aa04b2508f89b9658d904624e884bf9133caf3b7
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299557"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights エクスプローラー

この記事では、Azure Time Series Insights [エクスプローラー Web アプリ](https://insights.timeseries.azure.com/)の一般提供版にある機能とオプションについて説明します。 Time Series Insights エクスプローラーは、サービスによって提供される強力なデータ可視化機能を備え、ご利用の環境からアクセスすることができます。

Azure Time Series Insights は、数十億件の IoT イベントを同時に簡単に調査および分析できるようにする、フル マネージドの分析、ストレージ、および視覚化サービスです。 データの全体像が示され、これを使用してすばやく IoT ソリューションを検証したり、ミッション クリティカルなデバイスに発生するコストのかかるダウンタイムを回避したりできます。 ほぼリアルタイムで隠れた傾向を発見したり、異常を特定したり、根本原因分析を実施したりできます。 現在、Time Series Insights エクスプローラーはパブリック プレビュー段階にあります。

> [!TIP]
> デモンストレーション環境を使用したガイド ツアーについては、[Azure Time Series Insights のクイック スタート](time-series-quickstart.md)を参照してください。

## <a name="video"></a>ビデオ

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Time Series Insights エクスプローラーを使用したデータのクエリについて説明します。 </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>先行するビデオ、<a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">Azure IoT ソリューション アクセラレータを使用した Time Series Insights の概要</a>を見てください。

## <a name="prerequisites"></a>前提条件

Time Series Insights エクスプローラーを使用するための前提条件は、以下のとおりです。

- Time Series Insights 環境を作成します。 詳細については、[Time Series Insights の使用を開始する方法](./time-series-insights-get-started.md)に関するページを参照してください。
- 環境内でお使いのアカウントへの[アクセス権を与えます](time-series-insights-data-access.md)。
- それに [IoT ハブ](time-series-insights-how-to-add-an-event-source-iothub.md)または[イベント ハブ](time-series-insights-how-to-add-an-event-source-eventhub.md)のイベント ソースを追加します。

## <a name="explore-and-query-data"></a>データを調査しクエリを実行する

イベント ソースを Time Series Insights 環境に接続してから数分以内に、時系列データを調査し、クエリを実行できます。

1. 開始するには、Web ブラウザーで [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com/)を開きます。 ウィンドウの左側で、環境を選択します。 アクセスできるすべての環境がアルファベット順に一覧表示されます。

1. 環境を選択したら、一番上にある **[From] (開始)** と **[To] (終了)** の構成を使用するか、または必要な期間をクリックしてドラッグします。 右上隅にある虫眼鏡を選択するか、または選択された期間を右クリックして **[検索]** を選択します。

1. **[Auto On] (自動オン)** ボタンを選択して、1 分ごとに自動的に可用性を更新することもできます。 **[Auto On] (自動オン)** ボタンは、メインの視覚化の内容ではなく、可用性チャートにのみ適用されます。

1. Azure Cloud アイコンをクリックすると、Azure Portal の環境が表示されます。

   [![Time Series Insights 環境](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. 次に、選択した期間内のすべてのイベントの数を示すグラフが表示されます。 ここでは多くのことを制御できます。

    - **期間編集パネル**: 期間のスペースで環境に対するクエリを実行します。 これは画面の左側にあります。
      - **[メジャー]** :このドロップダウン リストには、すべての数値列 (**Double**) が表示されます。
      - **分割基準**:このドロップダウン リストには、カテゴリ列 (**String**) が表示されます。
      - **[メジャー]** の横にあるコントロール パネルから、ステップ補間を有効にしたり、最小値と最大値を表示したり、Y 軸を調整したりできます。 また、データの数、平均、または合計のいずれを表示するかを調整することもできます。
      - 最大 5 つの期間を追加して同じ X 軸に表示できます。 **[Copy-down] (コピー ダウン)** ボタンを使用して期間を追加するか、または **[追加]** を選択して新しい期間を追加します。

        [![期間編集パネル](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **[述語]** :次の表に示されているオペランドのセットを使用してイベントをすばやくフィルター処理するには、述語を使用します。 選択またはクリックして検索を実行すると、その検索に基づいて述語が自動的に更新されます。 サポートされるオペランドの型には以下が含まれます。

         |Operation  |サポートされている型  |メモ  |
         |---------|---------|---------|
         |`<`、`>`、`<=`, `>=`     |  Double、DateTime、TimeSpan       |         |
         |`=`、`!=`、`<>`     | String、Bool、Double、DateTime、TimeSpan、NULL        |         |
         |IN     | String、Bool、Double、DateTime、TimeSpan、NULL        |  すべてのオペランドは同じ型か NULL 定数である必要があります。        |
         |HAS     | string        |  右側には定数文字列リテラルのみが許可されます。 空の文字列や NULL は許可されません。       |

      - **クエリの例**

         [![クエリの例](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. **間隔サイズ** スライダー ツールを使用すると、同じ期間にわたる間隔を拡大および縮小できます。 このスライダーにより、滑らかな傾向を示す大きな時間スライスから、ミリ秒単位の小さなスライスまでの動きのより正確な制御が可能になるため、きめ細かな高解像度のデータ片を表示できるようになります。 このスライダーの既定の開始点は、解像度、クエリ速度、および細分性のバランスがとれた、選択されたデータの最適なビューとして設定されます。

1. **タイム ブラシ** ツールにより、ある期間から別の期間への移動が簡単になります。

1. 現在のクエリを保存し、それを環境の他のユーザーと共有するには、 **[保存]** コマンドを使用します。 **[開く]** を使用すると、保存されたすべてのクエリと、アクセスできる環境内の他のユーザーのすべての共有クエリを表示できます。

   [![クエリ](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>データの視覚化

1. 最大 4 つの固有のクエリを同時に表示するには、**パースペクティブ ビュー** ツールを使用します。 **[Perspective View] (パースペクティブ ビュー)** ボタンは、グラフの右上隅にあります。

   [![パースペクティブ ビュー](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. グラフを表示してデータを視覚的に調査し、**グラフ** ツールを使用します。

    - 特定の期間または 1 つのデータ系列を**選択**または**クリック**します。
    - 選択した期間の中で、ズームしたりイベントを調査したりできます。
    - データ系列の中で、系列を別の列で分割したり、系列を新しい期間として追加したり、選択した系列だけを表示したり、選択した系列を除外したり、その系列をピン留めしたり、選択した系列からイベントを調査したりできます。
    - グラフの左にあるフィルター領域では、表示されているすべてのデータ系列を確認し、値または名前で並べ替えることができます。 また、すべてのデータ系列、またはピン留めされた系列やピン留めされていない系列を表示することもできます。 1 つのデータ系列を選択してその系列を別の列で分割したり、系列を新しい期間として追加したり、選択された系列のみを表示したり、選択された系列を除外したり、その系列をピン留めしたり、選択された系列のイベントを調査したりできます。
    - 複数の期間を同時に表示する場合は、あるデータ系列に関する追加データを積み重ねたり、積み重ねを解除したり、表示したり、すべての期間にわたって同じ Y 軸を使用したりできます。 グラフの右上隅にあるボタンを使用します。

    [![グラフ ツール](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. 特定のクエリ内の固有のデータ系列や異常なデータ系列をすばやく見つけるには、**ヒートマップ**を使用します。 ヒートマップとして視覚化できるのは 1 つの検索期間だけです。

    [![ヒートマップ](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. 選択または右クリックしてイベントを調査する場合は、 **[イベント]** パネルが使用可能になります。 ここでは、すべての未加工のイベントを表示したり、イベントを JSON または CSV ファイルとしてエクスポートしたりできます。 Time Series Insights により、すべての生データが格納されます。

    [![イベント](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. イベントを調査した後に **[統計]** タブを選択して、パターンと列の統計を公開します。

    - **[パターン]** : この機能は、選択されたデータ リージョン内の最も統計的に有意なパターンを予防的に表面化します。 どのようなパターンに最も多くの時間やエネルギーが必要かを把握するために数千のイベントを調べる必要はありません。 Time Series Insights を使用すると、これらの統計的に有意なパターンに直接移動して、引き続き分析を実行できます。 この機能は、履歴データの事後分析にも役立ちます。
    - **[列の統計]** :列の統計では、選択された期間にわたる選択されたデータ系列の各列のデータを分類するグラフやテーブルが提供されます。

      [![統計](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

ここまで、Time Series Insights エクスプローラー Web アプリ内で使用できるさまざまな機能やオプションを見てきました。

## <a name="next-steps"></a>次の手順

- Time Series Insights 環境で[問題を診断して解決する](time-series-insights-diagnose-and-solve-problems.md)方法を学習します。

- [Azure Time Series Insights クイック スタート](time-series-quickstart.md)のガイド ツアーを実行します。
