---
title: "Microsoft Azure と Azure Monitor のアラートの概要 | Microsoft Docs"
description: "アラートを使用すると、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知を受けることができます。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afa863e2a900d4f823b77453d92f034db7d5a93f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure のアラートの概要
この記事では、Microsoft Azure のさまざまなソースのアラート、アラートの目的、アラートの利点、アラートとの基本的な使用方法について説明します。 特に Azure Monitor について説明しますが、アラートを使用する他のサービスの参照先も紹介します。 アラートは Azure の監視方法の 1 つです。データに対する条件を構成し、その条件が最新の監視データと一致したときに通知を受信できます。

## <a name="taxonomy-of-azure-alerts"></a>Azure のアラートの分類
Azure では、アラートとその機能を説明するときに次の用語を使用します。
* **アラート** - 一致したときにアクティブになる条件 (1 つまたは複数の規則または条件) の定義。
* **アクティブ** - アラートに定義されている条件と一致するときの状態。
* **解決済み** - アラートに定義されている条件と以前は一致していたが、一致しなくなった状態。
* **通知** - アラートがアクティブになったことに基づいて実行されるアクション。
* **アクション** - 通知の受信側に送信される特定の呼び出し (たとえば、アドレスへの電子メール送信や webhook URL への投稿など)。 通常、通知によって複数のアクションがトリガーされる可能性があります。

## <a name="alerts-in-different-azure-services"></a>さまざまな Azure サービスのアラート
アラートは複数の Azure 監視サービス全体で使用できます。 Azure 監視サービスの使用方法と使用する場合については、[こちらの記事を参照してください](./monitoring-overview.md)。 Azure 全体で使用できるアラートの種類の内訳を次に示します。

