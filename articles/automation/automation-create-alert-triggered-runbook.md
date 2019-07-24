---
title: Azure Automation Runbook をトリガーするアラートを使用する
description: Azure のアラートが発生したときに Runbook の実行をトリガーする方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88fe7740170638e9e0d7398a02dcf83ab81f6ffc
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421685"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Azure Automation Runbook をトリガーするアラートを使用する

[Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) を使用して Azure のほとんどのサービスのベース レベルのメトリックとログを監視します。 [アクション グループ](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)またはクラシック アラートから Azure Automation Runbook を呼び出して、アラートに基づくタスクを自動化することができます。 この記事では、アラートを使用して Runbook を構成および実行する方法を示します。

## <a name="alert-types"></a>アラートの種類

Automation Runbook は、次の 3 つの種類のアラートで使用できます。
* クラシック メトリック アラート
* アクティビティ ログ アラート
* ほぼリアルタイムのメトリック アラート

アラートが Runbook を呼び出すとき、実際の呼び出しは webhook に対する HTTP POST 要求です。 POST 要求の本文には、アラートに関する有用なプロパティを格納する JSON 形式のオブジェクトが含まれています。 次の表は、各アラートの種類のペイロード スキーマへのリンクを示しています。

|アラート:  |説明|ペイロード スキーマ  |
|---------|---------|---------|
|[従来のメトリック アラート](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |いずれかのプラットフォーム レベルのメトリックが特定の条件を満たしている場合に、通知が送信されます。 たとえば、過去 5 分間の VM の **CPU %** の値が **90** を超えている場合などです。| [クラシック メトリック アラートのペイロード スキーマ](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[アクティビティ ログ アラート](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Azure のアクティビティ ログ内の新しいイベントのいずれかが特定の条件と一致する場合に、通知が送信されます。 たとえば、`Delete VM` 処理が **myProductionResourceGroup** で発生した場合、または状態が **[アクティブ]** な新しい Azure Service Health イベントが表示されている場合などです。| [アクティビティ ログ アラートのペイロード スキーマ](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[ほぼリアルタイムのメトリック アラート](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |1 つまたは複数のプラットフォーム レベルのメトリックが指定した条件を満たす場合に、メトリック アラートよりも速く通知が送信されます。 たとえば、過去 5 分間の VM の **CPU %** の値が **90** より大きく、**[ネットワーク入力]** の値が **500 MB** より大きい場合などです。| [ほぼリアルタイムのメトリック アラートのペイロード スキーマ](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

種類ごとに提供されるデータは異なるため、アラートは種類ごとに異なる方法で処理されます。 次のセクションでは、さまざまな種類のアラートを処理する Runbook を作成する方法について説明します。

## <a name="create-a-runbook-to-handle-alerts"></a>アラートを処理する Runbook を作成する

アラートでオートメーションを使用するには、Runbook に渡されるアラートの JSON ペイロードを管理するロジックを持つ Runbook が必要です。 次の例の Runbook が Azure アラートから呼び出される必要があります。

前のセクションで説明したように、アラートの種類ごとにスキーマは異なります。 スクリプトは、アラートから `WebhookData` Runbook 入力パラメーターの Webhook データを取り込みます。 次に、スクリプトは JSON ペイロードを評価してどの種類のアラートが使用されたかを判断します。

この例では、VM からのアラートを使用します。 ペイロードから VM のデータを取得し、その情報を使用して VM を停止します。 接続は、Runbook が実行される Automation アカウントで設定する必要があります。 アラートを使用して Runbook をトリガーする場合は、トリガーされる Runbook でアラートの状態を確認することが重要です。 アラートの状態が変化するたびに Runbook がトリガーされます。 アラートには複数の状態があり、最も一般的な 2 つの状態が `Activated` と `Resolved` です。 Runbook が複数回実行されないように、Runbook ロジックでこの状態を確認します。 この記事の例では、`Activated` アラートのみを検索する方法を示しています。

Runbook は、VM に対する管理アクションを実行するために、**AzureRunAsConnection** [実行アカウント](automation-create-runas-account.md)を使用して Azure を認証します。

この例を使用して**Stop-AzureVmInResponsetoVMAlert** と呼ばれる Runbook を作成します。 PowerShell スクリプトは変更して、多くのさまざまなリソースで使用することができます。

1. Azure Automation アカウントに移動します。
1. **[プロセス オートメーション]** の **[Runbook]** を選択します。
1. Runbook の一覧の上部で、**[Runbook の追加]** を選択します。 
1. **[Runbook の追加]** ページで **[簡易作成]** を選択します。
1. Runbook の名前には「**Stop-AzureVmInResponsetoVMAlert**」と入力します。 Runbook の種類として、**[PowerShell]** を選びます。 そのうえで **[Create]\(作成\)** を選択します。  
1. **[編集]** ウィンドウに、次の PowerShell の例をコピーします。 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

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

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. **[発行]** を選択して Runbook を保存し、発行します。

## <a name="create-an-action-group"></a>アクション グループを作成する

アクション グループとは、アラートによってトリガーされるアクションのコレクションです。 Runbook は、アクション グループで使用できるさまざまなアクションの 1 つにすぎません。

1. Azure Portal で、**[モニター]** > **[設定]** > **[アクション グループ]** を選択します。
1. **[アクション グループの追加]** を選択して、必要な情報を入力します。  
    1. **[アクション グループ名]** ボックスに、名前を入力します。
    1. **[短い名前]** ボックスに、名前を入力します。 短い名前は、通知がこのアクション グループを使用して送信されるときに長い名前の代わりに使用されます。
    1. **[サブスクリプション]** ボックスには、現在のサブスクリプションが自動入力されます。 このサブスクリプションにアクション グループが保存されます。
    1. アクション グループが保存されるリソース グループを選択します。

この例では、Runbook アクションと通知アクションの 2 つのアクションを作成します。

### <a name="runbook-action"></a>Runbook アクション

アクション グループに Runbook アクションを作成するには:

1. **[アクション]** の **[アクション名]** にアクションの名前を入力します。 **[アクションの種類]** で **[Automation Runbook]** を選択します。
1. **[詳細]** で、**[詳細の編集]** を選択します。  
1. **[Runbook の構成]** ページの **[Runbook ソース]** で **[ユーザー]** を選択します。  
1. **[サブスクリプション]** と **[Automation アカウント]** を選択し、**Stop-AzureVmInResponsetoVMAlert** Runbook を選択します。  
1. 操作が完了したら、**[OK]** をクリックします。

### <a name="notification-action"></a>通知アクション

アクション グループに通知アクションを作成するには:

1. **[アクション]** の **[アクション名]** にアクションの名前を入力します。 **[アクションの種類]** で **[電子メール]** を選択します。  
1. **[詳細]** で、**[詳細の編集]** を選択します。  
1. **[電子メール]** ページで、通知に使用する電子メール アドレスを入力して、**[OK]** を選択します。 Runbook だけでなく、電子メール アドレスもアクションとして追加すると便利です。 Runbook が開始されたときに通知されます。  

    アクション グループは次の図のようになります。

   ![[アクション グループの追加] ページ](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. アクション グループを作成するには、**[OK]** を選択します。

このアクション グループは作成した[アクティビティ ログ アラート](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)と[ほぼリアルタイムのアラート](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json)で使用することができます。

## <a name="classic-alert"></a>クラシック アラート

クラシック アラートはメトリックに基づいており、アクション グループを使用しません。 ただし、クラシック アラートに基づく Runbook アクションを設定することができます。 

クラシック アラートを作成するには:

1. **[メトリック アラートの追加]** を選択します。
1. メトリック アラートに **myVMCPUAlert** という名前を付けます。 アラートに簡単な説明を入力します。
1. メトリック アラートの条件に、**[次の値より大きい]** を選択します。 **[しきい値]** の値に、**10** を選択します。 **[期間]** の値に、**[Over the last five minutes]\(過去 5 分間\)** を選択します。
1. **[アクションの実行]** で、**[このアラートから Runbook を実行します]** を選択します。
1. **[Runbook の構成]** ページの **[Runbook ソース]** で **[ユーザー]** を選択します。 Automation アカウントを選択し、**Stop-AzureVmInResponsetoVMAlert** Runbook を選択します。 **[OK]** を選択します。
1. アラート ルールを保存するには、**[OK]** を選択します。

## <a name="next-steps"></a>次の手順

* webhook を使用して Automation Runbook を開始する方法の詳細については、[webhook を使用した Runbook の開始](automation-webhooks.md)に関する記事をご覧ください。
* Runbook を起動するさまざまな方法については、「 [Runbook の開始](automation-starting-a-runbook.md)」を参照してください。
* アクティビティ ログ アラートの構成方法については、「[アクティビティ ログ アラートの作成](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)」をご覧ください。
* ほぼリアルタイムのアラートの作成方法については、「[Azure Portal でアラート ルールを作成する](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)」をご覧ください。

