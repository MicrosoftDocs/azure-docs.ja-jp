---
title: Azure ソリューションから IoT デバイスを監視するチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して IoT デバイスを監視する方法を学習します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d6d850fa8f896809318be77529e10abddaf6ea9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58173823"
---
# <a name="tutorial-monitor-your-iot-devices"></a>チュートリアル:IoT デバイスの監視

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して、接続されている IoT デバイスを監視します。 ソリューション ダッシュボードを使用して、テレメトリ、デバイス情報、アラート、および KPI を表示します。

このチュートリアルでは、場所、速度、および貨物温度のテレメトリを送信する 2 つのシミュレートされたトラック デバイスを使用します。 トラックは Contoso という組織によって管理され、リモート監視ソリューション アクセラレータに接続されています。 Contoso のオペレーターとして、現場のトラックの 1 台 (truck-02) の場所と動きを監視する必要があります。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * ダッシュボードのデバイスをフィルターで絞り込む
> * テレメトリをリアルタイムで表示する
> * デバイスの詳細の表示
> * デバイスのアラートを表示する
> * システムの KPI を表示する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>表示するデバイスを選択する

**[ダッシュボード]** ページに表示する接続されたデバイスを選択するには、フィルターを使用します。 **[トラック]** デバイスのみを選択するには、フィルターのドロップダウン メニューで組み込みの **[トラック]** フィルターを選択します。

[![ダッシュボードに表示するトラックをフィルター処理する](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

フィルターを適用すると、フィルター条件に一致するデバイスのみがマップとテレメトリ パネルに表示されます。 truck-02 を含む 2 台のトラックがソリューション アクセラレータに接続されていることがわかります。

[![トラックのみがマップに表示される](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

フィルターを作成、編集、および削除するには、**[Manage device groups]\(デバイス グループの管理\)** をクリックします。

## <a name="view-real-time-telemetry"></a>テレメトリをリアルタイムで表示する

ソリューション アクセラレータでは、リアルタイムのテレメトリがグラフとして **[ダッシュボード]** ページに表示されます。 テレメトリ グラフの上部には、現在のフィルターによって選択されているデバイス (truck-02 を含む) で使用可能なテレメトリの種類が表示されます。 既定では、グラフにはトラックの緯度が表示されます。truck-02 は静止しているようです。

[![トラックのテレメトリの種類](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

トラックの温度テレメトリを表示するには、**[温度]** をクリックします。 truck-02 の温度が 1 時間でどのように変化したかを見ることができます。

[![トラックの温度テレメトリのプロット](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>マップの表示

マップには、現在のフィルターで選択されているシミュレートされたトラックに関する情報が表示されます。 マップを拡大または縮小して、場所の細部や広域を表示できます。 マップ上のデバイス アイコンの色は、デバイスに対して**アラート** (濃い青) または**警告** (赤) がアクティブであるかどうかを示します。 **アラート**と**警告**をまとめた数値がマップの左側に表示されます。

truck-02 の詳細を表示するには、マップをパンおよび拡大縮小してトラックを探し、マップ上でトラックを選択します。 次に、デバイス ラベルをクリックして **[デバイスの詳細]** パネルを開きます。 デバイスの詳細情報は次のとおりです。

* 最新のテレメトリの値
* デバイスがサポートしているメソッド
* デバイスのプロパティ

[![ダッシュボードにデバイスの詳細を表示する](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>アラートを表示する

**[アラート]** パネルには、デバイスからの最新の警告に関する詳細情報が表示されます。 truck-02 からの警告は、貨物温度が通常よりも高いことを示しています。

[![ダッシュボードにデバイスのアラートを表示する](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

フィルターを使用して最新のアラートの時間範囲を調整できます。 既定では、パネルには過去 1 時間のアラートが表示されます。

## <a name="view-the-system-kpis"></a>システムの KPI を表示する

ソリューション アクセラレータによって計算されたシステム KPI は、**[ダッシュボード]** ページの **[分析]** パネルに表示されます。

[![ダッシュボードの KPI](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

ダッシュボードには、現在のデバイスと期間フィルターによって選択されたアラートに対して、次の 3 つの KPI が表示されます。

* 最も多くのアラートをトリガーした規則のアクティブなアラートの数。
* デバイスの種類ごとのアラートの割合。
* 重大なアラートの割合。

truck-02 の場合、すべての警告は貨物温度が通常よりも高いという注意です。

アラートの時間間隔を設定し、表示するデバイスを制御する同じフィルターによって、KPI の集計方法が決まります。 既定では、過去 1 時間に集計された KPI がパネルに表示されます。

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リモート監視ソリューション アクセラレータの **[ダッシュボード]** ページを使用して、シミュレートされたトラックをフィルター処理および監視する方法について説明しました。 ソリューション アクセラレータを使用して接続されているデバイスの問題を検出する方法について学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [監視ソリューションに接続されているデバイスの問題の検出](iot-accelerators-remote-monitoring-automate.md)