| サービス | アラートの種類 | サポートされているサービス | Description |
|---|---|---|---|
| Azure Monitor | [メトリック アラート](./insights-alerts-portal.md) | [Azure Monitor からサポートされるメトリック](./monitoring-supported-metrics.md) | プラットフォームレベルのメトリックが特定の条件 (たとえば、VM の CPU % が過去 5 分間で 90 を超えた、など) と一致する場合に通知を受け取ります。 |
|Azure Monitor | [ほぼリアルタイムのメトリック アラート (プレビュー)](./monitoring-near-real-time-metric-alerts.md)| [Azure Monitor からサポートされるリソース](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | 1 つまたは複数のプラットフォーム レベルのメトリックが指定された条件 (例: 過去 5 分間の VM の CPU % が 90 を超え、Network In が 500 MB を超えている) を満たしている場合に、メトリック アラートよりも早く通知を受け取ります。 |
| Azure Monitor | [アクティビティ ログ アラート](./monitoring-activity-log-alerts.md) | Azure Resource Manager で使用できるすべてのリソースの種類 | [Azure アクティビティ ログ](./monitoring-overview-activity-logs.md)の新しいイベントが特定の条件 (たとえば、myProductionResourceGroup で "VM の削除" 操作が発生した場合や、状態が "アクティブ" の新しいサービス正常性イベントが表示された場合など) と一致する場合に通知を受け取ります。 |
| Application Insights | [メトリック アラート](../application-insights/app-insights-alerts.md) | Application Insights にデータを送信するようにインストルメント化されたすべてのアプリケーション | アプリケーションレベルのメトリックが特定の条件 (たとえば、サーバーの応答時間が 2 秒を超える、など) と一致した場合に通知を受け取ります。 |
| Application Insights | [Web テスト アラート](../application-insights/app-insights-monitor-web-app-availability.md) | Application Insights にデータを送信するようにインストルメント化されたすべての Web サイト | Web サイトの可用性または応答性が期待を下回る場合に通知を受け取ります。 |
| Log Analytics | [Log Analytics アラート](../log-analytics/log-analytics-alerts.md) | データを Log Analytics に送信するように構成されたすべてのサービス | メトリックやイベント データに対する Log Analytics 検索クエリが特定の条件と一致する場合に通知を受け取ります。 |

## <a name="alerts-on-azure-monitor-data"></a>Azure Monitor データのアラート
Azure Monitor から使用できるデータには、メトリック アラート、ほぼリアルタイムのメトリック アラート (プレビュー)、およびアクティビティ ログ アラートという 3 種類のアラートがあります。

* **メトリック アラート** - このアラートは、指定したメトリックの値が、割り当てたしきい値を超えたときにトリガーされます。 このアラートで通知が生成されるのは、アラートが "アクティブ化済み" になったとき (しきい値を超え、アラートの条件を満たしたとき) と、"解決済み" になったとき (しきい値を再び超え、条件を満たさなくなったとき) です。 Azure Monitor がサポートするメトリックは増え続けています。使用できるメトリックの一覧については、[Azure Monitor でサポートされているメトリックの一覧](monitoring-supported-metrics.md)を参照してください。
* **ほぼリアルタイムのメトリック アラート (プレビュー)**: このアラートは、メトリック アラートに似ていますが、いくつかの点が異なります。 まず、名前が示すように、このアラートは、ほぼリアルタイム (1 分) でトリガーできます。 複数 (現時点では 2 つ) のメトリックの監視もサポートします。  このアラートで通知が生成されるのは、アラートが "アクティブ化" されたとき (各メトリックが同時にしきい値を上回り、かつアラートの条件が満たされたとき) と、アラートが "解決された" とき (少なくとも 1 つのメトリックがしきい値を下回り、条件を満たさなくなったとき) です。

> [!NOTE]
> ほぼリアルタイムのメトリック アラートは、現在パブリック プレビュー段階です。 機能やユーザー エクスペリエンスは変更されることがあります。
>
>

* **アクティビティ ログ アラート** - 割り当てたフィルター条件と一致するアクティビティ ログ イベントが生成されたときにトリガーされるストリーミング ログ アラート。 すべての新規イベントには、アラート エンジンによってフィルター条件が適用されるだけなので、これらのアラートの状態は "アクティブ化済み" のみです。 これらのアラートを使用して、新しいサービス正常性インシデントが発生したとき、またはユーザーまたはアプリケーションがサブスクリプションで操作 (仮想マシンの削除など) を実行したときに通知を受け取ることができます。

Azure Monitor で使用できる診断ログ データの場合、データを Log Analytics にルーティングし、Log Analytics アラートを使用することをお勧めします。 次の図は、Azure Monitor のデータのソースと、そのデータからアラートを生成する方法をまとめたものです。

![アラートについて説明します。](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Azure Monitor アラートについて通知を受け取る方法
これまで、各サービスの Azure アラートは、それぞれ独自の組み込み通知方法を使用していました。 これからは、Azure Monitor でアクション グループという再利用可能な通知グループを使用できます。 アクション グループには、通知の受信者セット (任意の数の電子メール アドレス、電話番号 (SMS の場合)、または webhook の URL) を指定します。アクション グループを参照するアラートがアクティブになると、すべての受信者がその通知を受け取ります。 そのため、多数のアラート オブジェクト全体で受信者のグループ (たとえば、オン コール エンジニア一覧など) を再利用できます。 現時点では、アクション グループを利用できるのはアクティビティ ログ アラートのみですが、他の Azure アラートの種類もアクション グループの使用に取り組んでいます。

アクション グループは、電子メール アドレスや SMS 番号に加え、webhook URL への投稿による通知をサポートしています。 そのため、たとえば自動化や修復に以下を使用できます。
    - Azure Automation Runbook
    - Azure 関数
    - Azure Logic App
    - サードパーティのサービス

ほぼリアルタイムのメトリック アラート (プレビュー) とアクティビティ ログ アラートでは、アクション グループを使用します。

メトリック アラートでは、まだアクション グループを使用していません。 個々のメトリック アラートでは、以下のように通知を構成することができます。
* メール通知をサービス管理者、共同管理者、または指定した追加のメール アドレスに送信する。
* webhook を呼び出す。これにより、追加のオートメーション アクションを実行できます。

## <a name="next-steps"></a>次のステップ
アラート ルールとその構成方法については、以下をご覧ください。

* [メトリック](monitoring-overview-metrics.md)の詳細
* [Azure Portal からのメトリック アラートク](insights-alerts-portal.md)の構成
* [メトリック アラート PowerShell](insights-alerts-powershell.md) の構成
* [メトリック アラート コマンドライン インターフェイス (CLI)](insights-alerts-command-line-interface.md) の構成
* [メトリック アラート Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) の構成
* [アクティビティ ログ](monitoring-overview-activity-logs.md) の詳細
* [Azure Portal からのアクティビティ ログ アラート](monitoring-activity-log-alerts.md)の構成
* [Resource Manager からのアクティビティ ログ アラート](monitoring-create-activity-log-alerts-with-resource-manager-template.md)の構成
* [アクティビティ ログ アラート webhook スキーマ](monitoring-activity-log-alerts-webhook.md) の確認
* [ほぼリアルタイムのメトリック アラート](monitoring-near-real-time-metric-alerts.md)
* [サービス通知](monitoring-service-notifications.md) の詳細
* [アクション グループ](monitoring-action-groups.md)の詳細については、こちらをご覧ください。
