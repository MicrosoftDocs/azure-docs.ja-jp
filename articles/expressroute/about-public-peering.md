---
title: Azure ExpressRoute パブリック ピアリングを作成して管理する
description: Azure パブリック ピアリングの管理について詳しく説明します
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: duau
ms.openlocfilehash: 477145619e1b4d8b41c422389b57a46615597478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202550"
---
# <a name="create-and-manage-expressroute-public-peering"></a>ExpressRoute パブリック ピアリングを作成して管理する

> [!div class="op_single_selector"]
> * [記事 - パブリック ピアリング](about-public-peering.md)
> * [ビデオ - パブリック ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [記事 - Microsoft ピアリング](expressroute-circuit-peerings.md#microsoftpeering)
>

この記事は、ExpressRoute 回線のパブリック ピアリング ルーティング構成の作成と管理に役立ちます。 また、状態確認のほか、ピアリングの更新、削除、およびプロビジョニング解除を行うこともできます。 この記事は、パブリック ピアリングが非推奨になる前に作成された Resource Manager 回線に適用されます。 以前存在していた回線 (パブリック ピアリングが非推奨になる前に作成されたもの) がある場合は、[Azure PowerShell](#powershell)、[Azure CLI](#cli)、および [Azure portal](#portal)を使用して、パブリック ピアリングを管理/構成することができます。

>[!NOTE]
>パブリック ピアリングは非推奨です。 新しい ExpressRoute 回線では、パブリック ピアリングは作成できません。 新しい ExpressRoute 回線がある場合は、代わりに [Microsoft ピアリング](expressroute-circuit-peerings.md#microsoftpeering) を Azure サービスに使用します。
>

## <a name="connectivity"></a>接続

接続は、常に WAN から Microsoft Azure サービスへ開始されます。 このルーティング ドメインを経由して Microsoft Azure サービスからお客様のネットワークに接続を開始することはできません。 ExpressRoute 回線が Azure ピアリングに対して有効になっている場合は、[Azure 内で使用されているパブリック IP の範囲](../virtual-network/public-ip-addresses.md#public-ip-addresses)に回線経由でアクセスできます。

パブリック ピアリングが有効になると、ほとんどの Azure サービスに接続できます。 ルートをアドバタイズするサービスを選択することはできません。

* Azure Storage、SQL Database、Web サイトなどのサービスは、パブリック IP アドレスで提供されます。
* パブリック ピアリング ルーティング ドメインを経由して、クラウド サービスの VIP などのパブリック IP アドレスでホストされているサービスにプライベート接続できます。
* パブリック ピアリング ドメインをご使用の DMZ に接続すれば、インターネット経由で接続しなくても WAN からパブリック IP アドレス上のすべての Azure サービスに接続できます。

## <a name="services"></a><a name="services"></a>サービス

このセクションでは、パブリック ピアリング経由で使用できるサービスについて説明します。 パブリック ピアリングは非推奨であるため、パブリック ピアリングに新規または追加サービスが追加される予定はありません。 パブリック ピアリングを使用している場合、使用するサービスが Microsoft ピアリング経由でしかサポートされていない場合は、Microsoft ピアリングに切り替える必要があります。 サポートされているサービスの一覧については、「[Microsoft ピアリング](expressroute-faqs.md#microsoft-peering)」を参照してください。

**サポート対象:**

* Power BI
* ほとんどの Azure サービスがサポートされています。 サポートの確認に使うサービスで直接確認してください。

**サポート対象外:**
  * CDN
  * Azure Front Door
  * Multi-Factor Authentication Server (レガシ)
  * Traffic Manager

特定のサービスの可用性を検証するには、そのサービスに関するドキュメントを調べて、そのサービスに対して発行された予約済みの範囲があるかどうかを確認します。 その後、ターゲット サービスの IP 範囲を参照し、[Azure IP 範囲とサービス タグ – パブリック クラウド XML ファイル](https://www.microsoft.com/download/details.aspx?id=56519)に関するページに一覧表示されている範囲と比較できます。 また、問題のサービス用のサポート チケットを開いて、詳細を確認することもできます。

## <a name="peering-comparison"></a><a name="compare"></a>ピアリングの比較

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Azure パブリック ピアリングでは、各 BGP セッションに 1 つの NAT IP アドレスが関連付けられます。 NAT IP アドレスが 2 つより多い場合には、Microsoft ピアリングに移行します。 Microsoft ピアリングを使用すると、独自の NAT 割り当てを構成したり、選択的なプレフィックス アドバタイズのルート フィルターを使用したりできます。 詳細については、「[Microsoft ピアリングに移行する](./how-to-move-peering.md)」を参照してください。
>

## <a name="custom-route-filters"></a>カスタム ルート フィルター

ネットワーク内でカスタム ルート フィルターを定義して、必要なルートのみを使用することができます。 ルーティング構成の詳細については、 [ルーティング](expressroute-routing.md) に関するページを参照してください。

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell の手順


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

パブリック ピアリングは非推奨であるため、新しい ExpressRoute 回線でパブリック ピアリングを構成することはできません。

1. プロビジョニングされ、有効になっている ExpressRoute 回線があることを確認します。 次の例を使用してください。

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
2. 回線用に Azure パブリック ピアリングを構成します。 作業を続行する前に、次の情報がそろっていることを確認します。

   * プライマリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。
   * セカンダリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * 省略可能:
   * いずれかを使用する場合は、MD5 ハッシュ。

   次の例を実行して、ご使用の回線用に Azure パブリック ピアリングを構成します。

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   MD5 ハッシュを使用する場合は、次の例を使用します。

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > 顧客 ASN ではなく、ピアリング ASN として AS 番号を指定するようにしてください。
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Azure パブリック ピアリングの詳細を取得するには

次のコマンドレットを使用して、構成の詳細を取得できます。

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure パブリック ピアリング構成を更新するには

構成の任意の部分を更新するには、次の例を使用します。 この例では、回線の VLAN ID が 200 から 600 に更新されています。

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure パブリック ピアリングを削除するには

ピアリング構成を削除するには、次の例を実行します。

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLI の手順


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. ExpressRoute 回線がプロビジョニングされ、有効になっていることを確認します。 次の例を使用してください。

   ```azurecli-interactive
   az network express-route list
   ```

   応答は次の例のようになります。

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. 回線用に Azure パブリック ピアリングを構成します。 作業を続行する前に、次の情報がそろっていることを確認します。

   * プライマリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。
   * セカンダリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * **省略可能 -** MD5 を使用する場合には、パスワードを準備します。

   次の例を実行して、回線用に Azure パブリック ピアリングを構成します。

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   MD5 ハッシュを使用する場合は、次の例を使用します。

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 顧客 ASN ではなく、ピアリング ASN として AS 番号を指定するようにしてください。

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Azure パブリック ピアリングの詳細を表示するには

構成の詳細を取得するには、次の例を使用します。

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

出力は次の例のようになります。

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure パブリック ピアリング構成を更新するには

構成の任意の部分を更新するには、次の例を使用します。 この例では、回線の VLAN ID が 200 から 600 に更新されています。

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure パブリック ピアリングを削除するには

ピアリング構成を削除するには、次の例を実行します。

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure portal の手順

ピアリングを構成するには、この記事に記載されている PowerShell または CLI の手順を使用します。 ピアリングを管理するには、以下のセクションを使用できます。 なお、これらの手順は、[ポータルの Microsoft ピアリング](expressroute-howto-routing-portal-resource-manager.md#msft)を管理する手順と似ています。

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Azure パブリック ピアリングの詳細を表示するには

ポータルでピアリングを選択して、Azure パブリック ピアリングのプロパティを表示します。

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Azure パブリック ピアリング構成を更新するには

ピアリングの行を選択し、ピアリングのプロパティを変更します。

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Azure パブリック ピアリングを削除するには

削除アイコンを選択してピアリングの構成を削除します。

## <a name="next-steps"></a>次のステップ

次に、[仮想ネットワークを ExpressRoute 回線にリンク](expressroute-howto-linkvnet-arm.md)します。

* ExpressRoute ワークフローの詳細については、「 [ExpressRoute ワークフロー](expressroute-workflows.md)」を参照してください。
* 回路ピアリングの詳細については、「 [ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。
* 仮想ネットワークの詳細については、「 [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」を参照してください。