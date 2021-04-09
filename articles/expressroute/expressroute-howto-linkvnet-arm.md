---
title: チュートリアル:VNet を ExpressRoute 回線にリンクする - Azure PowerShell
description: このチュートリアルでは、Resource Manager デプロイ モデルと Azure PowerShell を使用して ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 69067ca34b231f1b14f8cc854288c3ed4c4ac82a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91855992"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>チュートリアル:ExpressRoute 回線に仮想ネットワークを接続する
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-linkvnet-classic.md)
>

この記事では、Resource Manager デプロイ モデルと PowerShell を使用して Azure ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法について説明します。 仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。 この記事では、仮想ネットワークのリンクを更新する方法も示します。

* 最大 10 個の仮想ネットワークを標準 ExpressRoute 回線に接続できます。 標準 ExpressRoute 回線を使用する場合は、すべての仮想ネットワークが同じ地理的リージョンに存在する必要があります。 

* 単一の VNet を最大 4 つの ExpressRoute 回線にリンクできます。 この記事の手順を使用して、接続先の各 ExpressRoute 回線の新しい接続オブジェクトを作成します。 ExpressRoute 回線は、同じサブスクリプション、異なるサブスクリプション、または両方の組み合わせにすることができます。

* ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線の地理的リージョンの外部にある仮想ネットワークをリンクすることができます。 Premium アドオンを使用すると、選択した帯域幅に応じて、10 を超える仮想ネットワークを ExpressRoute 回線に接続することもできます。 Premium アドオンの詳細については、 [FAQ](expressroute-faqs.md) を確認してください。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - 同じサブスクリプション内の仮想ネットワークを回線に接続する
> - 別のサブスクリプション内の仮想ネットワークを回線に接続する
> - 仮想ネットワーク接続を変更する
> - ExpressRoute FastPath を構成する

## <a name="prerequisites"></a>前提条件

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、[ワークフロー](expressroute-workflows.md)を確認します。

* アクティブな ExpressRoute 回線が必要です。 
  * 手順に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md) し、接続プロバイダー経由で回線を有効にしてください。 
  * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ルーティング手順については、 [ルーティングの構成](expressroute-howto-routing-arm.md) に関する記事を参照してください。 
  * エンドツーエンド接続のために、確実に Azure プライベート ピアリングが構成され、ご使用のネットワークと Microsoft の間の BGP ピアリングを確立します。
  * 仮想ネットワークと仮想ネットワーク ゲートウェイを作成し、完全にプロビジョニングします。 指示に従って [ExpressRoute 用の仮想ネットワーク ゲートウェイを作成](expressroute-howto-add-gateway-resource-manager.md)します。 ExpressRoute 用の仮想ネットワーク ゲートウェイは、VPN ではなく GatewayType 'ExpressRoute' を使用します。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>同じサブスクリプション内の仮想ネットワークを回線に接続する
次のコマンドレットを使用し、ExpressRoute 回線に仮想ネットワーク ゲートウェイを接続できます。 コマンドレットを実行する前に、仮想ネットワーク ゲートウェイが作成されており、リンクの準備ができていることを確認してください。

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>別のサブスクリプション内の仮想ネットワークを回線に接続する
複数のサブスクリプションで ExpressRoute 回線を共有できます。 下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。 組織内の各部門は、サービスのデプロイを目的として、固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。 1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。 組織内の他のサブスクリプションを使用する場合も、ExpressRoute 回線を使用できます。

> [!NOTE]
> ExpressRoute 回線の接続と帯域幅の料金は、サブスクリプションの所有者が負担することになります。 すべての仮想ネットワークが同じ帯域幅を共有します。
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="サブスクリプション間接続":::

### <a name="administration---circuit-owners-and-circuit-users"></a>管理 - 回線所有者と回線ユーザー

"回線所有者" は、ExpressRoute 回線リソースの権限のあるパワー ユーザーです。 回線所有者は、"回線ユーザー" が利用できる承認を作成できます。 回線ユーザーは、ExpressRoute 回線と同じサブスクリプション内には存在しない仮想ネットワーク ゲートウェイの所有者です。 回線ユーザーは、承認を利用できます (仮想ネットワークごとに 1 つの承認)。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。 承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンク接続が削除されます。

### <a name="circuit-owner-operations"></a>回線所有者の操作

**承認を作成するには**

回線所有者が承認を作成すると、承認キーが作成されます。回線ユーザーは、その承認キーを使用して、各自の仮想ネットワーク ゲートウェイを ExpressRoute 回線に接続します。 承認は、1 つの接続に対してのみ有効です。

次のコマンドレット スニペットは、承認を作成する方法を示しています。

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

前のコマンドに対する応答には、承認キーと状態が含まれます。

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**承認を確認するには**

回線所有者は、次のコマンドレットを実行し、特定の回線で発行されるすべての承認を確認できます。

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**承認を追加するには**

回線所有者は、次のコマンドレットを使用して承認を追加できます。

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**承認を削除するには**

回線所有者は、次のコマンドレットを実行して、ユーザーに対する承認を取り消したり削除したりすることができます。

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>回線ユーザーの操作

回線ユーザーは、ピア ID と回線所有者が作成した承認キーを必要とします。 承認キーは GUID です。

ピア ID は、次のコマンドから確認できます。

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**接続の承認を利用するには**

回線ユーザーは次のコマンドレットを実行し、リンク承認を利用できます。

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**接続の承認を解除するには**

ExpressRoute 回線を仮想ネットワークにリンクしている接続を削除することで、承認を解除できます。

## <a name="modify-a-virtual-network-connection"></a>仮想ネットワーク接続を変更する
仮想ネットワーク接続の特定のプロパティを更新することができます。 

**接続の重みを更新するには**

仮想ネットワークは、複数の ExpressRoute 回線に接続できます。 複数の ExpressRoute 回線から同じプレフィックスを受け取る場合があります。 このプレフィックスを宛先とするトラフィックをどの接続が送信するかを選択するには、接続の *RoutingWeight* を変更します。 *RoutingWeight* が最も高い接続でトラフィックが送信されます。

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

*RoutingWeight* の範囲は 0 ～ 32000 です。 既定値は 0 です。

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath を構成する 
仮想ネットワーク ゲートウェイが Ultra Performance または ErGw3AZ である場合は、[ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) を有効にすることができます。 FastPath により、オンプレミス ネットワークと仮想ネットワークの間の 1 秒あたりのパケット数や 1 秒あたりの接続数などのデータ パスのパフォーマンスが向上します。 

**新しい接続で FastPath を構成する**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**既存の接続を更新して FastPath を有効にする**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ExpressRoute 接続が不要になった場合は、ゲートウェイが配置されているサブスクリプションから、`Remove-AzVirtualNetworkGatewayConnection` コマンドを使用して、ゲートウェイと回線の間のリンクを削除します。

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>次のステップ
ExpressRoute の詳細については、「 ExpressRoute のFAQ」をご覧ください。

> [!div class="nextstepaction"]
> [ExpressRoute の FAQ](expressroute-faqs.md)
