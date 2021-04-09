---
title: 仮想ネットワークから SAP HANA on Azure (L インスタンス) への接続の設定 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) を使用するための仮想ネットワークからの接続の設定。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 540247752be27af268a0485ea9eb68d121a25240
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775307"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>HANA Large Instances に仮想ネットワークを接続する

Azure Virtual Network を作成したら、SAP HANA on Azure Large Instances にそのネットワークを接続することができます。 仮想ネットワーク上に Azure ExpressRoute ゲートウェイを作成します。 このゲートウェイを使用すると、その仮想ネットワークを、HANA L インスタンス スタンプ上の顧客テナントに接続されている ExpressRoute 回線にリンクできます。

> [!NOTE] 
> この手順が完了するまで最大で 30 分かかることがあります。 新しいゲートウェイは指定された Azure サブスクリプション内で作成されてから、指定された Azure Virtual Network に接続されます。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

ゲートウェイが既に存在する場合は、それが ExpressRoute ゲートウェイかどうかを確認します。 それが ExpressRoute ゲートウェイでない場合は、そのゲートウェイを削除し、ExpressRoute ゲートウェイとして作成し直します。 ExpressRoute ゲートウェイが既に確立されている場合は、この記事の後述の「仮想ネットワークをリンクする」というセクションを参照してください。 

