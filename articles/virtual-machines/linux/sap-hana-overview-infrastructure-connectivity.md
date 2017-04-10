---
title: "SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続 | Microsoft Docs"
description: "SAP HANA on Azure (L インスタンス) を使用するために必要な接続インフラストラクチャを構成します。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 4b01305ea1fc033de64ed2e4fb7e4c9f3d16d394
ms.lasthandoff: 03/01/2017


---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続 

Microsoft のエンタープライズ アカウント チームとの間で SAP HANA on Azure (L インスタンス) の購入が完了した後で、次の情報が必要となります。

- お客様名
- 会社の連絡先情報 (電子メール アドレスや電話番号など)
- 技術担当者の連絡先情報 (電子メール アドレスや電話番号など)
- 技術担当者のネットワーキング用の連絡先情報 (電子メール アドレスや電話番号など)
- Azure デプロイ リージョン (2016 年 9 月時点では米国西部または米国東部)
- SAP HANA on Azure (L インスタンス) の SKU (構成) の確認
- デプロイ先のすべての Azure リージョン:
  - P2P 接続用の /29 の IP アドレス範囲
  - CIDR ブロック (HANA L インスタンスの NAT プール用。/24 を推奨)
- Azure リージョンとは関係なく HANA L インスタンスに接続するすべての Azure VNet:
  - 1 つ以上の /28 の IP アドレス範囲または /27 の IP アドレス範囲 (カスタマー VNet ゲートウェイ サブネット用)
  - 1 つ以上の CIDR ブロック (カスタマー VNet テナント サブネット用。/24 を推奨)
- 各 HANA L インスタンス システムのデータ:
  - 必要なホスト名
  - NAT プールの必要な IP アドレス
- SAP HANA on Azure (L インスタンス) が直接接続する Azure サブスクリプションの Azure サブスクリプション番号
- SAP HANA インスタンス用の SAP HANA SID 名 (SAP HANA に関連する必要なディスク ボリュームの作成に必要)

情報を入力した後で、Microsoft によって SAP HANA on Azure (L インスタンス) がプロビジョニングされます。

次にネットワークの設定情報が次の目的で提供されます。

- Azure VNet を HANA L インスタンスに接続する ExpressRoute 回線に Azure VNet を接続する
  - Azure Resource Manager:
     - 承認キー
     - ExpressRoute PeerID
- 確立された ExpressRoute 回線と Azure VNet で HANA L インスタンスにアクセスする

## <a name="creating-an-azure-vnet"></a>Azure VNet の作成

この Azure VNet は、Azure Resource Manager デプロイメント モデルを使用して作成する必要があります。 ASM として一般的に知られている以前の Azure デプロイメント モデルは、このソリューションではサポートされていません。

作成された Azure VNet には、少なくとも&1; つのテナント サブネットと、1 つのゲートウェイ サブネットが必要です。 これらは指定されたとおりに IP アドレス範囲を割り当てられ、Microsoft に送信される必要があります。

> [!IMPORTANT] 
> テナント アドレス ブロックとゲートウェイ アドレス ブロックだけは、Azure サブスクリプション内の VNet に割り当てられる必要があります。 P2P アドレス ブロックと NAT プールのアドレス ブロックは Azure サブスクリプションの外部に存在するため、VNet およびサブネットのアドレス空間とは別にする必要があります。

複数のテナント サブネットが使用できますが (非連続のアドレス範囲でも使用可能)、前述のように、これらのアドレス範囲は事前に Microsoft に送信されている必要があります。

これらのテナント サブネットには、任意の命名標準を使用できます。 SAP HANA on Azure (L インスタンス) の ExpressRoute 回線に接続する **VNet ごとに、必ず&1; つの (唯一の) ゲートウェイ サブネットが必要です**。**このゲートウェイ サブネットは必ず &quot;GatewaySubnet&quot; と命名する必要があります** (これにより ExpressRoute ゲートウェイが適切に配置されます)。

> [!WARNING] 
> ゲートウェイ サブネットに必ず &quot;GatewaySubnet&quot; と命名することは非常に重要です。

