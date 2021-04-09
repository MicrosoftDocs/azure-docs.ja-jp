---
title: チュートリアル:ExpressRoute 回線のピアリングの構成 - Azure PowerShell
description: このチュートリアルでは、PowerShell を使用して Resource Manager デプロイ モデルで ExpressRoute 回線のルーティング構成を作成および管理する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 7cfd378ae621192cd98b482b66c85c3dcd3ca454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721941"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>チュートリアル:PowerShell を使用した ExpressRoute 回線のピアリングの作成と変更

このチュートリアルでは、PowerShell を使用して Resource Manager デプロイ モデルで ExpressRoute 回線のルーティング構成を作成および管理する方法について説明します。 また、ExpressRoute 回線の状態確認、ピアリングの更新、または削除およびプロビジョニング解除を行うこともできます。 別の方法を使用して回線を操作する場合は、次の一覧から記事を選択してください。

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [パブリック ピアリング](about-public-peering.md)
> * [ビデオ - プライベート ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [ビデオ - Microsoft ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-routing-classic.md)
> 

次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供するマネージド レイヤー 3 サービス (MPLS など、通常は IPVPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。

> [!IMPORTANT]
> 現在のところ、サービス管理ポータルでは、サービス プロバイダーが構成したピアリングをアドバタイズしていません。 できるだけ早くこの機能を提供できるように取り組んでいます。 BGP ピアリングを構成する前に、ご利用のサービス プロバイダーにお問い合わせください。
> 

ExpressRoute 回線にプライベート ピアリングおよび Microsoft ピアリングを構成できます (Azure パブリック ピアリングは新しい回線では非推奨です)。 ピアリングは、任意の順序で構成できます。 ただし、各ピアリングの構成は必ず一度に 1 つずつ完了するようにしてください。 ルーティング ドメインとピアリングの詳細については、[ExpressRoute のルーティング ドメイン](expressroute-circuit-peerings.md)に関する記事をご覧ください。 パブリック ピアリングについては、[ExpressRoute のパブリック ピアリング](about-public-peering.md)に関する記事をご覧ください。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - 回線用に Microsoft ピアリングを構成、更新、削除する
> - 回線用に Azure プライベート ピアリングを構成、更新、削除する

## <a name="prerequisites"></a>前提条件

* 構成を開始する前に、次のページを確認してください。
    * [前提条件](expressroute-prerequisites.md) 
    * [ルーティングの要件](expressroute-routing.md)
    * [ワークフロー](expressroute-workflows.md)
* アクティブな ExpressRoute 回線が必要です。 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。 この記事にあるコマンドレットを実行するには、ExpressRoute 回線をプロビジョニング済みで有効化された状態にする必要があります。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft ピアリング

このセクションでは、ExpressRoute 回線用の Microsoft ピアリング構成を作成、取得、更新、および削除します。

> [!IMPORTANT]
> 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。 2017 年 8 月 1 日以降に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが回線に接続されるまで、プレフィックスはアドバタイズされません。 詳しくは、「[Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md)」(Microsoft ピアリング用にルート フィルターを構成する) をご覧ください。
> 

### <a name="to-create-microsoft-peering"></a>Microsoft ピアリングを作成するには

1. サインインして、使用するサブスクリプションを選択します。

   PowerShell をローカルにインストールした場合は、サインインします。 Azure Cloud Shell を使用している場合は、この手順を省略できます。

   ```azurepowershell
   Connect-AzAccount
   ```

   ExpressRoute 回線を作成するサブスクリプションを選択します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. ExpressRoute 回線を作成します。

   手順に従って、 [ExpressRoute 回線](expressroute-howto-circuit-arm.md) を作成し、接続プロバイダー経由で回線をプロビジョニングします。 接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Microsoft ピアリングを有効にするように接続プロバイダーに依頼できます。 次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、次の手順を使用して、構成を続行します。 

3. ExpressRoute 回線がプロビジョニングされ、有効になっていることを確認します。 次の例を使用してください。

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   応答は次の例のようになります。

   ```
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
   ```
4. 回路の Microsoft ピアリングを構成する 続行する前に、次の情報を確認してください。

   * プライマリ リンク用の /30 または /126 サブネット。 アドレス ブロックは、自分が所有し、RIR/IRR に登録されている有効なパブリック IPv4 または IPv6 プレフィックスである必要があります。
   * セカンダリ リンク用の /30 または /126 サブネット。 アドレス ブロックは、自分が所有し、RIR/IRR に登録されている有効なパブリック IPv4 または IPv6 プレフィックスである必要があります。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * アドバタイズするプレフィックス:BGP セッションを介してアドバタイズする予定のすべてのプレフィックスのリストを指定します。 パブリック IP アドレス プレフィックスのみが受け入れられます。 一連のプレフィックスを送信する場合は、コンマ区切りのリストを送信できます。 これらのプレフィックスは、RIR/IRR に登録する必要があります。 IPv4 BGP セッションには IPv4 のアドバタイズされたプレフィックス、IPv6 BGP セッションには IPv6 のアドバタイズされたプレフィックスが必要です。 
   * ルーティング レジストリ名: AS 番号とプレフィックスを登録する RIR/IRR を指定することができます。
   * 省略可能:
     * 顧客 ASN: ピアリング AS 番号に登録されていないプレフィックスをアドバタイズしている場合は、それらが登録されている AS 番号を指定できます。
     * いずれかを使用する場合は、MD5 ハッシュ。

> [!IMPORTANT]
> Microsoft は、指定された 'アドバタイズされたパブリック プレフィックス' と 'ピア ASN' (または '顧客 ASN') がインターネット ルーティング レジストリでユーザーに割り当てられているかどうかを確認します。 別のエンティティからパブリック プレフィックスを取得している場合、およびルーティング レジストリに割り当てが記録されていない場合、自動検証は完了せず、手動検証が必要になります。 自動検証が失敗した場合は、"Get-AzExpressRouteCircuitPeeringConfig" の出力に 'AdvertisedPublicPrefixesState' が '検証が必要です' として表示されます (次のセクションの「Microsoft ピアリングの詳細を取得するには」を参照)。 
> 
> '検証が必要です' というメッセージが表示された場合は、ルーティング レジストリにプレフィックスの所有者として一覧表示されているエンティティによって組織にパブリック プレフィックスが割り当てられていることを示すドキュメントを収集し、サポート チケットを開くことにより、これらのドキュメントを手動検証のために送信してください。 
> 

   次の例を使用して、ご使用の回線用に Microsoft ピアリングを構成します。

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft ピアリングの詳細を取得するには

構成の詳細を取得するには、次の例を使用します。

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft ピアリング構成を更新するには

次の例を使用して構成の任意の部分を更新することができます。

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Azure プライベート ピアリング

このセクションでは、ExpressRoute 回線用の Azure プライベート ピアリング構成を作成、取得、更新、および削除します。

> [!IMPORTANT]
> 現在、プライベート ピアリングの IPv6 サポートは **パブリック プレビュー** の段階です。 
> 
> 

### <a name="to-create-azure-private-peering"></a>Azure プライベート ピアリングを作成するには

1. ExpressRoute 用の PowerShell モジュールをインポートします。

   [PowerShell ギャラリー](https://www.powershellgallery.com/)から最新の PowerShell インストーラーをインストールします。 次に、ExpressRoute コマンドレットの使用を開始するために、Azure Resource Manager モジュールを PowerShell セッションにインポートします。 管理者として PowerShell を実行する必要があります。

   ```azurepowershell-interactive
   Install-Module Az
   ```

   既知のセマンティック バージョン範囲内の Az.\* モジュールをすべてインポートします。

   ```azurepowershell-interactive
   Import-Module Az
   ```

   既知のセマンティック バージョン範囲内の選択したモジュールのみをインポートすることもできます。

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   ご使用のアカウントにサインインします。

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   ExpressRoute 回線を作成するサブスクリプションを選択します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. ExpressRoute 回線を作成します。

   手順に従って、 [ExpressRoute 回線](expressroute-howto-circuit-arm.md) を作成し、接続プロバイダー経由で回線をプロビジョニングします。 接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに依頼できます。 次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、次の手順を使用して、構成を続行します。

3. ExpressRoute 回線がプロビジョニングされ、有効になっていることを確認します。 次の例を使用してください。

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   応答は次の例のようになります。

   ```
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
   ```
4. 回線用に Azure プライベート ピアリングを構成します。 次の手順に進む前に、以下のものがそろっていることを確認します。

   * 仮想ネットワーク用に予約されたアドレス空間の一部ではないサブネットのペア。 一方のサブネットはプライマリ リンク用に使用され、もう一方はセカンダリ リンク用に使用されます。 これらの各サブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。 このサブネットのペアには、3 つのオプションがあります。
       * IPv4: 2 つの /30 サブネット。
       * IPv6: 2 つの /126 サブネット。
       * 両方: 2 つの /30 サブネットと 2 つの /126 サブネット。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。 このピアリングではプライベート AS 番号を使用できます。 65515 は使用しないようにしてください。
   * 省略可能:
     * いずれかを使用する場合は、MD5 ハッシュ。

   次の例を使用して、回線用に Azure プライベート ピアリングを構成します。

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   MD5 ハッシュを使用する場合は、次の例を使用します。

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 顧客 ASN ではなく、ピアリング ASN として AS 番号を指定するようにしてください。
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Azure プライベート ピアリングの詳細を取得するには

構成の詳細を取得するには、次の例を使用します。

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure プライベート ピアリングの構成を更新するには

構成の任意の部分を更新するには、次の例を使用します。 この例では、回線の VLAN ID が 200 から 500 に更新されています。

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft ピアリングを削除するには

以下のコマンドレットを実行して、ピアリング構成を削除することができます。

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure プライベート ピアリングを削除するには

ピアリング構成を削除するには、次の例を実行します。

> [!WARNING]
> この例を実行する前に、すべての仮想ネットワークと ExpressRoute Global Reach 接続が削除されていることを確認する必要があります。 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>次のステップ

Azure プライベート ピアリングを構成すると、ExpressRoute ゲートウェイを作成して、仮想ネットワークを回線にリンクすることができます。 

> [!div class="nextstepaction"]
> [ExpressRoute の仮想ネットワーク ゲートウェイを構成する](expressroute-howto-add-gateway-resource-manager.md)
