---
title: 仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する:Azure Resource Manager | Microsoft Docs
description: 仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成します。
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: d5e62bf1838c8f07068208019d28d7273c28bd63
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492347"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する

この記事は、仮想ネットワーク ピアリングのゲートウェイ転送を構成するために役立ちます。 [仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)は、2 つの Azure 仮想ネットワークをシームレスに接続し、その 2 つの仮想ネットワークを接続目的で 1 つにマージします。 [ゲートウェイ転送](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)は、1 つの仮想ネットワークがクロスプレミス接続または VNet 間接続用にピアリングされた仮想ネットワーク内の VPN ゲートウェイを活用できるようにするピアリング プロパティです。 次の図は、仮想ネットワーク ピアリングでのゲートウェイ転送のしくみを示しています。

![ゲートウェイ転送](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

図では、ピアリングされた仮想ネットワークは、ゲートウェイ転送によって、Hub-RM 内の Azure VPN ゲートウェイを使用できます。 VPN ゲートウェイで使用可能な接続 (S2S、P2S、および VNet 間接続を含みます) は、3 つの仮想ネットワークのすべてに適用されます。 トランジット オプションは、同じデプロイ モデル間のピアリングでも異なるデプロイ モデル間のピアリングでも使用できます。 制約は、VPN ゲートウェイは、図に示すように、Resource Manager デプロイ モデルを使用する仮想ネットワーク内にのみ存在できることです。

ハブ アンド スポーク ネットワーク アーキテクチャでは、スポーク 仮想ネットワークは、ゲートウェイ転送によって、ハブ内の VPN ゲートウェイを共有でき、VPN ゲートウェイをすべてのスポーク仮想ネットワークにデプロイする必要はありません。 ゲートウェイに接続された仮想ネットワークまたはオンプレミス ネットワークへのルートは、ピアリングされた仮想ネットワークのルーティング テーブルにゲートウェイ転送を使用して伝達されます。 VPN ゲートウェイからの自動ルート伝達は無効にできます。 ルーティング テーブルを **[BGP ルート伝達を無効にする]** オプションを指定して作成し、そのルーティング テーブルをサブネットに関連付けて、これらのサブネットへのルート配布を防ぎます。 詳細については、[仮想ネットワーク ルーティング テーブル](../virtual-network/manage-route-table.md)に関する記事を参照してください。

このドキュメントで説明するシナリオは 2 つあります。

1. 2 つの仮想ネットワークの両方が Resource Manager デプロイ モデルを使用している
2. スポーク仮想ネットワークがクラシックであり、ゲートウェイを含むハブ仮想ネットワークが Resource Manager である

## <a name="requirements"></a>必要条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このドキュメントの例では、次のリソースを作成する必要があります。

1. VPN ゲートウェイがある Hub-RM 仮想ネットワーク
2. Spoke-RM 仮想ネットワーク
3. クラシック デプロイ モデルを使用する Spoke-Classic 仮想ネットワーク
4. 使用するアカウントは、必要なロールとアクセス許可を持っている必要があります。 詳細については、この記事の「[アクセス許可](#permissions)」セクションを参照してください。

手順については、次のドキュメントを参照してください。

1. [仮想ネットワーク内に VPN ゲートウェイを作成する](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [デプロイ モデルが同じ仮想ネットワークのピアリングを作成する](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [デプロイ モデルが異なる仮想ネットワークのピアリングを作成する](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a>アクセス許可

仮想ネットワーク ピアリングの作成に使用するアカウントは、必要なロールまたはアクセス許可を持っている必要があります。 次の例では、Hub-RM と Spoke-Classic という名前の 2 つの仮想ネットワークをピアリングする場合、各仮想ネットワークに対して次のロールまたはアクセス許可を持っている必要があります。
    
|仮想ネットワーク|デプロイメント モデル|Role|アクセス許可|
|---|---|---|---|
|Hub-RM|リソース マネージャー|[Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |クラシック|[Classic Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|該当なし|
|Spoke-Classic|リソース マネージャー|[Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||クラシック|[従来のネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)と、特定のアクセス許可を[カスタム ロール](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法 (Resource Manager のみ) の詳細を参照してください。

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Resource Manager から Resource Manager へのゲートウェイ転送によるピアリング

手順に従って、ゲートウェイ転送が有効な仮想ネットワーク ピアリングを作成するか、そのように更新します。

1. Azure Portal で Spoke-RM から Hub-RM への仮想ネットワーク ピアリングを作成するか、そのように更新します。 Spoke-RM 仮想ネットワーク リソースに移動し、[ピアリング]、[追加] の順にクリックします。
    - [Resource Manager] オプションを設定します。
    - 該当するサブスクリプションの [Hub-RM] 仮想ネットワークを選択します
    - [仮想ネットワーク アクセスを許可する] が [有効] になっていることを確認します。
    - **[リモート ゲートウェイを使用する]** オプションを設定します。
    - [OK] をクリックします。

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. ピアリングが既に作成されている場合は、ピアリング リソースに移動し、手順 (1) で示したスクリーン ショットのように **[リモート ゲートウェイを使用する]** オプションを有効にします。

3. Azure Portal で Hub-RM から Spoke-RM への仮想ネットワーク ピアリングを作成するか、そのように更新します。 Hub-RM 仮想ネットワーク リソースに移動し、[ピア リング]、[追加]の順にクリックします。
    - [Resource Manager] オプションを設定します。
    - [仮想ネットワーク アクセスを許可する] が [有効] になっていることを確認します。
    - 該当するサブスクリプションの [Spoke-RM] 仮想ネットワークを選択します
    - **[ゲートウェイ転送を許可する]** オプションを設定します。
    - [OK] をクリックします。

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. ピアリングが既に作成されている場合は、ピアリング リソースに移動し、手順 (3) で示したスクリーン ショットのように **[ゲートウェイ転送を許可する]** オプションを有効にします。

5. 両方の仮想ネットワークで、ピアリングの状態が **[接続済み]** になっていることを確認します。

### <a name="powershell-sample"></a>PowerShell のサンプル

上記の例のピアリングの作成または更新は、PowerShell を使用して行うこともできます。 変数を仮想ネットワークとリソース グループの名前に置き換えます。

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>クラシックから Resource Manager へのゲートウェイ転送によるピアリング

操作が Hub-RM 仮想ネットワークのみに適用されることを除いて、手順は Resource Manager の例に似ています。

1. Azure Portal で Hub-RM から Spoke-RM への仮想ネットワーク ピアリングを作成するか、そのように更新します。 Hub-RM 仮想ネットワーク リソースに移動し、[ピア リング]、[追加]の順にクリックします。
   - 仮想ネットワークのデプロイ モデルとして [クラシック] オプションを設定します。
   - 対応するサブスクリプションの [Spoke-RM] 仮想ネットワークを選択します
   - [仮想ネットワーク アクセスを許可する] が [有効] になっていることを確認します。
   - **[ゲートウェイ転送を許可する]** オプションを設定します。
   - [OK] をクリックします。

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. ピアリングが既に作成されている場合は、ピアリング リソースに移動し、手順 (1) で示したスクリーン ショットのように **[ゲートウェイ転送を許可する]** オプションを有効にします。

3. Spoke-Classic 仮想ネットワーク上での操作はありません。

4. Hub-RM 仮想ネットワークで、ピアリングの状態が **[接続済み]** になっていることを確認します。

状態が [接続済み] と表示されたら、スポーク仮想ネットワークは、ハブ仮想ネットワーク内の VPN ゲートウェイを経由する VNet 間接続またはクロスプレミス接続の使用を開始できます。

### <a name="powershell-sample"></a>PowerShell のサンプル

上記の例のピアリングの作成または更新は、PowerShell を使用して行うこともできます。 変数とサブスクリプション ID を、仮想ネットワークとリソース グループの値とサブスクリプションに置き換えます。 仮想ネットワーク ピアリングの作成は、ハブ仮想ネットワークでのみ行う必要があります。

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>次の手順

* 運用環境で使用する仮想ネットワーク ピアリングを作成する前に、[仮想ネットワーク ピアリングの制約と動作](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)と[仮想ネットワーク ピアリングの設定](../virtual-network/virtual-network-manage-peering.md#create-a-peering)を確認します。
* 仮想ネットワーク ピアリングとゲートウェイ転送を使用する[ハブとスポークのネットワーク トポロジを作成](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)する方法を確認します。