VNet は、Azure Portal、PowerShell、Azure テンプレート、Azure CLI を使用して作成することができます (「[Azure Portal を使用した仮想ネットワークの作成](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください)。

## <a name="creating-a-gateway-subnet"></a>ゲートウェイ サブネットの作成

Azure VNet を作成した後、VNet 上に ExpressRoute ゲートウェイを作成する必要があります。これを利用して、L インスタンス スタンプのカスタマー テナントに接続する ExpressRoute 回線に VNet をリンクします。

> [!NOTE] 
> この手順では、新しいゲートウェイは指定された Azure サブスクリプション内で作成されてから、指定された Azure VNet に接続されるので、最大 30 分かかる場合があります。

ゲートウェイが既に存在する場合、それが ExpressRoute ゲートウェイかどうかを確認します。 そうでなかった場合、ゲートウェイを削除し、ExpressRoute ゲートウェイとして再作成する必要があります。 ExpressRoute ゲートウェイが既に確立されている場合、Azure VNet は既にオンプレミス接続を行うために ExpressRoute 回線に接続されているので、次の「VNet のリンク」セクションに進んでください。

- (新しい) [Azure Portal](https://portal.azure.com/) または PowerShell を使用して、お使いの VNet に接続された ExpressRoute VPN ゲートウェイを作成します。
  - Azure Portal を使用する場合、新しい **Virtual Network ゲートウェイ**を追加し、ゲートウェイの種類として **ExpressRoute** を選択します。
  - 代わりに PowerShell を使用する場合、まずは最新の [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) をダウンロードして使用し、最適なエクスペリエンスを確保します。 次のコマンドは ExpressRoute ゲートウェイを作成します。 _$_ の後に続くテキストは、ユーザー固有の情報に更新する必要があるユーザー定義変数です。

```
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

この例では、HighPerformance ゲートウェイ SKU が使用されました。 使用できるのは、HighPerformance または UltraPerformance です。SAP HANA on Azure (L インスタンス) でサポートされているゲートウェイ SKU はこれらのみとなります。

## <a name="linking-vnets"></a>VNet のリンク

Azure VNet 用に ExpressRoute ゲートウェイを作成した後は、Microsoft から提供された承認情報を使用して、ExpressRoute ゲートウェイを SAP HANA on Azure (L インスタンス) の ExpressRoute 回線 (この接続を行うために作成したもの) に接続します。 これは PowerShell でしか実行できません (Azure Portal では現在サポートされていません)。

- 接続ごとに異なる AuthGUID を使用して、各 VNet ゲートウェイで次を実行します。 次に示す最初の&2; つのエントリは、Microsoft から提供される情報です。 また、AuthGUID はすべての VNet とそのゲートウェイに固有です。

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

サブスクリプションに関連付けられた複数の異なる ExpressRoute 回線にゲートウェイを接続する場合は、この手順を複数回実行しなければならない可能性があります。

## <a name="adding-more-ip-addresses-or-subnets"></a>IP アドレスまたはサブネットの追加

IP アドレスまたはサブネットを追加するには、Azure Portal、PowerShell、CLI のいずれかを使用します。

SAP HANA on Azure サービス管理で追加の IP アドレス空間範囲をまだ宣言していない場合は、Azure サポート要求を開いて追加を依頼します。 確認通知を受信したら、次の手順を実行します。

追加のサブネットを Azure Portal で作成するには、「[Azure Portal を使用した仮想ネットワークの作成](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。PowerShell で作成するには、「[PowerShell を使用した仮想ネットワークの作成](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="adding-vnets"></a>VNet の追加

最初に&1; つ以上の Azure VNet を接続した後、SAP HANA on Azure (L インスタンス) にアクセスする追加の Azure VNet を作成することができます。 最初に Azure サポート要求を送信します。この要求には、特定の Azure デプロイを識別するための固有の情報と、追加の Azure VNet のテナント サブネットおよびゲートウェイ サブネット用の IP アドレス空間範囲を含めます。 その後、SAP HANA on Azure サービス管理から、追加の VNet および ExpressRoute を接続するために必要な情報が提供されます。

新しい Azure VNet を追加する手順:

1. オンボード プロセスの最初の手順を完了します。前のセクション「_Azure VNet の作成_」を参照してください。
2. オンボード プロセスの&2; 番目の手順を完了します。前のセクション「_ゲートウェイ サブネットの作成_」を参照してください。
3. 新しい VNet の情報で Azure サポート要求を開き、追加の VNet を HANA L インスタンスの ExpressRoute 回線に接続するための新しい承認キーを要求します。
4. 承認の完了通知を受け取った後、Microsoft から提供された承認情報を使用して、オンボード プロセスの&3; 番目の手順を完了します。上のセクション「_VNet のリンク_」を参照してください。

## <a name="increasing-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅の増加

SAP HANA on Azure サービス管理に問い合わせてください。 SAP HANA on Azure (L インスタンス) の ExpressRoute 回線の帯域幅を増やすよう推奨された場合は、Azure サポート要求を作成します  (1 つの回線につき、帯域幅を最大 10 Gbps 増やすよう要求することができます)。作業が完了すると通知が届きます。その他の作業は不要です。これで Azure が高速化されます。

## <a name="adding-an-additional-expressroute-circuit"></a>ExpressRoute 回線の追加

SAP HANA on Azure サービス管理に問い合わせてください。ExpressRoute 回線の追加を推奨された場合は、Azure サポート要求を作成して新しい ExpressRoute 回線の作成を要求します (また、それに接続するための承認情報も取得します)。 VNet で使用されるアドレス空間は、SAP HANA on Azure サービス管理での承認に必要となるため、要求を作成する前に定義しておく必要があります。

新しい回線の作成と、SAP HANA on Azure サービス管理の構成が完了した後、作業の続行に必要な情報が記載された通知が届きます。 上の手順に従って新しい VNet を作成し、この追加の回線に接続します。 既に別の ExpressRoute 回線に接続されている Azure VNet は、この追加の回線には接続できません。

## <a name="deleting-a-subnet"></a>サブネットの削除

VNet のサブネットを削除するには、Azure Portal、PowerShell、CLI のいずれかを使用することができます。 アドレス空間が削除される場合、SAP HANA on Azure (L インスタンス) が通信できるアドレス範囲からそのアドレス空間を削除する必要があるため、SAP HANA on Azure サービス管理にアドレス空間の変更を通知する必要があります。

まだ具体的な手順にはなっていませんが、Azure.com のサブネットの削除専用ガイダンス (サブネットの削除の手順) は、追加時とは逆の手順となります。 サブネットの作成の詳細については、「[Azure Portal を使用した仮想ネットワークの作成](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="deleting-a-vnet"></a>VNet の削除

VNet を削除するには、Azure Portal、PowerShell、CLI のいずれかを使用します。 SAP HANA on Azure サービス管理は、SAP HANA on Azure (L インスタンス) の ExpressRoute 回線での既存の承認を取り消し、HANA L インスタンスとの通信のための IP アドレス範囲 (テナントとゲートウェイの両方の範囲) を削除します。

VNet が削除された後、Azure サポート要求を開き、削除する IP アドレス空間範囲を指定します。

まだ具体的な手順にはなっていませんが、Azure.com の VNet の削除専用ガイダンス (VNet の削除の手順) は、上で説明されている追加時の手順とは逆になります。 VNet の作成の詳細については、「[Azure Portal を使用した仮想ネットワークの作成](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」と「[PowerShell を使用した仮想ネットワークの作成](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

すべてを確実に削除するために、次の項目を削除します。

- **Azure Resource Manager の場合:** ExpressRoute 接続、VNet ゲートウェイ、VNet ゲートウェイのパブリック IP、VNet
- **クラシック VM の場合:** VNet ゲートウェイ、VNet

## <a name="deleting-an-expressroute-circuit"></a>ExpressRoute 回線の削除

SAP HANA on Azure (L インスタンス) の追加の ExpressRoute 回線を削除するには、SAP HANA on Azure サービス管理で Azure サポート要求を開き、回線の削除を要求します。 Azure サブスクリプション内では、必要に応じて VNet を削除または保持することができます。 ただし、HANA L インスタンスの ExpressRoute 回線とリンクされた VNet ゲートウェイの間で、接続の削除 (Azure Resource Manager の場合) または接続リンクの解除 (クラシックの場合) のいずれかが必要です。

VNet も削除する場合は、前のセクション「VNet の削除」のガイダンスに従います。



