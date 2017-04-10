---
title: " Automation Runbook で Azure VM アラートを修復する |Microsoft Docs"
description: "この記事では、Azure Automation Runbook で Azure Virtual Machine アラートを統合し、問題を自動修復する方法について説明します"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/14/2016
ms.author: csand;magoedte
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9abb6ee8eed01ef84ee10fc2c70ea23bf482dd1c


---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Azure Automation シナリオ - Azure VM アラートを修復する
Azure Automation および Azure Virtual Machines 向けに、Automation Runbook を実行するように仮想マシン (VM) アラートを構成できる新機能がリリースされました。 この新しい機能では、VM の再起動や停止など、VM アラートに応じて標準の修復を自動的に実行できます。

これまでは、アラートがトリガーするたびに Runbook を実行するように、VM アラート ルールの作成中に Runbook に [Automation Webhook を指定](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) できていました。 ただし、これには Runbook を作成し、Runbook 用の Webhook を作成してから、アラート ルールの作成中に Webhook をコピーして貼り付ける必要がありました。 この新しいリリースでは、アラート ルールの作成中に Runbook を一覧から直接選択でき、また Runbook を実行する Automation アカウントを選択するかアカウントを簡単に作成できるようになったため、プロセスがはるかに簡単なりました。

この記事では、Azure VM アラートを設定して、アラートがトリガーするたびに実行されるように Automation Runbook を構成することがいかに簡単かを示します。 シナリオ例では、メモリ リークがある VM 上のアプリケーションのためにメモリ使用量がしきい値を超えたときの VM の再起動や、CPU ユーザー時間が過去 1 時間で 1% を下回っていて使用されていないときの VM の停止を扱います。 また、Automation アカウントのサービス プリンシパルの自動作成によって Azure アラート修復での Runbook の使用を簡素化させる方法についても説明します。

## <a name="create-an-alert-on-a-vm"></a>VM のアラートを作成する
しきい値が満たされたら Runbook を起動するようにアラートを構成するには、次の手順を実行します。

> [!NOTE]
> このリリースでは、V2 仮想マシンのみをサポートします。クラシック VM のサポートについてはまもなく追加される予定です。  
> 
> 

1. Azure ポータルにログインし、 **[仮想マシン]**をクリックします。  
2. いずれかの仮想マシンを選択します。  仮想マシンのダッシュボード ブレードが表示され、 **[設定]** ブレードが右側に表示されます。  
3. [監視] セクションで、**[設定]** ブレードから **[アラート ルール]** を選択します。
4. **[アラート ルール]** ブレードで、**[アラートの追加]** をクリックします。

**[アラート ルールの追加]** ブレードが開きます。ここで、アラートの条件を構成して、電子メールを他のユーザーに送信する、Webhook を使用してアラートを別のシステムに転送する、応答の試行時に Automation Runbook を実行して問題を修正するなどといったオプションの 1 つまたはすべてを選択できます。

## <a name="configure-a-runbook"></a>Runbook を構成する
VM アラートしきい値に達したときに実行されるように Runbook を構成するには、 **[Automation Runbook]**を選択します。 **[Configure runbook (Runbook の構成)]** ブレードで、実行する Runbook と、Runbook を実行する Automation アカウントを選択できます。

