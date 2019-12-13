---
title: Azure Automation Runbook をトリガーするアラートを使用する
description: Azure のアラートが発生したときに Runbook の実行をトリガーする方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4afcbced2879a2a6d50112b6388cdf5c8098b1d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850382"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Azure Automation Runbook をトリガーするアラートを使用する

[Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) を使用して Azure のほとんどのサービスのベース レベルのメトリックとログを監視します。 [アクション グループ](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)またはクラシック アラートから Azure Automation Runbook を呼び出して、アラートに基づくタスクを自動化することができます。 この記事では、アラートを使用して Runbook を構成および実行する方法を示します。

## <a name="alert-types"></a>アラートの種類

Automation Runbook は、次の 3 つの種類のアラートで使用できます。

* 共通アラート
* アクティビティ ログ アラート
* ほぼリアルタイムのメトリック アラート

> [!NOTE]
> 共通アラート スキーマは、今日の Azure でのアラート通知の使用エクスペリエンスを標準化します。 これまで、今日の Azure の 3 種類のアラート (メトリック、ログ、アクティビティ ログ) は、独自の電子メール テンプレートや Webhook スキーマ などを持ちました。詳細については、「[共通アラート スキーマ](../azure-monitor/platform/alerts-common-schema.md)」を参照してください

アラートが Runbook を呼び出すとき、実際の呼び出しは webhook に対する HTTP POST 要求です。 POST 要求の本文には、アラートに関する有用なプロパティを格納する JSON 形式のオブジェクトが含まれています。 次の表は、各アラートの種類のペイロード スキーマへのリンクを示しています。

|アラート:  |説明|ペイロード スキーマ  |
|---------|---------|---------|
|[共通アラート](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|今日の Azure でのアラート通知の使用エクスペリエンスを標準化する共通アラート スキーマ。|共通アラートのペイロード スキーマ|
|[アクティビティ ログ アラート](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Azure のアクティビティ ログ内の新しいイベントのいずれかが特定の条件と一致する場合に、通知が送信されます。 たとえば、`Delete VM` 処理が **myProductionResourceGroup** で発生した場合、または状態が **[アクティブ]** な新しい Azure Service Health イベントが表示されている場合などです。| [アクティビティ ログ アラートのペイロード スキーマ](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[ほぼリアルタイムのメトリック アラート](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |1 つまたは複数のプラットフォーム レベルのメトリックが指定した条件を満たす場合に、メトリック アラートよりも速く通知が送信されます。 たとえば、過去 5 分間の VM の **CPU %** の値が **90** より大きく、 **[ネットワーク入力]** の値が **500 MB** より大きい場合などです。| [ほぼリアルタイムのメトリック アラートのペイロード スキーマ](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

種類ごとに提供されるデータは異なるため、アラートは種類ごとに異なる方法で処理されます。 次のセクションでは、さまざまな種類のアラートを処理する Runbook を作成する方法について説明します。

## <a name="create-a-runbook-to-handle-alerts"></a>アラートを処理する Runbook を作成する

アラートでオートメーションを使用するには、Runbook に渡されるアラートの JSON ペイロードを管理するロジックを持つ Runbook が必要です。 次の例の Runbook が Azure アラートから呼び出される必要があります。

前のセクションで説明したように、アラートの種類ごとにスキーマは異なります。 スクリプトは、アラートから `WebhookData` Runbook 入力パラメーターの Webhook データを取り込みます。 次に、スクリプトは JSON ペイロードを評価してどの種類のアラートが使用されたかを判断します。

この例では、VM からのアラートを使用します。 ペイロードから VM のデータを取得し、その情報を使用して VM を停止します。 接続は、Runbook が実行される Automation アカウントで設定する必要があります。 アラートを使用して Runbook をトリガーする場合は、トリガーされる Runbook でアラートの状態を確認することが重要です。 アラートの状態が変化するたびに Runbook がトリガーされます。 アラートには複数の状態があり、最も一般的な 2 つの状態が `Activated` と `Resolved` です。 Runbook が複数回実行されないように、Runbook ロジックでこの状態を確認します。 この記事の例では、`Activated` アラートのみを検索する方法を示しています。

Runbook は、VM に対する管理アクションを実行するために、**AzureRunAsConnection** [実行アカウント](automation-create-runas-account.md)を使用して Azure を認証します。

この例を使用して**Stop-AzureVmInResponsetoVMAlert** と呼ばれる Runbook を作成します。 PowerShell スクリプトは変更して、多くのさまざまなリソースで使用することができます。

1. Azure Automation アカウントに移動します。
2. **[プロセス オートメーション]** の **[Runbook]** を選択します。
3. Runbook の一覧の上部で、 **[+ Runbook の作成]** を選択します。
4. **[Runbook の追加]** ページで、Runbook 名として「**Stop-AzureVmInResponsetoVMAlert**」と入力します。 Runbook の種類として、 **[PowerShell]** を選びます。 そのうえで **[Create]\(作成\)** を選択します。  
5. **[編集]** ページに、次の PowerShell の例をコピーします。

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. **[発行]** を選択して Runbook を保存し、発行します。

## <a name="create-the-alert"></a>アラートを作成する

アラートでは、アクション グループが使用されます。アクション グループは、アラートによってトリガーされるアクションのコレクションです。 Runbook は、アクション グループで使用できるさまざまなアクションの 1 つにすぎません。

1. Automation アカウントで、 **[監視]** の下の **[アラート]** を選択します。
1. **[+ 新しいアラート ルール]** を選択します。
1. **[リソース]** の下の **[選択]** をクリックします。 **[リソースの選択]** ページで、アラートの対象となる VM を選択し、 **[完了]** をクリックします。
1. **[条件]** の下の **[条件の追加]** をクリックします。 使用するシグナル (たとえば、 **[CPU の割合]** ) を選択し、 **[完了]** をクリックします。
1. **[シグナル ロジックの構成]** ページで、 **[アラート ロジック]** の下の **[しきい値]** に入力し、 **[完了]** をクリックします。
1. **[アクション グループ]** の **[新規作成]** を選択します。
1. **[アクション グループの追加]** ページで、アクション グループに名前と短い名前を付けます。
1. アクションの名前を指定します。 アクションの種類として、 **[Automation Runbook]** を選択します。
1. **[詳細の編集]** を選択します。 **[Runbook の構成]** ページの **[Runbook ソース]** で **[ユーザー]** を選択します。  
1. **[サブスクリプション]** と **[Automation アカウント]** を選択し、**Stop-AzureVmInResponsetoVMAlert** Runbook を選択します。  
1. **[一般的なアラート スキーマを有効にします]** で **[はい]** を選択します。
1. アクション グループを作成するには、 **[OK]** を選択します。

    ![[アクション グループの追加] ページ](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    このアクション グループは作成した[アクティビティ ログ アラート](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)と[ほぼリアルタイムのアラート](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json)で使用することができます。

1. **[アラートの詳細]** で、アラート ルールの名前と説明を追加し、 **[アラート ルールの作成]** をクリックします。

## <a name="next-steps"></a>次の手順

* webhook を使用して Automation Runbook を開始する方法の詳細については、[webhook を使用した Runbook の開始](automation-webhooks.md)に関する記事をご覧ください。
* Runbook を起動するさまざまな方法については、「 [Runbook の開始](automation-starting-a-runbook.md)」を参照してください。
* アクティビティ ログ アラートの構成方法については、「[アクティビティ ログ アラートの作成](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)」をご覧ください。
* ほぼリアルタイムのアラートの作成方法については、「[Azure Portal でアラート ルールを作成する](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)」をご覧ください。
