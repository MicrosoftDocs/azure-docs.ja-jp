---
title: "Azure PowerShell を使用した Azure DDoS Protection Standard の管理 | Microsoft Docs"
description: "Azure PowerShell を使用して Azure DDoS Protection Standard を管理する方法について説明します。"
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: a1a3688d4ff215d05d2f78cdfa7d402e3fc20be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Azure PowerShell を使用した Azure DDoS Protection Standard の管理

>[!IMPORTANT]
>Azure DDoS Protection Standard (DDoS Protection) は、現在プレビュー段階にあります。 制限された数の Azure リソースが、選択されたいくつかの地域で DDoS Protection をサポートします。 サブスクリプション用に DDoS Protection を有効にするには、制限されたプレビュー中に[このサービスに登録する](http://aka.ms/ddosprotection)必要があります。 登録すると、Azure DDoS チームから連絡があり、有効化プロセスが案内されます。 DDoS Protection は、米国東部、米国西部、米国中部の各地域で使用できます。 プレビュー中、サービスの使用に対して課金されることはありません。

この記事では、Azure PowerShell を使用して DDoS Protection を有効にしたり、DDoS Protection を無効にしたり、テレメトリを使用して攻撃を軽減したりする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、「[Install Azure PowerShell module (Azure PowerShell モジュールのインストール)](/powershell/azure/install-azurerm-ps)」を参照してください。

## <a name="log-in-to-azure"></a>Azure へのログイン

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection"></a>DDoS Protection を有効にする

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>新しい仮想ネットワークを作成し、DDoS Protection を有効にします。

DDoS Protection が有効になった仮想ネットワークを作成するには、次の例を実行します。

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -DnsServer 10.0.1.5,10.0.1.6 -EnableDDoSProtection
```

この例では、2 つのサブネットと 2 つの DNS サーバーを含む仮想ネットワークを作成します。 仮想ネットワーク上の DNS サーバーを指定する効果は、この仮想ネットワークにデプロイされる NIC/VM が既定値としてこれらの DNS サーバーを継承する点にあります。 仮想ネットワーク内のすべての保護されたリソースに対して DDoS 保護が有効になります。

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>既存の仮想ネットワーク上で DDoS Protection を有効にする

既存の仮想ネットワーク上で DDoS Protection を有効にするには、次の例を実行します。

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks
```

## <a name="disable-ddos-protection-on-a-virtual-network"></a>仮想ネットワーク上の DDoS Protection を無効にする

仮想ネットワーク上の DDoS Protection を無効にするには、次の例を実行します。

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks"
```

## <a name="review-the-ddos-protection-status-of-virtual-networks"></a>仮想ネットワークの DDoS 保護の状態を確認する 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>DDoS Protection テレメトリを使用する

攻撃のテレメトリは、Azure Monitor 経由でリアルタイムに提供されます。 このテレメトリは、パブリック IP アドレスが軽減策のもとにある期間中のみ使用できます。 攻撃が軽減される前または軽減された後、テレメトリは表示されません。

### <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS Protection メトリックにアラートを構成する

Azure Monitor のアラート構成を利用すると、攻撃中にアクティブな軽減策が存在する場合に警告するいずれかの使用可能な DDoS Protection メトリックを選択できます。

#### <a name="configure-email-alert-rules-via-azure-powershell"></a>Azure PowerShell を使用して電子メール アラート ルールを構成する

1. 使用できるサブスクリプションの一覧を取得します。 適切なサブスクリプションが動作していることを確認します。 そうでない場合は、それを Get-AzureRmSubscription からの出力を使用して適切なものに設定します。 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. リソース グループの既存のルールを一覧表示するには、次のコマンドを使用します。 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. ルールを作成するには、最初に重要な情報をいくつか入手する必要があります。 

    - アラートを設定するリソースのリソース ID。
    - そのリソースに使用できるメトリック定義。 リソース ID は、Azure ポータルを使用して取得できます。 そのリソースが既に作成されていると仮定して、Azure Portal でそれを選択します。 その後、次のページで、*[設定]* セクションの下にある *[プロパティ]* を選択します。 **[RESOURCE ID] (リソース ID)** は、次のページにあるフィールドです。 また、[Azure リソース エクスプローラー](https://resources.azure.com/)を使用することもできます。 パブリック IP のリソース ID の例として `/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip` があります。

    次の例では、仮想ネットワークに関連付けられたパブリック IP にアラートを設定します。 アラートを作成するメトリックは、**[Under DDoS attack or not] (DDoS 攻撃を受けているかどうか)** です。 これはブール値 1 または 0 です。 **1** は、攻撃を受けていることを示します。 **0** は、攻撃を受けていないことを示します。 過去 5 分以内に攻撃を受けている場合は、アラートが作成されます。

    アラートが作成されたときに webhook を作成するか、または電子メールを送信するには、まず電子メールまたは webhook、あるいはその両方を作成します。 次に示すように、その後すぐに -Actions タグを使用して、ルールを作成します。 PowerShell を使用して、既に作成されているルールに webhook または電子メールを関連付けることはできません。

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail-Description "Under DDoS Attack" 
    ```

4. 個別のルールを確認して、アラートが正しく作成されていることを確かめます。

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

また、[webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)や、アラートを作成するための[ロジック アプリ](../logic-apps/logic-apps-what-are-logic-apps.md)の詳細についても学習できます。

## <a name="configure-logging-on-ddos-protection-metrics"></a>DDoS Protection メトリックへのログ記録を構成する

PowerShell 経由で Azure の診断ログにアクセスしたり、この診断ログを構成したりするには、[PowerShell クイック スタート サンプル](../monitoring-and-diagnostics/insights-powershell-samples.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure 診断ログの詳細を確認する](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Log Analytics を使用した、Azure ストレージからのログの分析](../log-analytics/log-analytics-azure-storage.md)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)