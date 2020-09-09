---
title: Azure Service Bus - メッセージング ユニットを自動的に更新する
description: この記事では、Azure Automation Runbook を使用して Service Bus 名前空間のメッセージング ユニットを自動的に更新する方法について説明します。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341495"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Azure Service Bus 名前空間のメッセージング ユニットを自動的に更新する 
この記事では、リソース (CPU またはメモリ) の使用率に基づいて Service Bus 名前空間の[メッセージング ユニット](service-bus-premium-messaging.md)を自動的に更新する方法について説明します。 

この記事の例では、名前空間の CPU 使用率が 75% を超えたときに Service Bus 名前空間のメッセージング ユニットを増やす方法を示します。 大まかな手順は次のとおりです。

1. PowerShell スクリプトを使用して、Service Bus 名前空間のメッセージング ユニットをスケールアップする (増やす) Azure Automation Runbook を作成します。 
2. 名前空間の CPU 使用率が 75% を超えたときに PowerShell スクリプトを呼び出す、Service Bus 名前空間に関する CPU 使用率アラートを作成します。 

> [!IMPORTANT]
> この記事は、Azure Service Bus の **Premium** レベルのみを対象としています。 


## <a name="create-a-service-bus-namespace"></a>Service Bus 名前空間を作成する
Premium レベルの Service Bus 名前空間を作成します。 記事「[Azure portal での名前空間の作成](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal)」の手順に従って、名前空間を作成します。 

## <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する
記事「[Azure Automation アカウントを作成する](../automation/automation-quickstart-create-account.md)」の手順に従って、Azure Automation アカウントを作成します。 

## <a name="import-azservice-module-from-gallery"></a>ギャラリーから Az.Service モジュールをインポートする
ギャラリーから Automation アカウントに `Az.Accounts` モジュールと `Az.ServiceBus` モジュールをインポートします。 `Az.ServiceBus` モジュールは `Az.Accounts` モジュールに依存するため、最初にインストールする必要があります。 

詳しい手順については、[モジュール ギャラリーからのモジュールのインポート](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal)に関するセクションを参照してください。

## <a name="create-and-publish-a-powershell-runbook"></a>PowerShell Runbook を作成して公開する

1. [PowerShell Runbook の作成](../automation/automation-quickstart-create-runbook.md)に関する記事の手順に従って、PowerShell Runbook を作成します。 

    Service Bus 名前空間のメッセージング ユニットを増やすために使用できるサンプルの PowerShell スクリプトを次に示します。 Automation Runbook 内のこの PowerShell スクリプトでは、MU を 1 から 2、2 から 4、または 4 から 8 に増やします。 このプロパティに使用できる値は次のとおりです: 1、2、4、および 8。 別の Runbook を作成すると、メッセージング ユニットを減らすことができます。

    **namespaceName** パラメーターと **resourceGroupName** パラメーターは、アラート シナリオとは別にスクリプトをテストするために使用されます。 
    
    **WebHookData** パラメーターは、アラートが実行時にリソース グループ名やリソース名などの情報を渡すためのものです。 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. **namespaceName** パラメーターと **resourceGroupName** パラメーターの値を指定して、[ブックをテスト](../automation/manage-runbooks.md#test-a-runbook)します。 名前空間のメッセージング ユニットが更新されていることを確認します。 
3. テストが成功したら、[ブックを公開](..//automation/manage-runbooks.md#publish-a-runbook)して、後で名前空間に関するアラートのアクションとして追加できるようにします。 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Runbook をトリガーする、名前空間に関するアラートを作成する
記事「[Azure Automation Runbook をトリガーするアラートを使用する](../automation/automation-create-alert-triggered-runbook.md)」を参照して、作成した Automation Runbook をトリガーする、Service Bus 名前空間に関するアラートを構成します。 たとえば、 **[CPU usage per namespace]\(名前空間あたりの CPU 使用率\)** または **[Memory size usage per namespace]\(名前空間あたりのメモリ サイズの使用量\)** メトリックに関するアラートを作成し、作成した Automation Runbook をトリガーするアクションを追加できます。 これらのメトリックの詳細については、「[リソース使用状況のメトリック](service-bus-metrics-azure-monitor.md#resource-usage-metrics)」を参照してください。

次の手順は、名前空間の **CPU 使用率**が **75%** を超えたときに Automation Runbook をトリガーするアラートを作成する方法を示しています。

1. 対象の名前空間の **[Service Bus 名前空間]** ページで、左側のメニューの **[アラート]** を選択し、ツール バーの **[+ 新しいアラート ルール]** を選択します。 
    
    ![[アラート] ページ - [新しいアラート ルール] ボタン](./media/automate-update-messaging-units/alerts-page.png)
2. **[アラート ルールの作成]** ページで、 **[条件の選択]** をクリックします。 

    ![[アラート ルールの作成] ページ - [条件の選択]](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. **[シグナル ロジックの構成]** ページで、シグナルとして **[CPU]** を選択します。 

    ![[シグナル ロジックの構成] - [CPU] を選択する](./media/automate-update-messaging-units/configure-signal-logic.png)
4. **しきい値**を入力し (この例では **75%** )、 **[完了]** を選択します。 

    ![CPU シグナルを構成する](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. 次に、 **[アラートの作成] ページ**で、 **[アクション グループの選択]** をクリックします。
    
    ![[アクション グループの選択]](./media/automate-update-messaging-units/select-action-group-button.png)
6. ツール バーの **[アクション グループの作成]** ボタンを選択します。 

    ![[アクション グループの作成] ボタン](./media/automate-update-messaging-units/create-action-group-button.png)
7. **[アクション グループの追加]** ページで、次の手順を実行します。
    1. アクション グループの**名前**を入力します。 
    2. アクション グループの**短い名前**を入力します。
    3. アクション グループを作成する**サブスクリプション**を選択します。
    4. **リソース グループ**を選択します。 
    5. **[アクション]** セクションで、**アクションの名前**を入力し、 **[Action type]\(アクションの種類\)** で **[Automation Runbook]** を選択します。 

        ![[アクション グループの追加] ページ](./media/automate-update-messaging-units/add-action-group-page.png)
8. **[Runbook の構成]** ページで、次の手順を実行します。
    1. **[Runbook のソース]** で、 **[ユーザー]** を選択します。 
    2. **[サブスクリプション]** で、Automation アカウントを含むご自分の Azure **サブスクリプション**を選択します。 
    3. **[Automation アカウント]** で、ご自分の **Automation アカウント**を選択します。
    4. **[Runbook]** で、ご自分の Runbook を選択します。 
    5. **[Runbook の構成]** ページで、 **[OK]** を選択します。 
        ![[Runbook の構成]](./media/automate-update-messaging-units/configure-runbook.png)
9. **[アクション グループの追加]** ページで、 **[OK]** を選択します。 
5. 次に、 **[アラート ルールの作成]** ページで、**ルールの名前**を入力し、 **[アラート ルールの作成]** を選択します。 
    ![[アラート ルールの作成]](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > これで、名前空間の CPU 使用率が 75 を超えると、アラートに基づいて Automation Runbook がトリガーされ、Service Bus 名前空間のメッセージング ユニットが増やされます。 同様に、名前空間の CPU 使用率が 25 を下回った場合にメッセージング ユニットを減らす別の Automation Runbook 用のアラートを作成することもできます。 

## <a name="next-steps"></a>次のステップ
メッセージング ユニットの詳細については、[Premium メッセージング](service-bus-premium-messaging.md)に関するページを参照してください
