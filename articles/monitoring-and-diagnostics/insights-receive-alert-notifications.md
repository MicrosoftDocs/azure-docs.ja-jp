---
title: "Azure サービスのアラート通知を受け取る |Microsoft Docs"
description: "アラート ルールの条件を満たすと通知されます。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: bc2fa75e-bc26-474e-af43-de30de5e604f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2015
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5c929849eb71d5ebace7ef274700626d4c5e5f3e


---
# <a name="receive-alert-notifications"></a>アラート通知の受信
監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

メトリック値のアラート ルールでは、指定したメトリックの値が割り当てられたしきい値を超えると、アラート ルールがアクティブになり、通知を送信できます。 イベントのアラート ルールでは、ルールはイベント *ごと* 、または特定のイベント数が発生した場合のみ、通知を送信できます。

アラート ルールを作成するときに、サービス管理者と共同管理者、または指定した別の管理者にメール通知を送信するオプションを選択できます。 ルールにアクティブになり、アラート条件が解決されると、メールによる通知が送信されます。

[REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) または [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) を使用して構成し、アラート ルールに関する情報をプログラムで取得できます。

## <a name="create-an-alert-rule"></a>アラート ルールを作成する
1. [ポータル](https://portal.azure.com/)で、 **[参照]**をクリックし、監視するリソースをクリックします。
2. **[操作]** レンズ内で、**[アラート ルール]** タイルをクリックします。
3. **[追加アラート]** コマンドをクリックします。
   
    ![[追加アラート]](./media/insights-receive-alert-notifications/Insights_AddAlert.png)
4. アラート ルールに名前を付け、通知メールに表示される説明を選択できます。
5. **[メトリック]** を選択する場合は、メトリックの条件としきい値を選択します。 これは、Azure がアラート アクティビティの監視とプロットに使用する期間です。
   
    ![条件としきい値](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)
6. **[イベント]** を選択することもできます。この場合は、特定のイベントが発生すると通知を受け取ります。
   
    ![イベント](./media/insights-receive-alert-notifications/Insights_Events.png)
7. 最後に、担当の管理者にメール通知を送信できます。

**[保存]**をクリックしてから数分以内で、選択したメトリックがしきい値を超えたかどうかが通知されます。

## <a name="managing-your-alert-rules"></a>アラート ルールの管理
アラート ルールが作成されると、過去のメトリックと比較したアラートしきい値のプレビューが表示されます。

![イベント](./media/insights-receive-alert-notifications/Insights_EditAlert.png)

このアラート ルールはもちろん編集できます。通知の受信を一時的に停止する場合は、**[無効]** または **[有効]** にできます。

## <a name="next-steps"></a>次のステップ
* [アラートに Webhook を構成して](insights-webhooks-alerts.md) 多様なチャネルに通知をルーティングする
* [サービス メトリックスを監視](insights-how-to-customize-monitoring.md) して、サービスの可用性と応答性を確認します。
* [監視と診断を有効](insights-how-to-use-diagnostics.md) にしてサービスに関する詳細な頻度の高いメトリックを収集します。
* [Web ページの可用性と応答性を監視](../application-insights/app-insights-monitor-web-app-availability.md) すると、ページがダウンしているかどうかを検出できます。
* [アプリケーションのパフォーマンスを監視](../application-insights/app-insights-azure-web-apps.md) します。
* [イベントとアクティビティ ログを表示](insights-debugging-with-events.md)して、サービスで発生したすべてのことを理解します。
* [サービス正常性を追跡](insights-service-health.md) して、Azure でパフォーマンスの低下やサービスの中断が発生したことを検出します。




<!--HONumber=Nov16_HO3-->


