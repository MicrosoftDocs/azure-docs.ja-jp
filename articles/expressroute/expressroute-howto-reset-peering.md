---
title: 'Azure ExpressRoute: 回線のピアリングをリセットする'
description: Azure PowerShell を使用して Azure ExpressRoute 回線のピアリングを有効および無効にする方法について説明します。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97559575"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute 回線のピアリングをリセットする

この記事では、PowerShell を使用して ExpressRoute 回線のピアリングを有効および無効にする方法について説明します。 ピアリングは、作成時に既定で有効になっています。 ピアリングを無効にすると、ExpressRoute 回線のプライマリとセカンダリ接続の両方の BGP セッションがシャットダウンされます。 Microsoft へのこのピアリングの接続が失われます。 ピアリングを有効にすると、ExpressRoute 回線のプライマリとセカンダリ接続の両方の BGP セッションが確立されます。 このピアリングについて、Microsoft への接続が復元されます。 ExpressRoute 回線上の Microsoft ピアリングと Azure プライベート ピアリングのピアリングを別々に有効および無効にすることができます。

ExpressRoute ピアリングをリセットすると役立つ可能性のあるシナリオが 2 つ存在します。
* ディザスター リカバリーの設計と実装をテストする場合。 たとえば、2 つの ExpressRoute 回線があるとします。 一方の回線のピアリングを無効にして、ネットワーク トラフィックをもう一方の回線に強制的にフェールオーバーするようにできます。
* ExpressRoute 回線の Azure プライベート ピアリングまたは Microsoft ピアリングのどちらかで BFD (Bidirectional Forwarding Detection) を有効にします。 BFD は、Azure プライベート ピアリングでは ExpressRoute 回線が 2018 年 8 月 1 日より後に作成され、Microsoft ピアリングでは ExpressRoute 回線が 2020 年 1 月 10 日より後に作成された場合、既定で有効です。 一覧表示された日付より前に回線が作成された場合は、ピアリングをリセットして BFD を有効にする必要があります。 

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>ピアリングをリセットする

1. PowerShell をローカルで実行している場合は、昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

   ```azurepowershell
   Connect-AzAccount
   ```
2. 複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. 使用するサブスクリプションを指定します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. 次のコマンドを実行して、ExpressRoute 回線を取得します。

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. 無効または有効にするピアリングを識別します。 *Peerings* は配列です。 次の例では、Peerings[0] は Azure プライベート ピアリングであり、Peerings[1] は Microsoft ピアリングです。

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
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
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. 次のコマンドを実行して、ピアリングの状態を変更します。

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   ピアリングが、設定された状態になります。 

## <a name="next-steps"></a>次のステップ
ExpressRoute の問題をトラブルシューティングするためにサポートが必要な場合は、次の記事をご覧ください。
* [ExpressRoute 接続の確認](expressroute-troubleshooting-expressroute-overview.md)
* [ネットワーク パフォーマンスのトラブルシューティング](expressroute-troubleshooting-network-performance.md)
