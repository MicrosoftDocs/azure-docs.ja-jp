---
title: チュートリアル:Azure PowerShell を使用して仮想ハブをセキュリティで保護する
description: この記事では、あるリージョンに Azure Virtual WAN を作成し、ハブで Azure Firewall を有効にする方法について説明します。
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 093b962bb9f42b660fb8cc5c0584ec67ebc87e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789164"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して仮想ハブをセキュリティで保護する

このチュートリアルでは、あるリージョンに Virtual WAN インスタンスと仮想ハブを作成し、その仮想ハブに Azure ファイアウォールをデプロイして接続をセキュリティで保護します。 この例では、仮想ネットワーク間の接続をセキュリティで保護する方法について説明します。 仮想セキュア ハブでは、仮想ネットワーク間だけでなくサイト間、ポイント対サイト、ExpressRoute ブランチ間のトラフィックもサポートされます。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Virtual WAN をデプロイする
> * Azure Firewall のデプロイとカスタム ルーティングの構成を行う
> * 接続をテストする

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- PowerShell 7

   このチュートリアルでは、Azure PowerShell をローカルの PowerShell 7 上で実行する必要があります。 PowerShell 7 のインストールについては、「[Windows PowerShell 5.1 から PowerShell 7 への移行](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7)」を参照してください。
- Az.Network バージョン 3.2.0

    Az.Network バージョン 3.4.0 以降を使用している場合は、このチュートリアルのいくつかのコマンドを使用するためにダウングレードする必要があります。 ご使用の Az.Network モジュールのバージョンは、`Get-InstalledModule -Name Az.Network` というコマンドで確認できます。 Az.Network モジュールをアンインストールするには、`Uninstall-Module -name az.network` を実行します。 Az.Network 3.2.0 モジュールをインストールするには、`Install-Module az.network -RequiredVersion 3.2.0 -force` を実行します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>最初の Virtual WAN のデプロイ

最初に、いくつかの変数を設定し、リソース グループ、Virtual WAN インスタンス、仮想ハブを作成する必要があります。

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

2 つの仮想ネットワークを作成し、それらをスポークとしてハブに接続します。

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

この時点で、Virtual WAN は完全に機能するようになり、Any-to-Any 接続が提供されます。 そのセキュリティを強化するためには、それぞれの仮想ハブに Azure ファイアウォールをデプロイする必要があります。 Virtual WAN の Azure Firewall インスタンスは、ファイアウォール ポリシーを使用して効率的に管理することができます。 そこで、この例ではファイアウォール ポリシーも作成します。

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Azure Firewall から Azure Monitor へのログを有効にするかどうかは任意ですが、この例では、トラフィックがファイアウォールを経由していることを確かめるために、ファイアウォール ログを使用します。

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Azure Firewall のデプロイとカスタム ルーティングの構成を行う

Azure ファイアウォールをハブに用意したら終わりではなく、Virtual WAN で仮想ネットワークやブランチからのトラフィックをファイアウォール経由で送信できるよう、次はルーティングに変更を加える必要があります。 これは 2 つの手順で行います。

1. すべての仮想ネットワーク接続 (ブランチ接続がある場合はそれらも含む) を `None` ルート テーブルに伝達するように構成します。 この構成の結果、他の仮想ネットワークやブランチではそれらのプレフィックスを認識しなくなるので、それらに到達するルーティングが行われなくなります。
1. これで、(すべての仮想ネットワークとブランチが既定で関連付けられる) `Default` ルート テーブルに静的ルートを挿入することができ、すべてのトラフィックが Azure ファイアウォールに送信されるようになります。

> [!NOTE]
> これは、Azure portal で Azure Firewall Manager を使用して接続をセキュリティで保護する場合のデプロイ構成です。

まずは最初の手順として、伝達先を `None` ルート テーブルとするように仮想ネットワーク接続を構成します。

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

続いて 2 つ目の手順に進み、`Default` ルート テーブルに静的ルートを追加します。 この例では、Virtual WAN における接続をセキュリティで保護する際に Azure Firewall Manager で生成される既定の構成を適用しますが、静的ルートに含まれるプレフィックスのリストは、特定の要件に合わせて変更することができます。

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>接続をテストする

以上で、完全に機能するセキュアなハブが完成しました。 接続をテストするためには、ハブに接続された各スポーク仮想ネットワークに 1 つの仮想マシンが必要です。

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

既定では、ファイアウォール ポリシーはすべてを遮断する構成になっています。 そこで、いくつかのルールを構成する必要があります。 まず DNAT ルールで、ファイアウォールのパブリック IP アドレスを通じてテスト仮想マシンにアクセスできるようにします。

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

今度は、いくつかのサンプル ルールを構成しましょう。 SSH トラフィックを許可するネットワーク ルールと、完全修飾ドメイン名 `ifconfig.co` へのインターネット アクセスを許可するアプリケーション ルールを定義します。 この URL では、HTTP 要求で検出される送信元 IP アドレスが返されます。

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

トラフィックを実際に送信する前に、仮想マシンの有効なルートを検査することができます。 これらには、Virtual WAN で指定されたプレフィックス (`0.0.0.0/0` および RFC1918) だけが含まれていて、他のスポークのプレフィックスは含まれていない必要があります。

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

次に、一方から他方の仮想マシンへのトラフィックを生成し、それが Azure ファイアウォールで遮断されることを検証してみましょう。 以下の SSH コマンドでは、仮想マシンのフィンガープリントを受け入れ、仮想マシンの作成時に定義したパスワードを指定する必要があります。 この例では、spoke1 の仮想マシンから spoke2 に対し、5 つの ICMP エコー要求パケットを送信すると共に、Linux ユーティリティである `nc` を使用して、ポート 22 での TCP 接続を試行します (`-vz` フラグが指定されているので、接続要求が送信され、その結果が表示されるだけです)。 ping は失敗し、ポート 22 での TCP 接続試行は成功することがわかります。これは、先ほど構成したネットワーク ルールによって許可されているためです。

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

インターネット トラフィックも検証しましょう。 ファイアウォール ポリシーで許可した FQDN (`ifconfig.co`) への `curl` ユーティリティによる HTTP 要求は正しく機能しますが、他の宛先への HTTP 要求は失敗します (この例では、`bing.com` を使用してテストしています)。

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

ファイアウォールによってパケットが遮断されたかどうかは、ログをチェックすれば簡単に検証できます。 Azure Monitor にログを送信するように Azure ファイアウォールを構成してあるので、Kusto クエリ言語を使用して、適切なログを Azure Monitor から取得できます。

> [!NOTE]
> Azure Monitor にログが送信されるのがわかるまでに 1 分ほどかかる場合があります。

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

前述のコマンドでは、さまざまなエントリが表示されます。

* SSH 接続が DNAT 変換されている
* スポークの VM (10.1.1.4 と 10.1.2.4) 間で ICMP パケットが遮断されている
* スポークの VM 間の SSH 接続が許可されている

以下に示すのは、上のコマンドで生成された出力の例です。

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

アプリケーション ルール (許可/拒否する HTTP 接続を記述したもの) のログを確認したい場合や、ログの表示方法を変更したい場合は、他の KQL クエリで試してみてください。 「[Azure Firewall の Azure Monitor ログ](../firewall/firewall-workbook.md)」でいくつかの例をご覧いただけます。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

テスト環境を削除するには、リソース グループとそこに含まれるすべてのオブジェクトを削除します。

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [信頼されたセキュリティ パートナーについて学習する](trusted-security-partners.md)