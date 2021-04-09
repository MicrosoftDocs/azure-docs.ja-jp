---
title: アラートの移行のためにロジック アプリと Runbook を更新する
description: 自主的な移行の準備のために、Webhook、ロジック アプリ、および Runbook を変更する方法について説明します。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: ce61c3539a4ea29cbeb48c379ed143363500701e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038018"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>クラシック アラート ルールの移行のためにロジック アプリと Runbook を準備する

> [!NOTE]
> [以前発表した](monitoring-classic-retirement.md)ように、Azure Monitor のクラシック アラートがパブリック クラウド ユーザーで廃止されましたが、**2021 年 5 月 31** 日までは、引き続き制限付きで使用できます。 Azure Government クラウドおよび Azure China 21Vianet 向けの従来のアラートは、**2024 年 2 月 29 日** に廃止されます。
>

お使いのクラシック アラート ルールを新しいアラート ルールに自主的に移行する場合は、2 つのシステムの間に違いがいくつか存在します。 この記事では、その相違点と、変更に備える方法について説明します。

## <a name="api-changes"></a>API の変更

クラシック アラート ルールを作成および管理するための API (`microsoft.insights/alertrules`) は、新しいメトリック アラートを作成および管理するための API (`microsoft.insights/metricalerts`) とは異なります。 現在、クラシック アラート ルールをプログラムで作成および管理している場合は、新しい API で機能するようにデプロイ スクリプトを更新してください。

次の表に、クラシック アラートと新しいアラートの両方のプログラム インターフェイスへの参照を示します。

| デプロイ スクリプトの種類 | クラシック アラート | 新しいメトリック アラート |
| ---------------------- | -------------- | ----------------- |
|REST API     | [microsoft.insights/alertrules](/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](/cli/azure/monitor/alert)        | [az monitor metrics alert](/cli/azure/monitor/metrics/alert)        |
|PowerShell      | [リファレンス](/powershell/module/az.monitor/add-azmetricalertrule)       |  [リファレンス](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager テンプレート | [クラシック アラート用](./alerts-enable-template.md)|[新しいメトリック アラート用](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>通知ペイロードの変更

通知ペイロードの形式は、[クラシック アラート ルール](alerts-webhooks.md)と[新しいメトリック アラート](alerts-metric-near-real-time.md#payload-schema)の間でわずかに異なります。 Webhook、ロジック アプリ、または Runbook アクションを含むクラシック アラート ルールがある場合は、新しいペイロード形式を受け入れるようにターゲットを更新する必要があります。

次の表は、クラシック形式の Webhook ペイロード フィールドを新しい形式にマッピングするときに使用します。

| 通知エンドポイントの種類 | クラシック アラート | 新しいメトリック アラート |
| -------------------------- | -------------- | ----------------- |
|アラートがアクティブ化または解決されたか    | **status**       | **data.status** |
|アラートに関するコンテキスト情報     | **context**        | **data.context**        |
|アラートがアクティブ化または解決された時点のタイム スタンプ     | **context.timestamp**       | **data.context.timestamp**        |
| アラート ルール ID | **context.id** | **data.context.id** |
| アラート ルール名 | **context.name** | **data.context.name** |
| アラート ルールの説明 | **context.description** | **data.context.description** |
| アラート ルールの条件 | **context.condition** | **data.context.condition** |
| メトリックの名前 | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| 時間の集計 (評価期間にメトリックが集計される方法)| **context.condition.timeAggregation** | **context.condition.timeAggregation** |
| 評価期間 | **context.condition.windowSize** | **data.context.condition.windowSize** |
| 演算子 (集計されたメトリック値がしきい値と比較される方法) | **context.condition.operator** | **data.context.condition.operator** |
| Threshold | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| メトリック値 | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| サブスクリプション ID | **context.subscriptionId** | **data.context.subscriptionId** |
| 影響を受けるリソースのリソース グループ | **context.resourceGroup** | **data.context.resourceGroup** |
| 影響を受けるリソースの名前 | **context.resourceName** | **data.context.resourceName** |
| 影響を受けるリソースの種類 | **context.resourceType** | **data.context.resourceType** |
| 影響を受けるリソースのリソース ID | **context.resourceId** | **data.context.resourceId** |
| ポータルのリソースの概要ページへのリンク | **context.portalLink** | **data.context.portalLink** |
| Webhook またはロジック アプリに渡されるカスタムのペイロード フィールド | **properties** | **data.properties** |

ご覧のように、ペイロードは似ています。 以降のセクションでは、次を提供します。

- 新しい形式を使用するためのロジック アプリ変更に関する詳細。
- 新しいアラートの通知ペイロードを解析する Runbook の例。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>ロジック アプリを変更してメトリック アラート通知を受信する

クラシック アラートを持つロジック アプリを使用している場合は、新しいメトリック アラート ペイロードが解析されるようにロジック アプリ コードを変更する必要があります。 次の手順に従います。

1. 新しいロジック アプリを作成します。

1. テンプレート "Azure Monitor - Metrics Alert Handler" を使用します。 このテンプレートには、定義された適切なスキーマでトリガーされる **HTTP 要求** があります。

    ![[空のロジック アプリ] と [Azure Monitor – Metrics Alert Handler] の 2 つのボタンを示すスクリーンショット。](media/alerts-prepare-migration/logic-app-template.png "メトリック アラート テンプレート")

1. 処理ロジックをホストするためのアクションを追加します。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>メトリック アラート通知を受信する Automation Runbook を使用する

次の例では、Runbook で使用する PowerShell コードを提供します。 このコードでは、クラシック メトリック アラート ルールと新しいメトリック アラート ルールの両方のペイロードが解析されます。

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

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

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
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

アラートがトリガーされたときに仮想マシンを停止する Runbook の完全な例については、[Azure Automation のドキュメント](../../automation/automation-create-alert-triggered-runbook.md)を参照してください。

## <a name="partner-integration-via-webhooks"></a>Webhook を介したパートナー統合

[クラシック アラートと統合しているパートナー](../partners.md)の大半は、統合によって、新しいメトリック アラートを既にサポートしています。 新しいメトリック アラートで既に機能する既知の統合を次に示します。

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

上記に含まれていないパートナー統合を使用している場合は、それらが新しいメトリック アラートで動作することをプロバイダーにご確認ください。

## <a name="next-steps"></a>次のステップ

- [移行ツールの使用方法](alerts-using-migration-tool.md)
- [移行ツールの動作の理解](alerts-understand-migration.md)