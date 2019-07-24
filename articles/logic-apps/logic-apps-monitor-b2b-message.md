---
title: Azure Monitor ログを使用して B2B メッセージを監視する - Azure Logic Apps | Microsoft Docs
description: Azure Monitor ログを使用して、統合アカウントと Azure Logic Apps の AS2、X12、および EDIFACT メッセージを監視し、診断ログを設定します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: 12799a308157c3c0e19de1f82c0fe3df44fad37e
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195168"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Azure Logic Apps で Azure Monitor ログを使用して Azure B2B メッセージを監視する

統合アカウントで取引先間の B2B 通信を設定すると、これらの取引先が相互にメッセージを交換できるようになります。 この通信が予想どおりに動作していることを確認するために、[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)を使用して、AS2、X12、および EDIFACT メッセージを監視し、統合アカウントの診断ログを設定できます。 このサービスは、クラウド環境とオンプレミス環境を監視して、その可用性とパフォーマンスの管理を支援し、深いデバッグを行うためのランタイムの詳細とイベントの収集を実行します。 また、Azure Storage や Azure Event Hubs などの他のサービスでこのデータを使用することもできます。

> [!NOTE]
> このページには、[2019 年 1 月に廃止される](../azure-monitor/platform/oms-portal-transition.md) Microsoft Operations Management Suite (OMS) への言及がまだ残っている可能性がありますが、可能な場合には、これらの手順を Azure Log Analytics で置き換えてください。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

* 診断ログが設定されているロジック アプリ。 [ロジック アプリを作成する方法](quickstart-create-first-logic-app-workflow.md)および[そのロジック アプリのログを設定する方法](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)を参照してください。

* 上記の要件を満たしたら、Azure Monitor ログを使用して B2B 通信を監視および追跡するために使用する Log Analytics ワークスペースも必要になります。 Log Analytics ワークスペースがない場合は、[Log Analytics ワークスペースの作成方法](../azure-monitor/learn/quick-create-workspace.md)に関するページを参照してください。

* ロジック アプリにリンクされた統合アカウント。 [ロジック アプリにリンクする統合アカウントを作成する方法](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)に関する記事を参照してください。

## <a name="turn-on-diagnostics-logging"></a>診断ログの有効化

