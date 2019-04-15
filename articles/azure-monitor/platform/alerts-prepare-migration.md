---
title: ロジック アプリと Runbook の更新による Azure Monitor クラシック アラートの移行の準備
description: 自主的な移行の準備のために、Webhook、ロジック アプリ、および Runbook を変更する方法について説明します。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631697"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>クラシック アラート ルールの移行のためにロジック アプリと Runbook を準備する

[以前発表された](monitoring-classic-retirement.md)ように、Azure Monitor のクラシック アラートは 2019 年 7 月に廃止される予定です。 自主的な移行を実行するための移行ツールが Azure portal で使用可能であり、クラシック アラート ルールを使用するお客様にロールアウトされます。

クラシック アラート ルールを新しいアラート ルールに自主的に移行する場合は、2 つのシステム間のいくつかの違いについて認識する必要があります。 この記事では、2 つのシステム間の違いと、変更に向けた準備について説明します。

## <a name="api-changes"></a>API の変更

クラシック アラート ルールを作成/管理するために使用される API (`microsoft.insights/alertrules`) は、新しいメトリック アラートを作成/管理するために使用される API (`microsoft.insights/metricalerts`) とは異なります。 現在、クラシック アラート ルールをプログラムで作成/管理している場合は、新しい API で機能するようにデプロイ スクリプトを更新してください。

次の表に、クラシック アラートと新しいアラートの両方のプログラム インターフェイスを示します。

|         |クラシック アラート  |新しいメトリック アラート |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [リファレンス](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Azure Resource Manager テンプレート | [クラシック アラート用](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[新しいメトリック アラート用](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>通知ペイロードの変更

通知ペイロードの形式は、[クラシック アラート ルール](alerts-webhooks.md)と[新しいメトリック アラート](alerts-metric-near-real-time.md#payload-schema)の間でわずかに異なります。 クラシック アラート ルールによってトリガーされる Webhook、ロジック アプリ、または Runbook のアクションがある場合は、新しいメトリック アラートのペイロード形式を受け入れるようにこれらの通知エンドポイントを更新する必要があります。

次の表を使用して、クラシック アラート ルールの Webhook ペイロードと新しいメトリック アラートの Webhook ペイロードをマップできます。

|  |クラシック アラート  |新しいメトリック アラート |
|---------|---------|---------|
|アラートがアクティブ化または解決されたか     | status       | data.status |
|アラートに関するコンテキスト情報     | context        | data.context        |
|アラートがアクティブ化または解決された時点のタイムスタンプ      | context.timestamp       | data.context.timestamp        |
| アラート ルール ID | context.id | data.context.id |
| アラート ルール名 | context.name | data.context.name |
| アラート ルールの説明 | context.description | data.context.description |
| アラート ルールの条件 | context.condition | data.context.condition|
| メトリックの名前 | context.condition.metricName| data.context.condition.allOf[0].metricName|
| 時間の集計 (評価期間にメトリックが集計される方法)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| 評価期間 | context.condition.windowSize | data.context.condition.windowSize|
| 演算子 (集計されたメトリック値がしきい値と比較される方法) | context.condition.operator | data.context.condition.operator|
| Threshold | context.condition.threshold| data.context.condition.allOf[0].threshold|
| メトリック値 | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| サブスクリプション ID | context.subscriptionId | data.context.subscriptionId|
| 影響を受けるリソースのリソース グループ | context.resourceGroup | data.context.resourceGroup|
| 影響を受けるリソースの名前 | context.resourceName | data.context.resourceName |
| 影響を受けるリソースの種類 | context.resourceType | data.context.resourceType |
|  影響を受けるリソースのリソース ID | context.resourceId | data.context.resourceId |
| ポータルのリソースの概要ページへの直接リンク | context.portalLink | data.context.portalLink|
| Webhook またはロジック アプリに渡されるカスタムのペイロード フィールド | properties |data.properties |

ご覧のように、両方のペイロードは似ています。 次のセクションでは、新しいアラートの通知ペイロードを解析するサンプル ロジック アプリとサンプル Runbook について詳細に説明します。

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>メトリック アラート通知を受信するロジック アプリの使用

クラシック アラートを持つロジック アプリを使用している場合は、新しいメトリック アラート ペイロードを解析するようにロジック アプリを変更する必要があります。

1. 新しいロジック アプリを作成します。

2. テンプレート "Azure Monitor - Metrics Alert Handler" を使用します。 このテンプレートには、定義された適切なスキーマでトリガーされる **HTTP 要求**があります。

    ![logic-app-template](media/alerts-migration/logic-app-template.png "メトリック アラート テンプレート")

3. 処理ロジックをホストするためのアクションを追加します。

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>メトリック アラート通知を受信する Automation Runbook の使用

次のサンプルでは、クラシック メトリック アラート ルールと新しいメトリック アラート ルールの両方のペイロードを解析できる Runbook で使用可能な PowerShell コードを提供します。

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

アラートがトリガーされたときに VM を停止する Runbook の完全な例については、[Azure Automation の資料](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)を参照してください。

## <a name="partner-integration-via-webhooks"></a>Webhook を介したパートナー統合

[クラシック アラートと統合しているパートナー](https://docs.microsoft.com/azure/azure-monitor/platform/partners)の大半は、統合によって、新しいメトリック アラートを既にサポートしています。 新しいメトリック アラートを操作する既知の統合を次に示します。

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

上記に含まれていないパートナー統合を使用している場合は、統合が新しいメトリック アラートで動作することを統合のプロバイダーにご確認ください。

## <a name="next-steps"></a>次の手順

- [移行ツールの使用方法](alerts-using-migration-tool.md)
- [移行ツールの動作の理解](alerts-understand-migration.md)