- [Azure Portal](https://portal.azure.com/) または PowerShell を使用して、お使いの仮想ネットワークに接続された ExpressRoute VPN ゲートウェイを作成します。
  - Azure Portal を使用する場合、新しい **Virtual Network ゲートウェイ** を追加し、ゲートウェイの種類として **[ExpressRoute]** を選択します。
  - PowerShell を使用する場合は、まず、最新の [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) をダウンロードして、それを使用します。 
 
次のコマンドは ExpressRoute ゲートウェイを作成します。 _$_ に続くテキストは、ユーザー固有の情報で更新する必要があるユーザー定義変数です。

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "UltraPerformance" # Supported values for HANA large instances are: UltraPerformance

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

SAP HANA on Azure (L インスタンス) でサポートされている唯一のゲートウェイ SKU は、**UltraPerformance** です。

## <a name="link-virtual-networks"></a>仮想ネットワークをリンクする

これで、Azure Virtual Network に ExpressRoute ゲートウェイが作成されました。 Microsoft から提供された承認情報を使用して、ExpressRoute ゲートウェイを SAP HANA L インスタンスの ExpressRoute 回線に接続します。 Azure Portal または PowerShell を使用して接続できます。 PowerShell での手順は次のとおりです。 

接続ごとに異なる AuthGUID を使用して、ExpressRoute ゲートウェイごとに次のコマンドを実行します。 次のスクリプトに示す最初の 2 つのエントリは、Microsoft から提供される情報です。 また、AuthGUID はすべての仮想ネットワークとそのゲートウェイに固有のものです。 別の Azure 仮想ネットワークを追加する場合は、HANA L インスタンスを Microsoft の Azure に接続する ExpressRoute 回線のための別の AuthID を取得する必要があります。 

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
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> コマンド New-AzVirtualNetworkGatewayConnection の最後のパラメーター **ExpressRouteGatewayBypass** は、ExpressRoute Fast Path を有効にする新しいパラメーターです。 これは、HANA L インスタンス ユニットと Azure VM の間のネットワーク待ち時間を短縮する機能です。 この機能は 2019 年 5 月に追加されました。 詳細については、「[SAP HANA (L インスタンス) のネットワーク アーキテクチャ](./hana-network-architecture.md)」の記事を確認してください。 このコマンドを実行する前に、最新バージョンの PowerShell コマンドレットを実行していることを確認してください。

サブスクリプションに関連付けられた複数の ExpressRoute 回線にゲートウェイを接続するには、この手順を複数回実行しなければならないことがあります。 たとえば、通常は同じ仮想ネットワーク ゲートウェイを、仮想ネットワークとオンプレミス ネットワークとを接続する ExpressRoute 回線に接続します。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>既存の HANA L インスタンスの ExpressRoute 回線への ExpressRoute Fast Path の適用
このドキュメントではここまで、HANA L インスタンス デプロイで作成された新しい ExpressRoute 回線を、いずれかの Azure 仮想ネットワークの Azure ExpressRoute ゲートウェイに接続する方法について説明してきました。 ただし、多くの顧客では既に ExpressRoute 回線が設定され、仮想ネットワークが既に HANA L インスタンスに接続されています。 新しい ExpressRoute Fast Path はネットワーク待ち時間を短縮するため、この機能を使用するための変更を適用することをお勧めします。 新しい ExpreesRoute 回線を接続するためのコマンドと、既存の ExpressRoute 回線を変更するためのコマンドは同じです。 そのため、使用する既存の回線を変更するには、この PowerShell コマンドのシーケンスを実行する必要があります。 

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
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

ExpressRoute Fast Path 機能を有効にするために、上に表示されている最後のパラメーターを追加することが重要です。


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
次の 2 つのシナリオのどちらかまたは両方に対して Global Reach を有効にする場合は、

 - 追加のプロキシまたはファイアウォールを使用しない HANA システム レプリケーション
 - システム コピーまたはシステム更新を実行するための 2 つの異なるリージョン内の HANA L インスタンス ユニット間でのバックアップのコピー

次のことを考慮する必要があります。

- /29 のアドレス空間のアドレス空間範囲を指定する必要があります。 そのアドレス範囲は、これまでに HANA L インスタンスを Azure に接続するために使用した他のどのアドレス空間範囲とも重複することはできず、さらに Azure 内またはオンプレミスのどこか別の場所で使用したどの IP アドレス範囲とも重複することはできません。
- オンプレミスのルートを HANA L インスタンスにアドバタイズするために使用できる ASN (自律システム番号) には制限があります。 オンプレミスでは、65000 – 65020 または 65515 の範囲のプライベート ASN を持つルートをアドバタイズできません。 
- オンプレミスの直接アクセスを HANA L インスタンスに接続するシナリオの場合は、Azure に接続する回線の料金を計算する必要があります。 価格については、[Global Reach アドオン](https://azure.microsoft.com/pricing/details/expressroute/)の価格を確認してください。

デプロイに適用するシナリオのどちらかまたは両方を取得するには、「[HANA large Instances のサポート リクエストを開く](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)」で説明されているように Azure でサポート メッセージを開きます。

必要なデータ、および Microsoft がそのリクエストをルーティングしたり対処したりできるようにするために使用する必要のあるキーワードは次のとおりです。

- サービス: SAP HANA L インスタンス
- 問題の種類: 構成とセットアップ
- 問題のサブタイプ: 問題が上記一覧に含まれていない
- 件名 'ネットワークを変更する - Global Reach を追加する'
- 詳細: 'HANA L インスタンスから HANA L インスタンス テナントに対して Global Reach を追加する' または 'オンプレミスから HANA L インスタンス テナントに対して Global Reach を追加する'。
- HANA L インスタンスから HANA L インスタンス テナントのケースの追加詳細: 接続する 2 つのテナントが配置されている **2 つの Azure リージョン** を定義する必要があり、**さらに**、 **/29 IP アドレス範囲** を送信する必要があります。
- オンプレミスから HANA L インスタンス テナントのケースの追加情報: 直接接続したい HANA L インスタンス テナントがデプロイされる **Azure リージョン** を定義する必要があります。 さらに、オンプレミスと Azure の間に ExpressRoute 回線を確立したときに受信した **Auth GUID** と **回線ピア ID** を指定する必要があります。 さらに、**ASN** に名前を付ける必要があります。 最後の提供物は、ExpressRoute Global Reach の **/29 の IP アドレス範囲** です。

> [!NOTE]
> 両方のケースが処理されるようにしたい場合は、これまでに使用された他のどの IP アドレス範囲とも重複しない 2 つの異なる /29 の IP アドレス範囲を指定する必要があります。 




## <a name="next-steps"></a>次のステップ

- [HLI 向け追加のネットワーク要件](hana-additional-network-requirements.md)
