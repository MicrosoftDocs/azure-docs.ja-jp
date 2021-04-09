---
title: 仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する
description: 仮想ネットワーク ピアリングのゲートウェイ転送を構成して、接続目的で 2 つの Azure 仮想ネットワークを 1 つにシームレスに接続します。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98872181"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する

この記事は、仮想ネットワーク ピアリングのゲートウェイ転送を構成するために役立ちます。 [仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)は、2 つの Azure 仮想ネットワークをシームレスに接続し、その 2 つの仮想ネットワークを接続目的で 1 つにマージします。 [ゲートウェイ転送](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)は、1 つの仮想ネットワークがクロスプレミス接続または VNet 間接続用にピアリングされた仮想ネットワーク内の VPN ゲートウェイを使用できるようにするピアリング プロパティです。 次の図は、仮想ネットワーク ピアリングでのゲートウェイ転送のしくみを示しています。

![ゲートウェイ転送の図](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

図では、ピアリングされた仮想ネットワークは、ゲートウェイ転送によって、Hub-RM 内の Azure VPN ゲートウェイを使用できます。 VPN ゲートウェイで使用可能な接続 (S2S、P2S、および VNet 間接続を含みます) は、3 つの仮想ネットワークのすべてに適用されます。 トランジット オプションは、同じデプロイ モデル間のピアリングでも異なるデプロイ モデル間のピアリングでも使用できます。 異なるデプロイ モデル間で転送を構成する場合、ハブ仮想ネットワークと仮想ネットワーク ゲートウェイは、クラシック デプロイ モデルではなく Resource Manager デプロイ モデルに配置する必要があります。
>

ハブ アンド スポーク ネットワーク アーキテクチャでは、スポーク 仮想ネットワークは、ゲートウェイ転送によって、ハブ内の VPN ゲートウェイを共有でき、VPN ゲートウェイをすべてのスポーク仮想ネットワークにデプロイする必要はありません。 ゲートウェイに接続された仮想ネットワークまたはオンプレミス ネットワークへのルートは、ピアリングされた仮想ネットワークのルーティング テーブルにゲートウェイ転送を使用して伝達されます。 VPN ゲートウェイからの自動ルート伝達は無効にできます。 ルーティング テーブルを **[BGP ルート伝達を無効にする]** オプションを指定して作成し、そのルーティング テーブルをサブネットに関連付けて、これらのサブネットへのルート配布を防ぎます。 詳細については、[仮想ネットワーク ルーティング テーブル](../virtual-network/manage-route-table.md)に関する記事を参照してください。

この記事では、次の 2 つのシナリオを扱います。

* **同じデプロイ モデル**:2 つの仮想ネットワークの両方が Resource Manager デプロイ モデルに作成されています。
* **異なるデプロイ モデル**:スポーク仮想ネットワークはクラシック デプロイ モデルに作成され、ハブ仮想ネットワークとゲートウェイは Resource Manager デプロイ モデルに作成されています。

>[!NOTE]
> Windows VPN クライアントがある状態でネットワークのトポロジに変更を加えた場合は、変更をそのクライアントに適用するために、Windows クライアント用の VPN クライアント パッケージをダウンロードしてもう一度インストールする必要があります。
>

## <a name="prerequisites"></a>前提条件

開始する前に、以下の仮想ネットワークとアクセス許可があることを確認してください。

### <a name="virtual-networks"></a><a name="vnet"></a>仮想ネットワーク

|VNet|デプロイ モデル| 仮想ネットワーク ゲートウェイ|
|---|---|---|---|
| Hub-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [はい](tutorial-create-gateway-portal.md)|
| Spoke-RM | [Resource Manager](./tutorial-site-to-site-portal.md)| いいえ |
| Spoke-Classic | [クラシック](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | いいえ |

### <a name="permissions"></a><a name="permissions"></a>アクセス許可

仮想ネットワーク ピアリングの作成に使用するアカウントは、必要なロールまたはアクセス許可を持っている必要があります。 次の例では、**Hub-RM** と **Spoke-Classic** という名前の 2 つの仮想ネットワークをピアリングする場合、各仮想ネットワークに対して次のロールまたはアクセス許可を持っている必要があります。

|VNet|デプロイメント モデル|Role|アクセス許可|
|---|---|---|---|
|Hub-RM|リソース マネージャー|[Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |クラシック|[Classic Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|該当なし|
|Spoke-Classic|リソース マネージャー|[Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||クラシック|[Classic Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)と、特定のアクセス許可を[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法 (Resource Manager のみ) の詳細を参照してください。

## <a name="same-deployment-model"></a><a name="same"></a>同じデプロイ モデル

このシナリオでは、2 つの仮想ネットワークの両方が Resource Manager デプロイ モデルに配置されています。 以下の手順に従って、ゲートウェイ転送が有効な仮想ネットワーク ピアリングを作成するか、そのように更新します。

### <a name="to-add-a-peering-and-enable-transit"></a>ピアリングを追加して転送を有効にするには

1. [Azure portal](https://portal.azure.com)で Hub-RM からの仮想ネットワーク ピアリングを作成するか、そのように更新します。 **Hub-RM** 仮想ネットワークに移動します。 **[ピアリング]** を選択してから、 **[+ 追加]** を選択して **[ピアリングの追加]** を開きます。
1. **[ピアリングの追加]** ページで、 **[この仮想ネットワーク]** の値を構成します。

   * ピアリング リンク名: リンクの名前を指定します。 例:**HubRMToSpokeRM**
   * リモート仮想ネットワークへのトラフィック: **許可**
   * リモート仮想ネットワークから転送されるトラフィック: **許可**
   * 仮想ネットワーク ゲートウェイ: **この仮想ネットワークのゲートウェイを使用する**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="このスクリーンショットは [ピアリングの追加] を示しています。":::

1. 同じページで、続けて **[リモート仮想ネットワーク]** の値を構成します。

   * ピアリング リンク名: リンクの名前を指定します。 例:**SpokeRMtoHubRM**
   * デプロイ モデル: **Resource Manager**
   * 仮想ネットワーク:**Spoke-RM**
   * リモート仮想ネットワークへのトラフィック: **許可**
   * リモート仮想ネットワークから転送されるトラフィック: **許可**
   * 仮想ネットワーク ゲートウェイ: **リモート仮想ネットワークのゲートウェイを使用する**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="スクリーンショットは、リモート仮想ネットワークの値を示しています。":::

1. **[追加]** を選択してピアリングを作成します。
1. 両方の仮想ネットワークで、ピアリングの状態が **[接続済み]** になっていることを確認します。

### <a name="to-modify-an-existing-peering-for-transit"></a>転送用に既存のピアリングを変更するには

ピアリングが既に作成されている場合は、転送用にピアリングを変更できます。

1. 仮想ネットワークに移動します。 **[ピアリング]** を選択し、変更するピアリングを選択します。

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="スクリーンショットは、ピアリングの選択を示しています。":::

1. VNet ピアリングを更新します。

   * リモート仮想ネットワークへのトラフィック: **許可**
   * リモート仮想ネットワークから転送されるトラフィック: **許可**
   * 仮想ネットワーク ゲートウェイ: **リモート仮想ネットワークのゲートウェイを使用する**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="スクリーンショットは、ピアリング ゲートウェイの変更を示しています。":::

1. ピアリングの設定を **保存** します。

### <a name="powershell-sample"></a><a name="ps-same"></a>PowerShell のサンプル

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

## <a name="different-deployment-models"></a><a name="different"></a>異なるデプロイ モデル

この構成では、スポーク VNet **Spoke-Classic** はクラシック デプロイ モデルにあり、ハブ VNet **Hub-RM** は Resource Manager デプロイ モデルにあります。 デプロイ モデル間の転送を構成する場合は、クラシック VNet ではなく、Resource Manager VNet 用に仮想ネットワーク ゲートウェイを構成する必要があります。

この構成では、**Hub-RM** 仮想ネットワークのみを構成する必要があります。 **Spoke-Classic** VNet では、何も構成する必要はありません。

1. Azure portal で、 **Hub-RM** 仮想ネットワークに移動し、 **[ピアリング]** を選択してからてから **[+ 追加]** を選択します。
1. **[ピアリングの追加]** ページで、以下の値を構成します。

   * ピアリング リンク名: リンクの名前を指定します。 例:**HubRMToClassic**
   * リモート仮想ネットワークへのトラフィック: **許可**
   * リモート仮想ネットワークから転送されるトラフィック: **許可**
   * 仮想ネットワーク ゲートウェイ: **この仮想ネットワークのゲートウェイを使用する**
   * リモート仮想ネットワーク: **クラシック**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Spoke-Classic 用の [ピアリングの追加] ページ":::

1. サブスクリプションが正しいことを確認し、ドロップダウンから仮想ネットワークを選択します。
1. **[追加]** を選択してピアリングを追加します。
1. Hub-RM 仮想ネットワークで、ピアリングの状態が **[接続済み]** になっていることを確認します。 

この構成では、**Spoke-Classic** 仮想ネットワークで何も構成する必要はありません。 状態が **[接続済み]** と表示されたら、スポーク仮想ネットワークは、ハブ仮想ネットワーク内の VPN ゲートウェイを経由する接続を使用します。

### <a name="powershell-sample"></a><a name="ps-different"></a>PowerShell のサンプル

上記の例のピアリングの作成または更新は、PowerShell を使用して行うこともできます。 変数とサブスクリプション ID を、仮想ネットワークとリソース グループの値とサブスクリプションに置き換えます。 仮想ネットワーク ピアリングの作成は、ハブ仮想ネットワークでのみ行う必要があります。

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>次のステップ

* 運用環境で使用する仮想ネットワーク ピアリングを作成する前に、[仮想ネットワーク ピアリングの制約と動作](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)と[仮想ネットワーク ピアリングの設定](../virtual-network/virtual-network-manage-peering.md#create-a-peering)を確認します。
* 仮想ネットワーク ピアリングとゲートウェイ転送を使用する[ハブとスポークのネットワーク トポロジを作成](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering)する方法を確認します。
* [デプロイ モデルが同じ仮想ネットワークのピアリングを作成します](../virtual-network/tutorial-connect-virtual-networks-portal.md)。
* [デプロイ モデルが異なる仮想ネットワークのピアリングを作成します](../virtual-network/create-peering-different-deployment-models.md)。