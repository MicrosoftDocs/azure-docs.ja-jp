---
title: "Azure サービス アラートの作成 - PowerShell | Microsoft Docs"
description: "指定した条件が満たされたときに、電子メール、通知、Websites URL (webhook) の呼び出し、またはオートメーションをトリガーします。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 62faba3827e9fc33e9788cd2d487adf04d760791
ms.lasthandoff: 03/15/2017


---
# <a name="create-alerts-in-azure-monitor-for-azure-services---powershell"></a>Azure Monitorでの Azure サービス アラートの作成 
> [!div class="op_single_selector"]
> * [ポータル](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>概要
この記事では、PowerShell を使用して Azure アラートを設定する方法について説明します。  

監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

* **メトリック値** - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。    
* **アクティビティ ログ イベント** - アラートは、" *すべて* " のイベントに対して、または特定数のイベントが発生したときにのみトリガーされます。

アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービスの管理者/共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す
* Azure Runbook の実行を開始する (Azure ポータルからのみ)

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します

* [Azure ポータル](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [コマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

詳細については、「 ```Get-Help``` 」と入力し、その後にヘルプが必要な PowerShell コマンドを入力してください。

## <a name="create-alert-rules-in-powershell"></a>PowerShell でアラート ルールを作成する
1. Azure にログインします。   

    ```PowerShell
    Login-AzureRmAccount

    ```
2. 使用できるサブスクリプションの一覧を取得します。 適切なサブスクリプションが動作していることを確認します。 動作していない場合は、 `Get-AzureRmSubscription`からの出力を使用して、適切なサブスクリプションに設定します。

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. リソース グループの既存のルールを一覧表示するには、次のコマンドを使用します。

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. ルールを作成するには、最初に重要な情報をいくつか入手する必要があります。

   * アラートを設定するリソースの **リソース ID**
   * そのリソースが使用できる **メトリック定義**

     リソース ID は、Azure ポータルを使用して取得できます。 リソースが既に作成されている場合は、ポータルでそのリソースを選択し、 次のブレードで "*設定*" セクションの "*プロパティ*" を選択します。 次のブレードに "**リソース ID**" フィールドがあります。 また、[Azure リソース エクスプローラー](https://resources.azure.com/)を使用することもできます。

     Web アプリのリソース ID の例を次に示します。

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     `Get-AzureRmMetricDefinition` を使用して、特定のリソースのメトリック定義の一覧を表示できます。

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     次の例では、そのメトリックの名前と単位を含むテーブルを生成します。

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Get-AzureRmMetricDefinition の使用可能なオプションの一覧を入手するには、Get-MetricDefinitions を実行します。
5. 次の例では、Web サイト リソースにアラートを設定します。 アラートは、5 分間を通してすべてのトラフィックを受け取り続けたときにトリガーされ、その後 5 分間トラフィックを受け取らなくなった時点で再度トリガーされます。

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. アラートがトリガーされたときに webhook を作成するか、電子メールを送信するには、最初に電子メールと webhook、またはそのいずれかを作成します。 次に示すように、その後すぐに -Actions タグを使用して、ルールを作成します。 PowerShell では、webhook または電子メールを、作成済みのルールと関連付けることはできません。

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


1. アクティビティ ログで特定の状況にトリガーされるアラートを作成するには、次の形式のコマンドを使用します。

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    -OperationName は、アクティビティ ログのイベントの種類に対応します。 *Microsoft.Compute/virtualMachines/delete*、*microsoft.insights/diagnosticSettings/write* などが例として挙げられます。

    PowerShell コマンド [Get AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) を使用すると、考えられる operationNames の一覧を取得できます。 また、Azure ポータルを使用して、アクティビティ ログを照会し、アラートを作成する過去の操作を検索することもできます。 操作は、フレンドリ名のグラフィック ログに表示されます。 JSON でエントリを検索し、OperationName 値を取得します。   
2. 個別のルールを確認して、アラートが正しく作成されていることを確かめます。

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
3. アラートを削除します。 この記事で作成したルールは、次のコマンドで削除します。

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>次のステップ
* [Azure での監視の概要](monitoring-overview.md) 情報を入手します。
* [アラートでの webhook の構成](insights-webhooks-alerts.md)に関する詳細情報を確認します。
* [Azure Automation Runbooks](../automation/automation-starting-a-runbook.md)の詳細情報を確認します。
* [診断ログ収集の概要](monitoring-overview-of-diagnostic-logs.md) 情報を入手して、サービスに関する詳細な頻度の高いメトリックを収集します。
* [メトリック収集の概要](insights-how-to-customize-monitoring.md) 情報を入手して、サービスの可用性と応答性を確認します。