![Automation Runbook を構成して新しい Automation アカウントを作成する](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> このリリースでは、サービスが提供する Restart VM、Stop VM、Remove VM (VM の消去) の 3 つの Runbook から選択できます。  他の Runbook やいずれかの独自の Runbook を選択する機能は、今後のリリースで提供されます。
> 
> 

![Runbook の選択肢](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

3 つの利用可能な Runbook からいずれかを選択すると、 **[Automation アカウント]** ボックスが表示され、Runbook を実行する Automation アカウントを選択できます。 Runbook は、Azure サブスクリプションにある [Automation アカウント](automation-security-overview.md) のコンテキストで実行する必要があります。 既に作成した Automation アカウントを選択するか、新しい Automation アカウントを作成することができます。

提供されている Runbook は、サービス プリンシパルを使用して Azure を認証します。 既存の Automation アカウントのいずれかで Runbook を実行する場合は、サービス プリンシパルが自動的に作成されます。 新しい Automation アカウントを作成する場合は、アカウントとサービス プリンシパルが自動的に作成されます。 いずれの場合も、**AzureRunAsCertificate** という証明書資産と **AzureRunAsConnection** という接続資産の 2 つの資産が、Automation アカウントで作成されます。 Runbook は、VM に対する管理アクションを実行するために、 **AzureRunAsConnection** を使用して Azure を認証します。

> [!NOTE]
> サービス プリンシパルは、サブスクリプションのスコープで作成され、共同作成者ロールに割り当てられます。 このロールは、アカウントに対し、Automation Runbook を実行して Azure VM を管理する権限を設定するために必要です。  Automation アカウントとサービス プリンシパルの作成は、1 回限りのイベントです。 アカウントは、1 度作成すると、他の Azure VM アラートでの Runbook の実行にも私用できます。
> 
> 

**[OK]** をクリックすると、アラートが構成されます。新しい Automation アカウントを作成するオプションを選択した場合は、アカウントがサービス プリンシパルと共に作成されます。  この処理の完了には数秒かかります。  

![構成中の Runbook](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

構成が完了すると、 **[アラート ルールの追加]** ブレードに Runbook の名前が表示されます。

![構成済みの Runbook](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

**[アラート ルールの追加]** ブレードで **[OK]** をクリックすると、アラート ルールが作成され、仮想マシンが実行状態の場合アクティブ化されます。

### <a name="enable-or-disable-a-runbook"></a>Runbook を有効または無効にする
アラートで Runbook を構成した場合、Runbook はその構成を削除せず無効にできます。 これにより、アラートの実行を保持したまま、一部のアラート ルールをテストし、その後 Runbook を再度有効にすることができます。

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Azure アラートで動作する Runbook を作成する
Azure アラート ルールの一部として Runbook を選択するときは、渡されるアラート データを管理するためのロジックが Runbook に必要です。  Runbook がアラート ルールで構成されていると、Runbook 用に webhook が作成されます。アラートがトリガーするたびに、その webhook を使用して Runbook が開始されます。  Runbook を開始するための実際の呼び出しは、webhook の URL に対する HTTP POST 要求です。 POST 要求の本文には、アラートに関する有用なプロパティを格納する JSON 形式のオブジェクトが含まれています。  後述するように、アラート データには、subscriptionID、resourceGroupName、resourceName、resourceType などの詳細情報が含まれています。

### <a name="example-of-alert-data"></a>アラート データの例
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

Automation webhook サービスは HTTP POST を受信すると、アラート データを抽出して、WebhookData Runbook 入力パラメーターで Runbook に渡します。  以下のサンプル Runbook では、WebhookData パラメーターの使用方法、およびアラート データを抽出し、アラートをトリガーした Azure リソースの管理にそれを使用する方法を示します。

### <a name="example-runbook"></a>Runbook の例
```
#  This runbook will restart an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>概要
Azure VM でアラートを構成する場合、アラートがトリガー時に修復アクションを自動で実行するように Automation Runbook を簡単に構成できるようになりました。 このリリースでは、アラート シナリオに応じて VM の再起動、停止、または削除を行う Runbook を選択できます。 この新機能は、アラートのトリガー時に自動的に実行されるアクション (通知、トラブルシューティング、修復) を制御できる、便利なシナリオの始まりにすぎません。

## <a name="next-steps"></a>次のステップ
* グラフィカルな Runbook の使用を開始するには、「 [初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md)
* PowerShell Workflow Runbook の使用を開始するには、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)
* Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)




<!--HONumber=Nov16_HO3-->


