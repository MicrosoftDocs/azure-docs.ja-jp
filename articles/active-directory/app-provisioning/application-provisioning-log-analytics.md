---
title: プロビジョニングを Azure Active Directory の Azure Monitor ログと統合する方法について説明します。
description: プロビジョニングを Azure Active Directory の Azure Monitor ログと統合する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f656f55b0c74103053f110b8c66645d353db9fbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561570"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>プロビジョニングを Azure Monitor ログと統合する方法の概要

プロビジョニングは、Azure Monitor ログおよび Log Analytics と統合されています。 Azure Monitoring を使用すると、ブック (ダッシュボードとも呼ばれます) の作成、プロビジョニング ログの 30 日以上の保存、カスタム クエリとアラートの作成などを行うことができます。 この記事では、プロビジョニング ログを Azure Monitor ログと統合する方法について説明します。 プロビジョニング ログの全般的なしくみの詳細については、[プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)に関するページを参照してください。

## <a name="enabling-provisioning-logs"></a>プロビジョニング ログの有効化

Azure Monitoring と Log Analytics については既に理解している必要があります。 そうでない場合は、別のページでそれらについて学習してから、このページに戻ってアプリケーションのプロビジョニング ログについて学習してください。 Azure Monitoring の詳細については、「[Azure Monitor の概要](../../azure-monitor/overview.md)」を参照してください。 Azure Monitor ログと Log Analytics の詳細については、「[Azure Monitor のログ クエリの概要](../../azure-monitor/logs/log-query-overview.md)」を参照してください。

Azure Monitoring を構成すると、アプリケーションのプロビジョニングのログを有効にすることができます。 このオプションは、 **[診断設定]** ページにあります。

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="診断設定へのアクセス" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="アプリケーションのプロビジョニング ログを有効にする" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> 最近ワークスペースをプロビジョニングしたばかりの場合は、ログを送信できるようになるまでに時間がかかることがあります。 サブスクリプションが  *microsoft.insights* を使用するように登録されていないというエラーを受け取った場合は、数分後にもう一度確認してください。
 
## <a name="understanding-the-data"></a>データの説明
プロビジョニングからログ ビューアーに送信される基のデータ ストリームはほぼ同じです。 Azure Monitor ログには、Azure portal UI および Azure API とほぼ同じストリームが取得されます。 次の表に示すように、ログ フィールドにはわずかな **違い** しかありません。 これらのフィールドの詳細については、「[provisioningObjectSummary を一覧表示する](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta)」を参照してください。

|Azure Monitor ログ   |Azure portal UI   |Azure API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor ブック

Azure Monitor ブックには、データ分析用の柔軟なキャンバスが用意されています。 また、Azure portal 内で上質なビジュアル レポートを作成することもできます。 詳細については、[Azure Monitor ブックの概要](../../azure-monitor/visualize/workbooks-overview.md)に関するページを参照してください。

アプリケーションのプロビジョニングには、あらかじめ構築された一連のブックが付属しています。 これらは、[ブック] ページで確認できます。 データを表示するには、すべてのフィルター (timeRange、jobID、appName) を設定する必要があります。 また、アプリをプロビジョニングしておく必要があります。そうしないと、ログにデータが記録されません。

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="アプリケーションのプロビジョニングのブック" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="アプリケーションのプロビジョニング ダッシュボード" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>カスタム クエリ

カスタム クエリを作成し、Azure ダッシュボードにデータを表示することができます。 方法については、[Log Analytics データのダッシュボードの作成と共有](../../azure-monitor/logs/get-started-queries.md)に関するページを参照してください。 また、[Azure Monitor のログ クエリの概要](../../azure-monitor/logs/log-query-overview.md)に関するページも参照してください。

アプリケーション プロビジョニングを始めるためのいくつかのサンプルを次に示します。

ソース システムの ID に基づいて、ユーザーのログに対してクエリを実行する:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

ErrorCode あたりの集計数:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

アクション別の 1 日あたりのイベント数の集計:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

100 イベントとプロジェクト キーのプロパティを取得する:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>カスタム アラート

Azure Monitor を使用すると、プロビジョニングに関連する主要なイベントに関する通知を受け取ることができるように、カスタム アラートを構成することができます。 たとえば、障害が急増した場合にアラートを受け取ることができます。 また、無効または削除が急増することもあります。 アラートが必要なもう 1 つの例として、プロビジョニングがないことがあります。これは、何かがうまく行っていないことを示します。

アラートの詳細については、「[Azure Monitor のアラートを使用してイベントに応答する](../../azure-monitor/alerts/tutorial-response.md)」を参照してください。

障害が急増した場合にアラートを発します。 jobID は、お使いのアプリケーションの jobID に置き換えてください。

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="障害が急増した場合にアラートを発します。" lightbox="media/application-provisioning-log-analytics/alert1.png":::

プロビジョニング サービスが動作しなくなる原因となった問題が存在する可能性があります。 次のアラートを使用して、特定の期間にプロビジョニング イベントがないことを検出します。

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="プロビジョニング サービスが動作しなくなる原因となった問題が存在する可能性があります。" lightbox="media/application-provisioning-log-analytics/alert2.png":::

無効または削除が急増した場合にアラートを発します。

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="無効または削除が急増した場合にアラートを発します。" lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>コミュニティからの投稿

Microsoft では、アプリケーションのプロビジョニングのクエリとダッシュボードに対して、オープン ソースとコミュニティベースのアプローチを採用しています。 他のユーザーに役立つと思われるクエリ、アラート、またはブックを作成した場合は、ぜひ [Azure Monitor コミュニティの GitHub リポジトリ](https://github.com/microsoft/AzureMonitorCommunity)に公開してください。 そして、リンクを含むメールを Microsoft までお送りください。 他のユーザーが恩恵を受けられるように、確認した上でサービスに公開します。 provisioningfeedback@microsoft.com までお問い合わせください。

## <a name="next-steps"></a>次のステップ

- [ログ分析](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Azure Monitor ログでクエリの使用を開始する](../../azure-monitor/logs/get-started-queries.md)
- [Create and manage alert groups in the Azure portal](../../azure-monitor/alerts/action-groups.md)
- [Azure Active Directory ログ分析用のビューのインストールと使用](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [プロビジョニング ログ API](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)