ログは、統合アカウントから直接有効にすることも、[Azure Monitor サービスを通じて](#azure-monitor-service)有効にすることもできます。 Azure Monitor は、インフラストラクチャ レベルのデータの基本的な監視を提供します。 詳細については、「[Azure Monitor の概要](../azure-monitor/overview.md)」を参照してください。

### <a name="turn-on-logging-from-integration-account"></a>統合アカウントからログ記録を有効にする

1. [Azure ポータル](https://portal.azure.com)で、統合アカウントを検索して選択します。 **[監視]** で **[診断設定]** を選択します。

   ![統合アカウントを検索して選択し、[診断設定] を選択します。](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. 次に、ご自分の統合アカウントを探して選択します。 フィルターの一覧で、ご自分の統合アカウントに適用する値を選択します。
完了したら、**[診断設定の追加]** を選択します。

   | プロパティ | 値 | 説明 | 
   |----------|-------|-------------|
   | **サブスクリプション** | <*Azure サブスクリプション名*> | 統合アカウントに関連付けられている Azure サブスクリプション | 
   | **リソース グループ** | <*Azure-resource-group-name*> | 統合アカウントの Azure リソース グループ | 
   | **リソースの種類** | **統合アカウント** | ログを有効にする Azure リソースの種類 | 
   | **リソース** | <*integration-account-name*> | ログを有効にする Azure リソースの名前 | 
   ||||  

   例: 

   ![統合アカウントで診断を設定する](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. 新しい診断設定の名前を指定し、Log Analytics ワークスペースとログに記録するデータを選択します。

   1. **[Log Analytics への送信]** を選択します。 

   1. **[Log Analytics]** で、**[構成]** を選択します。 

   1. **[OMS ワークスペース]** で、ログに使用する Log Analytics ワークスペースを選択します。 

      > [!NOTE]
      > OMS ワークスペースは、Log Analytics ワークスペースに置き換えられています。 

   1. **[ログ]** で、**[IntegrationAccountTrackingEvents]** カテゴリを選択し、**[保存]** を選択します。

   例:  

   ![診断データをログに送信できるように Azure Monitor ログを設定する](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. 次に、[Azure Monitor ログで B2B メッセージを追跡するための設定](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)を行います。

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Azure Monitor を通じてログ記録を有効にする

1. [Azure portal](https://portal.azure.com) の Azure メイン メニューで、**[監視]** を選びます。 **[設定]** で **[診断設定]** を選択します。 

   ![[監視]、[診断設定]、ご自分の統合アカウントの順に選択します。](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. 次に、ご自分の統合アカウントを探して選択します。 フィルターの一覧で、ご自分の統合アカウントに適用する値を選択します。
完了したら、**[診断設定の追加]** を選択します。

   | プロパティ | 値 | 説明 | 
   |----------|-------|-------------|
   | **サブスクリプション** | <*Azure サブスクリプション名*> | 統合アカウントに関連付けられている Azure サブスクリプション | 
   | **リソース グループ** | <*Azure-resource-group-name*> | 統合アカウントの Azure リソース グループ | 
   | **リソースの種類** | **統合アカウント** | ログを有効にする Azure リソースの種類 | 
   | **リソース** | <*integration-account-name*> | ログを有効にする Azure リソースの名前 | 
   ||||  

   例: 

   ![統合アカウントで診断を設定する](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. 新しい診断設定の名前を指定し、Log Analytics ワークスペースとログに記録するデータを選択します。

   1. **[Log Analytics への送信]** を選択します。 

   1. **[Log Analytics]** で、**[構成]** を選択します。 

   1. **[OMS ワークスペース]** で、ログに使用する Log Analytics ワークスペースを選択します。 

      > [!NOTE]
      > OMS ワークスペースは、Log Analytics ワークスペースに置き換えられています。 

   1. **[ログ]** で、**[IntegrationAccountTrackingEvents]** カテゴリを選択し、**[保存]** を選択します。

   例:  

   ![診断データをログに送信できるように Azure Monitor ログを設定する](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. 次に、[Azure Monitor ログで B2B メッセージを追跡するための設定](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)を行います。

## <a name="use-diagnostic-data-with-other-services"></a>診断データを他のサービスで使用する

Azure Monitor ログと併せて、ロジック アプリの診断データを他の Azure サービスで使用する方法を次のように拡張できます。 

* [Azure 診断ログを Azure Storage にアーカイブする](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Azure 診断ログを Azure Event Hubs にストリーミングする](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

これにより、[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) や [Power BI](../azure-monitor/platform/powerbi.md) などの他のサービスのテレメトリと分析を使用したリアルタイム監視が可能になります。 例: 

* [Event Hubs からStream Analytics にデータをストリーミングする](../stream-analytics/stream-analytics-define-inputs.md)
* [ストリーミング データを Stream Analytics で分析し、Power BI でリアルタイム分析ダッシュボードを作成する](../stream-analytics/stream-analytics-power-bi-dashboard.md)

設定するオプションに基づいて、[Azure ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md)または[Azure イベント ハブの作成](../event-hubs/event-hubs-create.md)を最初に行うようにしてください。 その後、診断データの送信先を選択できます。
保有期間は、ストレージ アカウントの使用を選択した場合にのみ適用されます。

![データを Azure ストレージ アカウントまたはイベント ハブに送信する](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>サポートされる追跡スキーマ

Azure では、次の種類の追跡スキーマをサポートしています。カスタム以外はすべて固定スキーマです。

* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>次の手順

* [Azure Monitor ログで B2B メッセージを追跡する](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Azure Monitor ログで B2B メッセージを追跡する")
* [Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")

