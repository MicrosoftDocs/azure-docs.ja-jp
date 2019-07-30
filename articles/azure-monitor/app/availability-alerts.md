---
title: Azure Application Insights を使用して可用性アラートを設定する | Microsoft Docs
description: Application Insights で Web テストを設定します。 Web サイトが使用できなくなったり、応答速度が低下したりした場合に、アラートを受け取ります。
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304751"
---
# <a name="availability-alerts"></a>可用性のアラート

[ Application Insights](../../azure-monitor/app/app-insights-overview.md) は、世界各地の複数のポイントから定期的にアプリケーションに Web 要求を送信します。 お使いのアプリケーションが応答していない場合、または応答が遅すぎる場合は、アラートを受信できます。

## <a name="enable-alerts"></a>アラートを有効にする

現在、アラートは既定で有効になっていますが、アラートを完全に構成するには、可用性テストを先に作成する必要があります。

![エクスペリエンスを作成する](./media/availability-alerts/create-test.png)

> [!NOTE]
>  [新しい統合アラート](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)の場合、アラート ルールの重大度と通知の基本設定[をアクション グループ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)と一緒に、アラート エクスペリエンスで構成する**必要があります**。 次の手順を行わないと、ポータル内通知を受け取るだけとなります。

1. 可用性テストを保存した後、詳細タブで、作成したテストの省略記号をクリックします。 [アラートの編集] をクリックします。

   ![保存後の編集](./media/availability-alerts/edit-alert.png)

2. 目的の重大度レベルとルールの説明を設定し、次に、最も重要なものとして、このアラート ルールに使用する、通知の基本設定が含まれているアクション グループを設定します。

   ![保存後の編集](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Y 箇所中 X 箇所に関するアラートでエラーが報告されている

新しい可用性テストを作成する場合、"Y 箇所中 X 箇所" アラート ルールは既定では[新しい統合アラート エクスペリエンス](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)で有効にされます。 オプトアウトするには、"クラシック" オプションを選択するか、またはアラート ルールを無効にすることを選択します。

> [!NOTE]
> 上記の手順に従って、アラートがトリガーされたときに通知を受信するようにアクション グループを構成します。 この手順を行わないと、ルールがトリガーされたとき、ポータル内通知を受け取るだけとなります。
>

### <a name="alert-on-availability-metrics"></a>可用性メトリックに関するアラート

[新しい統合アラート](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)を使用すると、セグメント化された可用性の集計メトリックおよびテスト継続期間メトリックに関するアラートを生成することもできます。

1. メトリック エクスペリエンス内で Application Insights リソースを選択し、可用性メトリックを選択します。

    ![可用性メトリックの選択](./media/availability-alerts/select-metric.png)

2. メニューからアラート オプションを選択すると、新しいエクスペリエンスが表示されます。そこでは、アラート ルールの設定対象とする特定のテストまたは場所を選択することができます。 ここでは、このアラート ルールに対してアクション グループを構成することもできます。

### <a name="alert-on-custom-analytics-queries"></a>カスタム分析クエリに関するアラート

[新しい統合アラート](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)を使用すると、[カスタム ログ クエリ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log)に関するアラートを生成することができます。 カスタム クエリを使用すると、可用性の問題を示す最も確かなシグナルを取得するのに役立つ任意の条件に基づいてアラートを生成することができます。 また、これは TrackAvailability SDK を使用してカスタムの可用性の結果を送信する場合、特に有効です。 

> [!Tip]
> 可用性データに関するメトリックにはカスタムの可用性の結果が含まれます。これは、Microsoft の TrackAvailability SDK を呼び出すことによって送信することができます。 メトリックに関するアラートのサポートを使用することにより、カスタムの可用性の結果に関するアラートを生成することができます。
>

## <a name="troubleshooting"></a>トラブルシューティング

専用の[トラブルシューティング](troubleshoot-availability.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

* [複数ステップ Web テスト](availability-multistep.md)
* [URL ping Web テスト](monitor-web-app-availability.md)

