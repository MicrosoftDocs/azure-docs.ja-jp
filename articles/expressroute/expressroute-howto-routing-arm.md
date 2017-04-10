---
title: "ExpressRoute 回線のルーティング (ピアリング) を構成する方法: Resource Manager: PowerShell: Azure | Microsoft Docs"
description: "この記事では、ExpressRoute 回線のプライベート、パブリックおよび Microsoft ピアリングを作成し、プロビジョニングする手順について説明します。 この記事では、回線のピアリングの状態確認、更新、または削除の方法も示します。"
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 160560fcc3d586d2bbcba67d2f7c60cfed26f5c3
ms.lasthandoff: 03/24/2017


---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>PowerShell を使用した ExpressRoute 回線のピアリングの作成と変更
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [クラシック- PowerShell](expressroute-howto-routing-classic.md)
> * [ビデオ - プライベート ピアリング](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [ビデオ - パブリック ピアリング](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [ビデオ - Microsoft ピアリング](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

この記事では、PowerShell と Azure リソース マネージャーのデプロイメント モデルを使用して、ExpressRoute 回線のルーティング構成を作成して管理する手順について説明します。  以下の手順では、ExpressRoute 回線の状態確認、ピアリングの更新、または削除およびプロビジョニング解除の方法も示します。 

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>構成の前提条件
* Azure Resource Manager PowerShell コマンドレットの最新版をインストールする必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。 
* 構成を開始する前に必ず、[前提条件](expressroute-prerequisites.md)ページ、[ルーティングの要件](expressroute-routing.md)ページおよび[ワークフロー](expressroute-workflows.md) ページを確認してください。
* アクティブな ExpressRoute 回線が必要です。 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。 ExpressRoute 回線をプロビジョニングされ、有効になっている状態にする必要があります。そうすれば、以下で説明されているコマンドレットを実行できます。

次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IPVPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。

> [!IMPORTANT]
> 現在のところ、サービス管理ポータルでは、サービス プロバイダーが構成したピアリングをアドバタイズしていません。 できるだけ早くこの機能を提供できるように取り組んでいます。 BGP ピアリングを構成する前に、ご利用のサービス プロバイダーにお問い合わせください。
> 
> 

ExpressRoute 回線用に 1 つ、2 つ、または 3 つすべてのピアリング (Azure プライベート、Azure パブリックおよび Microsoft) を構成することができます。 ピアリングは任意の順序で構成することができます。 ただし、各ピアリングの構成は必ず一度に 1 つずつ完了するようにしてください。 

## <a name="azure-private-peering"></a>Azure プライベート ピアリング
このセクションでは、ExpressRoute 回線用の Azure プライベート ピアリング構成を作成、取得、更新、および削除する方法について説明します。 

### <a name="to-create-azure-private-peering"></a>Azure プライベート ピアリングを作成するには
1. ExpressRoute 用の PowerShell モジュールをインポートします。
   
     ExpressRoute コマンドレットを使用するには、 [PowerShell ギャラリー](http://www.powershellgallery.com/) から最新の PowerShell インストーラーをインストールし、Azure リソース マネージャー モジュールを PowerShell セッションにインポートする必要があります。 管理者として PowerShell を実行する必要があります。
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    既知のセマンティック バージョン範囲内の AzureRM.* モジュールをすべてインポートします。
   
        Import-AzureRM
   
    既知のセマンティック バージョン範囲内の選択したモジュールのみをインポートすることもできます。 
   
        Import-Module AzureRM.Network 
   
    ご使用のアカウントにログオンします。
   
        Login-AzureRmAccount
   
    ExpressRoute 回線を作成するサブスクリプションを選択します。
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. ExpressRoute 回線を作成します。
   
    手順に従って、 [ExpressRoute 回線](expressroute-howto-circuit-arm.md) を作成し、接続プロバイダー経由で回線をプロビジョニングします。 
   
    接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに要求できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、以下の手順に従います。 
3. ExpressRoute 回線がプロビジョニングされていることを確認します。
   
    まず、ExpressRoute 回線がプロビジョニングされており、有効にもなっているかどうかを確認する必要があります。 次の例を見てください。
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    応答は、以下の例のようになります。
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []
4. 回線用に Azure プライベート ピアリングを構成します。
   
    次の手順に進む前に、以下のものがそろっていることを確認します。
   
   * プライマリ リンク用の /30 サブネット。 これを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。
   * セカンダリ リンク用の /30 サブネット。 これを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。 このピアリングではプライベート AS 番号を使用できます。 65515 を使用しないようにしてください。
   * いずれかを使用する場合は、MD5 ハッシュ。 **これは省略可能です**。
     
    次のコマンドレットを実行して、回線用に Azure プライベート ピアリングを構成することができます。
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200
     
          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
    MD5 ハッシュを使用する場合は、以下のコマンドレットを使用できます。
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

     
   > [!IMPORTANT]
   > 顧客 ASN ではなく、ピアリング ASN として AS 番号を指定するようにしてください。
   > 
   >


### <a name="to-view-azure-private-peering-details"></a>Azure プライベート ピアリングの詳細を表示するには
次のコマンドレットを使用して、構成の詳細を取得できます。

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt    


### <a name="to-update-azure-private-peering-configuration"></a>Azure プライベート ピアリングの構成を更新するには
次のコマンドレットを使用して、構成のどの部分でも更新することができます。 次の例では、回路の VLAN ID が 100 から 500 に更新されています。

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Azure プライベート ピアリングを削除するには
以下のコマンドレットを実行して、ピアリング構成を削除することができます。

> [!WARNING]
> このコマンドレットを実行する前に、すべての仮想ネットワークが ExpressRoute 回線からリンク解除されていることを確認する必要があります。 
> 
> 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Azure パブリック ピアリング
このセクションでは、ExpressRoute 回線用の Azure パブリック ピアリング構成を作成、取得、更新および削除する方法について説明します。

### <a name="to-create-azure-public-peering"></a>Azure パブリック ピアリングを作成するには
1. ExpressRoute 用の PowerShell モジュールをインポートします。
   
     ExpressRoute コマンドレットを使用するには、 [PowerShell ギャラリー](http://www.powershellgallery.com/) から最新の PowerShell インストーラーをインストールし、Azure リソース マネージャー モジュールを PowerShell セッションにインポートする必要があります。 管理者として PowerShell を実行する必要があります。
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    既知のセマンティック バージョン範囲内の AzureRM.* モジュールをすべてインポートします。
   
        Import-AzureRM
   
    既知のセマンティック バージョン範囲内の選択したモジュールのみをインポートすることもできます。 
   
        Import-Module AzureRM.Network 
   
    ご使用のアカウントにログオンします。
   
        Login-AzureRmAccount
   
    ExpressRoute 回線を作成するサブスクリプションを選択します。
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. ExpressRoute 回線を作成します。
   
    手順に従って、 [ExpressRoute 回線](expressroute-howto-circuit-arm.md) を作成し、接続プロバイダー経由で回線をプロビジョニングします。 
   
    接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure パブリック ピアリングを有効にするように接続プロバイダーに要求できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、以下の手順に従います。
3. ExpressRoute 回線がプロビジョニングされていることを確認します。
   
    まず、ExpressRoute 回線がプロビジョニングされており、有効にもなっているかどうかを確認する必要があります。 次の例を見てください。
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    応答は、以下の例のようになります。
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. 回線用に Azure パブリック ピアリングを構成します。
   
       Make sure that you have the following information before you proceed further.
   
   * プライマリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。
   * セカンダリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * いずれかを使用する場合は、MD5 ハッシュ。 **これは省略可能です**。

    
    次のコマンドレットを実行して、回線用に Azure パブリック ピアリングを構成することができます。
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
    MD5 ハッシュを使用する場合は、以下のコマンドレットを使用できます。
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

     
> [!IMPORTANT]
> 顧客 ASN ではなく、ピアリング ASN として AS 番号を指定するようにしてください。
> 
>


### <a name="to-view-azure-public-peering-details"></a>Azure パブリック ピアリングの詳細を表示するには
次のコマンドレットを使用して、構成の詳細を取得できます。

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt

### <a name="to-update-azure-public-peering-configuration"></a>Azure パブリック ピアリング構成を更新するには
次のコマンドレットを使用して、構成のどの部分も更新することができます。

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

上記の例では、回線の VLAN ID は 200 から 600 に更新されています。

### <a name="to-delete-azure-public-peering"></a>Azure パブリック ピアリングを削除するには
以下のコマンドレットを実行して、ピアリング構成を削除することができます。

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Microsoft ピアリング
このセクションでは、ExpressRoute 回線の Microsoft ピアリング構成を作成、取得、更新および削除する方法について説明します。 

### <a name="to-create-microsoft-peering"></a>Microsoft ピアリングを作成するには
1. ExpressRoute 用の PowerShell モジュールをインポートします。
   
     ExpressRoute コマンドレットを使用するには、 [PowerShell ギャラリー](http://www.powershellgallery.com/) から最新の PowerShell インストーラーをインストールし、Azure リソース マネージャー モジュールを PowerShell セッションにインポートする必要があります。 管理者として PowerShell を実行する必要があります。
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    既知のセマンティック バージョン範囲内の AzureRM.* モジュールをすべてインポートします。
   
        Import-AzureRM
   
    既知のセマンティック バージョン範囲内の選択したモジュールのみをインポートすることもできます。 
   
        Import-Module AzureRM.Network 
   
    ご使用のアカウントにログオンします。
   
        Login-AzureRmAccount
   
    ExpressRoute 回線を作成するサブスクリプションを選択します。
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. ExpressRoute 回線を作成します。
   
    手順に従って、 [ExpressRoute 回線](expressroute-howto-circuit-arm.md) を作成し、接続プロバイダー経由で回線をプロビジョニングします。 
   
    接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに要求できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、以下の手順に従います。
3. ExpressRoute 回線がプロビジョニングされていることを確認します。
   
    まず、ExpressRoute 回線がプロビジョニングされており、有効にもなっているかどうかを確認する必要があります。 次の例を見てください。
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    応答は、以下の例のようになります。
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. 回路の Microsoft ピアリングを構成する
   
    続行する前に、次の情報を確認してください。
   
   * プライマリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。
   * セカンダリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * アドバタイズされたプレフィックス: BGP セッションを介してアドバタイズする予定のすべてのプレフィックスのリストを指定する必要があります。 パブリック IP アドレス プレフィックスのみが受け入れられます。 一連のプレフィックスを送信する予定の場合は、コンマ区切りのリストを送信できます。 これらのプレフィックスは、RIR/IRR に登録する必要があります。
   * 顧客 ASN: ピアリング AS 番号に登録されていないプレフィックスをアドバタイズする場合は、そのプレフィックスが登録されている AS 数を指定できます。 **これは省略可能です**。
   * ルーティング レジストリ名: AS 番号とプレフィックスを登録する RIR/IRR を指定することができます。
   * いずれかを使用する場合は、MD5 ハッシュ。 **これは省略可能です。**
     
      次のコマンドレットを実行して、回線用に Microsoft ピアリングを構成することができます。
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"
     
          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-get-microsoft-peering-details"></a>Microsoft ピアリングの詳細を取得するには
次のコマンドレットを使用して、構成の詳細を取得できます。

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Microsoft ピアリング構成を更新するには
次のコマンドレットを使用して、構成のどの部分も更新することができます。

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-microsoft-peering"></a>Microsoft ピアリングを削除するには
以下のコマンドレットを実行して、ピアリング構成を削除することができます。

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>次のステップ
次の手順では、 [ExpressRoute 回線に VNet をリンク](expressroute-howto-linkvnet-arm.md)します。

* ExpressRoute ワークフローの詳細については、「 [ExpressRoute ワークフロー](expressroute-workflows.md)」を参照してください。
* 回路ピアリングの詳細については、「 [ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。
* 仮想ネットワークの詳細については、「 [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」を参照してください。


