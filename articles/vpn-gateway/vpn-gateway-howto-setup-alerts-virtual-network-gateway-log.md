---
title: Azure VPN Gateway:診断リソース ログ イベントでアラートを構成する
description: Azure Monitor Log Analytics を使用して、Azure VPN Gateway からのリソース ログ イベントに基づいてアラートを設定する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 70cac6ef566ef52409cd9667708c2fc297e046f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656616"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>VPN Gateway からのリソース ログ イベントにアラートを設定する

この記事は、Azure Monitor Log Analytics を使用して、Azure VPN Gateway からのリソース ログ イベントに基づいてアラートを設定するのに役立ちます。 

Azure では、次のリソース ログを使用できます。

|"***名前** | _ *_説明_** |
|--- | --- |
|GatewayDiagnosticLog | ゲートウェイ構成イベント、主要な変更、メンテナンス イベントのリソース ログが含まれています |
|TunnelDiagnosticLog | トンネルの状態変更イベントが含まれています。 トンネルの接続/切断イベントには、状態変更の理由の概要があります (該当する場合) |
|RouteDiagnosticLog | ゲートウェイで発生する静的ルートへの変更および BGP イベントがログに記録されます |
|IKEDiagnosticLog | ゲートウェイ上の IKE コントロールのメッセージおよびイベントがログに記録されます |
|P2SDiagnosticLog | ゲートウェイ上のポイント対サイト コントロールのメッセージおよびイベントがログに記録されます。 接続ソース情報は、IKEv2 接続と OpenVPN 接続に対してのみ提供されます |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Azure portal でアラートを設定する

次の手順の例では、サイト対サイト VPN トンネルを含む切断イベントのアラートを作成します。


1. Azure portal の **[すべてのサービス]** で **[Log Analytics]** を検索し、 **[Log Analytics ワークスペース]** を選択します。

   ![Log Analytics ワークスペースに移動するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "作成")

2. **[Log Analytics]** ページで **[作成]** を選択します。

   ![[作成] ボタンがある Log Analytics ページ](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Select")

3. **[新規作成]** を選択し、詳細を入力します。

   ![Log Analytics ワークスペースを作成するための詳細](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Select")

4. **[モニター]**  >  **[診断設定]** ブレードで VPN ゲートウェイを見つけます。

   ![[診断設定] で VPN ゲートウェイを検索するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Select")

5. 診断をオンにするには、ゲートウェイをダブルクリックし、 **[診断をオンにする]** を選択します。

   ![診断をオンにするための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Select")

6. 詳細を入力し、確実に **[Log Analytics への送信]** と **[TunnelDiagnosticLog]** が選択されているようにします。 手順 3 で作成した Log Analytics ワークスペースを選択します。

   ![チェック ボックスをオン](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Select")

   > [!NOTE]
   > 最初はデータが表示されるまでに数時間かかる場合があります。

7. 仮想ネットワーク ゲートウェイ リソースの概要に移動し、 **[監視]** タブから **[アラート]** を選択します。次に新しいアラート ルールを作成するか、または既存のアラート ルールを編集します。

   ![新しいアラート ルールを作成するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")

   ![ポイント対サイト](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")
8. Log Analytics ワークスペースとリソースを選択します。

   ![ワークスペースとリソースの選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Select")

9. **[条件の追加]** で、シグナル ロジックとして **[カスタム ログ検索]** を選択します。

   ![カスタム ログ検索のための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Select")

10. **[検索クエリ]** ボックスに次のクエリを入力します。 必要に応じて、<> 内の値と TimeGenerated の値を置き換えます。

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    しきい値を 0 に設定し、 **[完了]** を選択します。

    ![クエリの入力としきい値の選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Select")

11. **[ルールの作成]** ページで、 **[アクション グループ]** セクションの **[新規作成]** を選択します。 詳細を入力して、 **[OK]** を選択します。

    ![新しいアクション グループの詳細](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Select")

12. **[ルールの作成]** ページで、 **[アクションをカスタマイズする]** に詳細を入力し、 **[アクション グループ名]** セクションに正しい名前が表示されていることを確認します。 **[アラート ルールの作成]** を選択して、ルールを作成します。

    ![ルールを作成するための選択内容](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Select")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>PowerShell を使用してアラートを設定する

次の手順の例では、サイト対サイト VPN トンネルを含む切断イベントのアラートを作成します。

1. Log Analytics ワークスペースを作成します。

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. VPN ゲートウェイの診断を有効にします。

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. アクション グループを作成します。

   このコードでは、アラートがトリガーされたときに電子メール通知を送信するアクション グループを作成します。

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. カスタム ログ検索に基づいてアラート ルールを作成します。

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>次のステップ

トンネル メトリックにアラートを構成するには、「[Set up alerts on VPN Gateway metrics](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)」(VPN Gateway メトリックにアラートを設定する) を参照してください。
