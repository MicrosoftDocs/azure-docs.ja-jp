---
title: PowerShell を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する
description: この記事では、Azure PowerShell を使用して Azure Firewall をデプロイおよび構成する方法を学習します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/28/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: e60c829831bde3b454ab180d1a39ec46cb346963
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658657"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Azure PowerShell を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する

オンプレミス ネットワークを Azure 仮想ネットワークに接続してハイブリッド ネットワークを作成する場合、ご利用の Azure ネットワーク リソースへのアクセスを制御する機能が、全体的なセキュリティ プランの中で重要な役割を果たします。

Azure Firewall を使用すれば、許可するネットワーク トラフィックと拒否するネットワーク トラフィックを定義するルールを使って、ハイブリッド ネットワークにおけるネットワーク アクセスを制御できます。

この記事では、3 つの仮想ネットワークを作成します。

- **VNet-Hub** - ファイアウォールは、この仮想ネットワーク内に存在します。
- **VNet-Spoke** - スポーク仮想ネットワークは Azure 上のワークロードを表します。
- **VNet-Onprem** - オンプレミス仮想ネットワークはオンプレミス ネットワークを表します。 実際のデプロイでは、VPN 接続または ExpressRoute 接続のいずれかで接続できます。 わかりやすくするため、この記事では VPN ゲートウェイ接続を使用し、Azure に配置された仮想ネットワークがオンプレミスのネットワークを表すために使用されます。

