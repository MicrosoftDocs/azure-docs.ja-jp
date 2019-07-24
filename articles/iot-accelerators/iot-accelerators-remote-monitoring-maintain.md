---
title: リモート監視ソリューションでアラートを使用し、デバイスの問題を修正するチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、アラートを使用して、リモート監視ソリューション アクセラレータに接続されているデバイスの問題を特定し、修正する方法を示します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1cd1eb9a0bd4b8457ea82303a747acb2553ab707
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451724"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>チュートリアル:デバイスの問題のトラブルシューティングと修正

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して、接続されている IoT デバイスの問題を特定し、修正します。 ソリューション アクセラレータ ダッシュボードでアラートを使用して問題を特定した後、リモート ジョブを実行して問題を修正します。

Contoso は、フィールド内の新しい**プロトタイプ** デバイスをテストしています。 Contoso の運用者であるあなたは、テスト中に**プロトタイプ** デバイスがダッシュボード上で予期せぬ温度アラートをトリガーしていることに気付きます。 今すぐこの異常のある**プロトタイプ** デバイスの動作を調査し、問題を解決する必要があります。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * デバイスからのアラートを調査する
> * デバイスの問題を解決する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>アラートの調査

**[ダッシュボード]** ページに、**プロトタイプ** デバイスに関連付けられたルールから生じる予期しない温度アラートがあることに気付きます。

[![ダッシュボードに表示されているアラート](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

問題をさらに調査するため、アラートの横にある **[Explore Alert] (アラートの確認)** オプションを選択します。

[![ダッシュボードでアラートを確認する](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

アラートの詳細ビューには、以下が表示されます。

* アラートがトリガーされた時間
* アラートに関連付けられているデバイスに関する状態の情報
* アラートに関連付けられているデバイスのテレメトリ

[![アラートの詳細](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

アラートを確認するには、**発生したアラート**をすべて選択し、**[Acknowledge]\(確認\)** を選択します。 このアクションにより、他のオペレーターは、アラートが確認済みであり、対応中であることを知ることができます。

[![アラートを確認する](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

アラートを確認すると、発生の状態は **[承認済み]** に変わります。

アラートが発生したデバイスの一覧では、温度アラートを発生させた**プロトタイプ** デバイスを表示できます。

[![アラートの原因となったデバイスを一覧表示する](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>問題の解決

**プロトタイプ** デバイスの問題を解決するには、デバイスで **DecreaseTemperature** メソッドを呼び出す必要があります。

デバイスに対処するには、アラートが発生したデバイスの一覧でデバイスを選択して、**[ジョブ]** を選択します。 **プロトタイプ** デバイス モデルでは、次の 6 つのメソッドがサポートされています。

[![デバイスでサポートされているメソッドを表示する](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

**DecreaseTemperature** を選択して、ジョブの名前を **DecreaseTemperature** に設定します。 次に、**[適用]** をクリックします。

[![温度を低下させるジョブを作成する](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

ジョブの状態を追跡するには、**[View job status]\(ジョブの状態の表示\)** をクリックします。 **[ジョブ]** ビューを使用して、ソリューション内のすべてのジョブとメソッドの呼び出しを追跡します。

[![温度を低下させるジョブを監視する](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

**ダッシュボード** ページでテレメトリを表示すると、デバイスの温度が低下していることを確認できます。

[![温度の低下を確認する](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アラートを使用してデバイスの問題を特定する方法と、対象のデバイスを操作して問題を解決する方法について説明しました。 実デバイスをソリューション アクセラレータに接続する方法を学習するには、ハウツー記事に進んでください。

デバイスの問題を管理する方法を学習した後は、次のステップとして、[デバイスをリモート監視ソリューション アクセラレータに接続する](iot-accelerators-connecting-devices.md)方法を学習することをお勧めします。
