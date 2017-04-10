---
title: "Microsoft Azure と Azure Monitor のアラートの概要 | Microsoft Docs"
description: "アラートを使用すると、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知を受けることができます。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: c9065c3346c924ea21060c9e7e5b297a3cb26941
ms.lasthandoff: 03/09/2017


---
# <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure のアラートの概要
この記事では、アラートの概要、利点、使用方法について説明します。 特に Azure Monitor について述べますが、他のサービスについての情報も提供します。  

アラートとは、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知するための手段です。  

## <a name="alerts-in-different-azure-services"></a>さまざまな Azure サービスのアラート
アラートは、次のようなさまざまなサービスで使用できます。

* **Application Insights**: Web テストとメトリックのアラートを有効にします。 「[Application Insights のアラートの設定](../application-insights/app-insights-alerts.md)」と「[Web サイトの可用性と応答性の監視](../application-insights/app-insights-monitor-web-app-availability.md)」をご覧ください。
* **Log Analytics (Operations Management Suite)**: アクティビティ ログと診断ログの Log Analytics へのルーティングを有効にします。 Operations Management Suite では、メトリック、ログなどのアラートの種類が許可されています。 詳細については、「 [Log Analytics のアラート](../log-analytics/log-analytics-alerts.md)」を参照してください。  
* **Azure Monitor**: メトリック値とアクティビティ ログ イベントの両方に基づいてアラートを有効にします。 [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) を使用して、アラートを管理できます。  詳細については、[Azure Portal、PowerShell、またはコマンド ライン インターフェイスでのアラートの作成](insights-alerts-portal.md)に関するページをご覧ください。

## <a name="visual-summary"></a>概要図
次の図は、アラートの説明と、特に "Azure Monitor" においてアラートでできることの概要を示しています。 これ以外のアクションは、前に記載したサービスで使用できる可能性があります。 たとえば、現在、診断ログのアラートは Log Analytics でのみ使用可能です。

![アラートについて説明します。](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="what-can-trigger-alerts-in-azure-monitor"></a>Azure Monitor でアラートをトリガーする条件

アラートは、以下の値とイベントに基づいて受け取ることができます。

* **メトリック値**: アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。 Azure Monitor がサポートするメトリックは増え続けています。使用できるメトリックの一覧については、[Azure Monitor でサポートされているメトリックの一覧](monitoring-supported-metrics.md)を参照してください。
* **アクティビティ ログ イベント**: アラートは、すべてのイベントに対して、または発生したイベントが特定の数に達したときにのみトリガーされます。

## <a name="what-can-alerts-do"></a>アラートでできること
次のアクションを実行するようにアラートを構成することができます。

* メール通知をサービス管理者、共同管理者、または指定した追加のメール アドレスに送信する。
* webhook を呼び出す。これにより、追加のオートメーション アクションを実行できます。 たとえば、次のものの呼び出しが可能です。
    - Azure Automation Runbook
    - Azure 関数
    - Azure Logic App
    - サードパーティのサービス


## <a name="next-steps"></a>次のステップ
アラート ルールとその構成方法については、以下をご覧ください。

* [Azure ポータル](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [コマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