![ハイブリッド ネットワークでのファイアウォール](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

この記事では、次のことについて説明します。

* 変数を宣言する
* ファイアウォールのハブ仮想ネットワークを作成する
* スポーク仮想ネットワークを作成する
* オンプレミス仮想ネットワークを作成する
* ファイアウォールを構成してデプロイする
* VPN ゲートウェイを作成して接続する
* ハブとスポークの仮想ネットワークをピアリングする
* ルートを作成する
* 仮想マシンの作成
* ファイアウォールをテストする

このチュートリアルの実行に、代わりに Azure portal を使用する場合は、「[チュートリアル: Azure portal を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する](tutorial-hybrid-portal.md)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

この記事では、PowerShell をローカルで実行する必要があります。 Azure PowerShell モジュールをインストールしておく必要があります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell のバージョンを確認した後、`Login-AzAccount` を実行して Azure との接続を作成します。

このシナリオが正しく機能するために重要な要件が 3 つあります。

- スポーク サブネット上のユーザー定義ルート (UDR) が、Azure Firewall IP アドレスを既定のゲートウェイとして指すこと。 このルート テーブルでは仮想ネットワーク ゲートウェイのルート伝達を **無効** にする必要があります。
- ハブ ゲートウェイ サブネット上の UDR が、スポーク ネットワークへの次のホップとしてファイアウォール IP アドレスを指すこと。

   Azure Firewall サブネット上に UDR は必要ありません。BGP からルートを学習するためです。
- VNet-Hub を VNet-Spoke にピアリングするときは **AllowGatewayTransit** を設定し、VNet-Spoke を VNet-Hub にピアリングするときは **UseRemoteGateways** を設定してください。

これらのルートの作成方法については、この記事の「[ルートを作成する](#create-the-routes)」セクションをご覧ください。

>[!NOTE]
>Azure Firewall には、インターネットへの直接接続が必要です。 AzureFirewallSubnet が BGP 経由のオンプレミス ネットワークへの既定のルートを学習する場合は、インターネットへの直接接続を保持するために、**NextHopType** の値を **Internet** に設定した 0.0.0.0/0 UDR でこれを上書きする必要があります。
>
>Azure Firewall は、強制トンネリングをサポートするように構成できます。 詳細については、「[Azure Firewall 強制トンネリング](forced-tunneling.md)」を参照してください。

>[!NOTE]
>直接ピアリングされた VNets 間のトラフィックは、UDR が既定のゲートウェイとして Azure Firewall をポイントしている場合でも、直接ルーティングされます。 このシナリオでサブネット間トラフィックをファイアウォールに送信するには、UDR に両方のサブネットのターゲットのサブネット ネットワーク プレフィックスを明示的に含める必要があります。

関連する Azure PowerShell リファレンス ドキュメントを確認するには、[Azure PowerShell リファレンス](/powershell/module/az.network/new-azfirewall)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="declare-the-variables"></a>変数を宣言する

以下の例では、この記事の値を使って変数を宣言します。 いくつかの値については、ご利用のサブスクリプションで使用するために実際の値で置き換えることが必要になる場合があります。 変数を必要に応じて変更したうえでコピーし、PowerShell コンソールに貼り付けます。

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>ファイアウォールのハブ仮想ネットワークを作成する

まず、この記事のリソースを含めるためのリソース グループを作成します。

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

仮想ネットワークに含めるサブネットを定義します。

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

次に、ファイアウォールのハブ仮想ネットワークを作成します。

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

この仮想ネットワーク用に作成する VPN ゲートウェイに割り当てるパブリック IP アドレスを要求します。 *AllocationMethod* が **Dynamic** であることに注意してください。 使用する IP アドレスを指定することはできません。 VPN アドレスはゲートウェイに動的に割り当てられます。

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>スポーク仮想ネットワークを作成する

スポーク仮想ネットワークに含めるサブネットを定義します。

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

スポーク仮想ネットワークを作成します。

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>オンプレミス仮想ネットワークを作成する

仮想ネットワークに含めるサブネットを定義します。

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

次に、オンプレミス仮想ネットワークを作成します。

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

この仮想ネットワーク用に作成するゲートウェイに割り当てるパブリック IP アドレスを要求します。 *AllocationMethod* が **Dynamic** であることに注意してください。 使用する IP アドレスを指定することはできません。 IP アドレスはゲートウェイに動的に割り当てられます。

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>ファイアウォールを構成してデプロイする

次に、ハブ仮想ネットワークにファイアウォールをデプロイします。

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>ネットワーク ルールを構成する

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>VPN ゲートウェイを作成して接続する

ハブとオンプレミスの仮想ネットワークは、VPN ゲートウェイ経由で接続されます。

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>ハブ仮想ネットワークの VPN ゲートウェイを作成する

VPN ゲートウェイ構成を作成します。 VPN ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

次に、ハブ仮想ネットワークの VPN ゲートウェイを作成します。 ネットワーク間構成には、RouteBased VpnType が必要です。 選択した VPN ゲートウェイ SKU によっては、VPN ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>オンプレミス仮想ネットワークの VPN ゲートウェイを作成する

VPN ゲートウェイ構成を作成します。 VPN ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

次に、オンプレミス仮想ネットワークの VPN ゲートウェイを作成します。 ネットワーク間構成には、RouteBased VpnType が必要です。 選択した VPN ゲートウェイ SKU によっては、VPN ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>VPN 接続を作成する

これで、ハブ ゲートウェイとオンプレミス ゲートウェイとの間に VPN 接続を作成することができます。

#### <a name="get-the-vpn-gateways"></a>VPN ゲートウェイを取得する

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>接続を作成する

この手順では、ハブ仮想ネットワークからオンプレミス仮想ネットワークへの接続を作成します。 この例では、参照される共有キーが表示されます。 共有キーには独自の値を使用することができます。 両方の接続の共有キーが一致することが重要です。 接続の作成完了までしばらくかかります。

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
オンプレミスとハブとの間に仮想ネットワーク接続を作成します。 この手順は前の手順と似ていますが、VNet-Onprem から VNet-Hub への接続を作成する点が異なります。 共有キーが一致することを確認してください。 数分後に接続が確立されます。

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>接続を確認する

*Get-AzVirtualNetworkGatewayConnection* コマンドレットを使用して、接続が成功したことを確認できます。*-Debug* は指定しても指定しなくてもかまいません。 次のコマンドレットを使用します。値は実際の値に置き換えてください。 プロンプトが表示されたら、**A** を選択して **All** を実行します。 この例では、テストする接続の名前が *-Name* で示されています。

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

コマンドレットの完了後、値を確認します。 以下の例では、接続状態は *Connected* と表示され、受信バイトと送信バイトを確認できます。

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>ハブとスポークの仮想ネットワークをピアリングする

次に、ハブとスポークの仮想ネットワークをピアリングします。

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>ルートを作成する

次に、2 つのルートを作成します。

- ファイアウォール ハブ IP アドレスを介したハブ ゲートウェイ サブネットからスポーク サブネットへのルート
- ファイアウォール ハブ IP アドレスを介したスポーク サブネットからの既定ルート

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

次に、スポーク ワークロードとオンプレミスの仮想マシンを作成し、適切なサブネットに配置します。

### <a name="create-the-workload-virtual-machine"></a>ワークロード仮想マシンを作成する

スポーク仮想ネットワークに仮想マシンを作成し、IIS を実行します。この仮想マシンにパブリック IP アドレスは割り当てません。一方、ping の受信は許可します。
プロンプトが表示されたら、仮想マシンの HTTPS ユーザー名とパスワードを入力します。

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>オンプレミス仮想マシンを作成する

これは、リモート デスクトップを使用してパブリック IP アドレスに接続する際に使用する単純な仮想マシンです。 そこから、ファイアウォールを介してオンプレミス サーバーに接続します。 プロンプトが表示されたら、仮想マシンの HTTPS ユーザー名とパスワードを入力します。

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>ファイアウォールをテストする

まず、**VM-spoke-01** 仮想マシンのプライベート IP アドレスを取得してメモします。

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Azure portal から、**VM-Onprem** 仮想マシンに接続します。
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
**VM-Onprem** 上で Web ブラウザーを開き、 http://\<VM-spoke-01 private IP\> にアクセスします。

インターネット インフォメーション サービスの既定ページが表示されます。

**VM-Onprem** で、プライベート IP アドレスにある **VM-spoke-01** に対してリモート デスクトップを開きます。

接続が成功すると、選択したユーザー名とパスワードを使用してサインインできるはずです。

これで、ファイアウォール ルールが動作していることを確認できました。

<!---- You can ping the server on the spoke VNet.--->
- スポーク仮想ネットワーク上の Web サーバーにブラウザーでアクセスすることができます。
- スポーク仮想ネットワーク上のサーバーには、RDP を使用して接続できます。

次に、ファイアウォール ネットワーク ルール コレクションの動作を **Deny** に変更して、ファイアウォール ルールが想定どおりに動作することを確認します。 次のスクリプトを実行して、ルール コレクションの動作を **Deny** に変更します。

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

ここで、テストを再実行します。 このとき、すべてが失敗するはずです。 既存のリモート デスクトップをすべて閉じてから、変更したルールをテストします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ファイアウォール リソースは、次のチュートリアルのために残しておいてもかまいませんが、不要であれば、**FW-Hybrid-Test** リソース グループを削除して、ファイアウォール関連のすべてのリソースを削除してください。

## <a name="next-steps"></a>次のステップ

次に、Azure Firewall のログを監視することができます。

[チュートリアル:Azure Firewall のログを監視する](./firewall-diagnostics.md)