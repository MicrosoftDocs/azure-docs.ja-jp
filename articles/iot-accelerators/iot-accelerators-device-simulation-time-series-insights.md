---
title: Time Series Insights を使用してデバイス シミュレーション テレメトリを視覚化する - Azure | Microsoft Docs
description: Time Series Insights 環境を構成して、デバイス シミュレーション ソリューション アクセラレータによって生成されたテレメトリを調査および分析する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: aea02cbde32d9485bd49ec39a6f300021c6ef927
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597700"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Time Series Insights を使用して、デバイス シミュレーション ソリューション アクセラレータから送信されたテレメトリを視覚化する

デバイス シミュレーション ソリューション アクセラレータを使用すると、シミュレートされたデバイスからテレメトリを生成して IoT ソリューションをテストできます。 このハウツー ガイドでは、Time Series Insights 環境を使用してシミュレートされたテレメトリを視覚化および分析する方法を示します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、アクティブな Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このハウツー ガイドの手順は、Azure サブスクリプションにデバイス シミュレーション ソリューション アクセラレータがデプロイされているものと想定しています。 ソリューション アクセラレータをまだデプロイしていない場合は、クイック スタート「[クラウドベースのデバイス シミュレーション ソリューションをデプロイして実行する](quickstart-device-simulation-deploy.md)」の手順を実行してください。

この記事では、ソリューション アクセラレータの名前を **contoso-simulation** と想定しています。 以下の手順を実行するときは、**contoso-simulation** を実際のソリューション アクセラレータの名前に置き換えてください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="start-a-simulation"></a>シミュレーションの開始

Time Series Insights エクスプローラーを使用する前に、いくつかのテレメトリを生成するようにデバイス シミュレーション ソリューション アクセラレータを構成します。 次のスクリーンショットは、10 台の冷却装置のシミュレーションを実行している状態を示しています。

![実行中のデバイス シミュレーション](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights エクスプローラー

Time Series Insights エクスプローラーは、テレメトリを視覚化するために使用できる Web アプリです。

1. Azure portal で、Time Series Insights の **[概要]** タブを選択します。

1. Time Series Insights エクスプローラー Web アプリを開くには、**[環境を開く]** をクリックします。

    ![Time Series Insights エクスプローラー](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. 時間選択パネルで、クイック タイム メニューから **[過去 30 分間]** を選択し、**[検索]** をクリックします。

    ![Time Series Insights エクスプローラーでの検索](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. 左側の条件パネルで、**[メジャー]** として **[温度]** を選択し、**[分割基準]** の値として **[iothub-connection-device-id]** を選択します。

    ![Time Series Insights エクスプローラーでのクエリ](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. グラフを右クリックし、**[イベントの探索]** を選択します。

    ![Time Series Insights エクスプローラーでのイベント](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. イベント データはグリッドに表示されます。

    ![Time Series Insights エクスプローラーでの表](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. パースペクティブ ビュー ボタンをクリックします。

    ![Time Series Insights エクスプローラーでのパースペクティブ](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. **[+]** をクリックして、パースペクティブに新しいクエリを追加します。

    ![Time Series Insights エクスプローラーでのクエリの追加](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. 時間間隔として **[過去 30 分間]** を選択し、**[メジャー]** として **[湿度]** を選択し、**[分割基準]** の値として **[iothub-connection-device-id]** を選択します。

    ![Time Series Insights エクスプローラーでのクエリ](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. パースペクティブ ビュー ボタンをクリックして、デバイス テレメトリ ダッシュボードを表示します。

    ![Time Series Insights エクスプローラーでのダッシュボード](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>次の手順

Time Series Insights エクスプローラーでのデータの探索とクエリ方法の詳細については、「[Azure Time Series Insights エクスプローラー](../time-series-insights/time-series-insights-explorer.md)」を参照してください。
