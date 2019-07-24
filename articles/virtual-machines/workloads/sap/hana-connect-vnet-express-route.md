---
title: 仮想ネットワークから SAP HANA on Azure (Large Instances) への接続のセットアップ | Microsoft Docs
description: SAP HANA on Azure (Large Instances) を使用した仮想ネットワークからの接続のセットアップ。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb14d0784ecb87c85b02952880e9eb5744d205a2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850671"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>HANA Large Instances に仮想ネットワークを接続する

Azure Virtual Network を作成したら、SAP HANA on Azure Large Instances にそのネットワークを接続することができます。 仮想ネットワーク上に Azure ExpressRoute ゲートウェイを作成します。 このゲートウェイでは、大規模なインスタンス スタンプのカスタマー テナントに接続する ExpressRoute 回線に仮想ネットワークをリンクすることができます。

> [!NOTE] 
> この手順が完了するまで最大で 30 分かかることがあります。 新しいゲートウェイは指定された Azure サブスクリプション内で作成されてから、指定された Azure Virtual Network に接続されます。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

ゲートウェイが既に存在する場合は、それが ExpressRoute ゲートウェイかどうかを確認します。 ExpressRoute ゲートウェイでない場合は、そのゲートウェイを削除し、ExpressRoute ゲートウェイとして再作成します。 ExpressRoute ゲートウェイが既に確立されている場合は、この記事の後述の「仮想ネットワークをリンクする」というセクションを参照してください。 

- [Azure Portal](https://portal.azure.com/) または PowerShell を使用して、お使いの仮想ネットワークに接続された ExpressRoute VPN ゲートウェイを作成します。
  - Azure Portal を使用する場合、新しい **Virtual Network ゲートウェイ**を追加し、ゲートウェイの種類として **[ExpressRoute]** を選択します。
  - PowerShell を使用する場合は、まず、最新の [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) をダウンロードして、それを使用します。 次のコマンドは ExpressRoute ゲートウェイを作成します。 _$_ の後に続くテキストはユーザー定義変数で、ユーザー固有の情報で更新する必要があります。

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

この例では、HighPerformance ゲートウェイ SKU が使用されました。 使用できるのは、HighPerformance または UltraPerformance です。SAP HANA on Azure (Large Instances) でサポートされているゲートウェイ SKU はこれらのみとなります。

> [!IMPORTANT]
> Type II クラス SKU の HANA Large Instances の場合、UltraPerformance ゲートウェイ SKU を使用する必要があります。

## <a name="link-virtual-networks"></a>仮想ネットワークをリンクする

これで、Azure Virtual Network に ExpressRoute ゲートウェイが作成されました。 Microsoft から提供された承認情報を使用して、ExpressRoute ゲートウェイを SAP HANA on Azure (Large Instances) の ExpressRoute 回線に接続します。 Azure Portal または PowerShell を使用して接続できます。 ポータルの使用をお勧めしますが、PowerShell を使用する必要がある場合、手順は次のようになります。 

接続ごとに異なる AuthGUID を使用して、各仮想ネットワーク ゲートウェイで次のコマンドを実行します。 次のスクリプトに示す最初の 2 つのエントリは、Microsoft から提供される情報です。 また、AuthGUID はすべての仮想ネットワークとそのゲートウェイに固有のものです。 別の Azure Virtual Network を追加する場合は、HANA Large Instances を Azure に接続する ExpressRoute 回線の AuthID がもう 1 つ必要になります。 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

サブスクリプションに関連付けられた複数の ExpressRoute 回線にゲートウェイを接続するには、この手順を複数回実行しなければならないことがあります。 たとえば、通常は同じ仮想ネットワーク ゲートウェイを、仮想ネットワークとオンプレミス ネットワークとを接続する ExpressRoute 回線に接続します。

## <a name="next-steps"></a>次の手順

- [HLI 向け追加のネットワーク要件](hana-additional-network-requirements.md)
