---
title: 'Azure ExpressRoute: ExpressRoute Direct を構成する'
description: 世界中のピアリングの場所で Microsoft のグローバル ネットワークに直接接続するため、Azure PowerShell を使用して Azure ExpressRoute Direct を構成する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/22/2020
ms.author: duau
ms.openlocfilehash: c4ce764f50f85ef9979d5a14235759c16228f6b7
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396031"
---
# <a name="how-to-configure-expressroute-direct"></a>ExpressRoute Direct を構成する方法

ExpressRoute Direct を使用すると、世界中に戦略的に分散されたピアリングの場所で Microsoft のグローバル ネットワークに直接接続できます。 詳細については、[ExpressRoute Direct](expressroute-erdirect-about.md) に関するページを参照してください。

## <a name="create-the-resource"></a><a name="resources"></a>リソースを作成する

1. Azure にサインインしてサブスクリプションを選択します。 ExpressRoute Direct リソースおよび ExpressRoute 回線は、同じサブスクリプション内にある必要があります。

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Expressrouteportslocation および expressrouteport API にアクセスするには、Microsoft.Network へのサブスクリプションを再登録してください。

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. ExpressRoute Direct がサポートされるすべての場所を一覧表示します。
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **出力例**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
4. 上記で表示された場所に使用可能な帯域幅があるかどうか確認します。

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **出力例**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
5. 上記で選択した場所に基づいて ExpressRoute Direct リソースを作成します。

   ExpressRoute Direct では、QinQ と Dot1Q 両方のカプセル化がサポートされます。 QinQ を選択した場合、各 ExpressRoute 回線に S-Tag が動的に割り当てられ、ExpressRoute Direct リソース全体で一意になります。 回線上の各 C-Tag はその回線で一意である必要がありますが、ExpressRoute Direct 全体ではありません。  

   Dot1Q カプセル化を選択した場合、ExpressRoute Direct リソース全体で C-Tag (VLAN) が一意になるように管理する必要があります。  

   > [!IMPORTANT]
   > ExpressRoute Direct ではカプセル化の種類を 1 つしか選択できません。 ExpressRoute Direct を作成した後でカプセル化を変更することはできません。
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > Encapsulation 属性に Dot1Q を設定することもできます。 
   >

   **出力例:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>リンクの管理状態を変更する

  このプロセスは、レイヤー 1 のテストを実施して、各相互接続がプライマリとセカンダリの各ルーターに適切に接続されていることを確認するために使用する必要があります。
1. ExpressRoute Direct の詳細を取得します。

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. リンクを Enabled に設定します。 この手順を繰り返して、各リンクを有効に設定します。

   Links[0] はプライマリ ポート、Links[1] はセカンダリ ポートです。

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **出力例:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   同じ手順を `AdminState = "Disabled"` について使用して、ポートを停止します。

## <a name="create-a-circuit"></a><a name="circuit"></a>回線を作成する

既定では、ExpressRoute Direct リソースがあるサブスクリプション内に 10 個の回線を作成できます。 これはサポートによって増やすことができます。 プロビジョニング済みの帯域幅と使用済みの帯域幅の両方を追跡してください。 プロビジョニング済み帯域幅は、ExpressRoute Direct リソース上のすべての回線の帯域幅の合計です。使用済み帯域幅は、基になる物理インターフェイスの物理的な使用量です。

ExpressRoute Direct には、前述したシナリオをサポートするために利用できるだけの追加の回線帯域幅があります。 次のとおりです。40 Gbps と 100 Gbps です。

**SkuTier** には Local、Standard、または Premium を使用できます。

ExpressRoute Direct では無制限がサポートされていないため、**SkuFamily** には MeteredData のみを使用できます。

ExpressRoute Direct リソース上に回線を作成します。

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  他の帯域幅には、5.0、10.0、および 40.0 が含まれます。

  **出力例:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>次のステップ

ExpressRoute Direct について詳しくは、[概要](expressroute-erdirect-about.md)のページをご覧ください。
