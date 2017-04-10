---
title: "Azure サービス アラートの作成 - Azure Portal | Microsoft Docs"
description: "指定した条件が満たされたときに電子メール、通知、Websites URL (webhook) の呼び出し、またはオートメーションをトリガーします。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 530cbb7b53316324f5bc5bcbe9b1b73b9f0b0f9b


---
# <a name="create-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>Azure Monitorでの Azure サービス アラートの作成 - Azure Portal
> [!div class="op_single_selector"]
> * [ポータル](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>概要
この記事では、Azure ポータルを使用して Azure アラートを設定する方法について説明します。   

監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

* **メトリック値** - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。    
* **アクティビティ ログ イベント** - アラートは、" *すべて* " のイベントに対して、または特定数のイベントが発生したときにのみトリガーされます。

アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービスの管理者/共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す
* Azure Runbook の実行を開始する (Azure ポータルからのみ)

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します

* [Azure ポータル](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [コマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure ポータルでメトリックにアラート ルールを作成する
1. [ポータル](https://portal.azure.com/)で、監視するリソースを見つけて選択します。
2. [監視] セクションで、**[アラート]** または **[アラート ルール]** を選択します。 テキストとアイコンは、リソースごとに多少異なる場合があります。  

    ![監視](./media/insights-alerts-portal/AlertRulesButton.png)
3. **[アラートの追加]** コマンドを選択し、フィールドに入力します。

    ![[アラートの追加]](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)
4. アラート ルールに**名前**を付けて、**説明**を選択します。この説明は通知電子メールにも表示されます。
5. 監視する**メトリック**を選択し、メトリックの**条件**と**しきい値**を選択します。 また、 **[期間]** では、どのくらいの期間メトリック ルールが満たされた後、アラートがトリガーされるかを選択します。 たとえば、期間として [PT5M] を使用すると、アラートは 80% を超える CPU を見つけて、その CPU が 5 分間継続して 80% を超えた時点でトリガーされます。 最初のトリガーが発生したら、次のアラートは、CPU が 5 分間継続して 80% を下回ったときにトリガーされます。 CPU 測定は 1 分ごとに発生します。   
6. アラートが発生したときに管理者と共同管理者に電子メールが送信されるようにするには、 **[メールの所有者...]** をオンにします。
7. アラートが発生したときに、他のアドレスにも電子メールを送信して通知する場合は、 **[追加する管理者の電子メール]** フィールドにそのアドレスを入力します。 複数の電子メール アドレスを入力する場合はセミコロンで区切ります- *email@contoso.com;email2@contoso.com*
8. **[webhook]** フィールドに、アラートが発生したときに呼び出す webhook の有効な URI を入力します。
9. Azure Automation を使用する場合は、アラートが発生したときに実行する Runbook を選択できます。
10. 完了したら **[OK]** を選択して、アラートを作成します。   

数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="managing-your-alerts"></a>アラートの管理
アラートを作成して選択したら、次の操作を行うことができます。

* メトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* 編集または削除する。
* そのアラートの受信を一時的に停止または再開する必要がある場合に、そのアラートを**無効**または**有効**にする。

## <a name="next-steps"></a>次のステップ
* [Azure での監視の概要](monitoring-overview.md) 情報を入手します。
* [アラートでの webhook の構成](insights-webhooks-alerts.md)に関する詳細情報を確認します。
* [Azure Automation Runbooks](../automation/automation-starting-a-runbook.md)の詳細情報を確認します。
* [診断ログの概要](monitoring-overview-of-diagnostic-logs.md) 情報を入手し、サービスに関する詳細な頻度の高いメトリックを収集します。
* [メトリック収集の概要](insights-how-to-customize-monitoring.md) 情報を入手して、サービスの可用性と応答性を確認します。



<!--HONumber=Jan17_HO5-->


