---
title: 'Azure ExpressRoute: Global Reach を構成する'
description: この記事では、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築し、Global Reach を有効にする方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.openlocfilehash: 2365317d83a4c11fa17cb5c449a25b70da17c2eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368368"
---
# <a name="configure-expressroute-global-reach"></a>ExpressRoute Global Reach を構成する

この記事は、PowerShell を使用して ExpressRoute Global Reach を構成する際に役立ちます。 詳細については、[ExpressRouteRoute Global Reach](expressroute-global-reach.md) に関するページを参照してください。

 ## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、次のことを確認してください。

* ExpressRoute 回線のプロビジョニング [ワークフロー](expressroute-workflows.md)を理解していること。
* ExpressRoute 回線がプロビジョニングされた状態にあること。
* ExpressRoute 回線上に Azure プライベート ピアリングが構成されていること。
* PowerShell をローカルで実行する場合は、Azure PowerShell の最新バージョンがコンピューターにインストールされていることを確認します。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>回線を特定する

1. 構成を開始するには、Azure アカウントにサインインし、使用するサブスクリプションを選択します。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. 使用する ExpressRoute 回線を特定します。 任意の 2 つの ExpressRoute 回線のプライベート ピアリング間で ExpressRoute Global Reach を有効にすることができるのは、それらがサポートされている国や地域に配置され、かつ異なるピアリングの場所で作成されている場合だけです。 

   * サブスクリプションが両方の回線を所有している場合は、以降のセクションで構成を実行するために、どちらかの回線を選択できます。
   * 2 つの回線が異なる Azure サブスクリプションに含まれている場合は、1 つの Azure サブスクリプションからの承認が必要です。 その後、もう一方の Azure サブスクリプションで構成コマンドを実行するときに承認キーを渡します。

## <a name="enable-connectivity"></a>接続性の確保

オンプレミス ネットワーク間の接続を有効にします。 同じ Azure サブスクリプション内にある回線と、異なるサブスクリプションである回線には、異なるセットの手順があります。

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>同じ Azure サブスクリプション内の ExpressRoute 回線

1. 次のコマンドを使用して回線 1 と回線 2 を取得します。 2 つの回線は同じサブスクリプションにあります。

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. 回線 1 に対して次のコマンドを実行し、回線 2 のプライベート ピアリング ID を渡します。 このコマンドを実行するときは、次のことに注意してください。

   * プライベート ピアリング ID は、次の例のようになります。 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* は、/29 IPv4 サブネット ("10.0.0.0/29" など) である必要があります。 このサブネット内の IP アドレスを使用して、2 つの ExpressRoute 回線間の接続を確立します。 このサブネット内のアドレスを Azure 仮想ネットワークやオンプレミス ネットワークでは使用しないでください。

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. 回線 1 上の構成を次のように保存します。

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

前の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行います。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>異なる Azure サブスクリプションの ExpressRoute 回線

2 つの回線が同じ Azure サブスクリプション内にない場合は、承認が必要です。 次の構成では、回線 2 のサブスクリプション内に承認が生成され、その承認キーが回線 1 に渡されます。

1. 承認キーを生成します。

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   回線 2 のプライベート ピアリング ID と承認キーを書きとめます。
2. 回線 1 に対して次のコマンドを実行します。 回線 2 のプライベート ピアリング ID と承認キーを渡します。

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. この構成は回線 1 に保存します。

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

前の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行います。

## <a name="verify-the-configuration"></a>構成を確認する

構成が作成された回線 (たとえば、前の例では回線 1) 上の構成を確認するには、次のコマンドを使用します。
```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

単純に PowerShell で *$ckt_1* を実行すると、出力に *CircuitConnectionStatus* が表示されます。 これは、接続が確立されているかどうか、つまり "Connected" または "Disconnected" を通知します。 

## <a name="disable-connectivity"></a>接続の無効化

オンプレミス ネットワーク間での接続を無効にするには、構成が作成された回線 (たとえば、前の例では回線 1) に対してコマンドを実行します。

```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Get 操作を実行して状態を確認できます。

前の操作が完了すると、ExpressRoute 回線を経由したオンプレミス ネットワーク間の接続は存在しなくなります。

## <a name="next-steps"></a>次のステップ
1. [ExpressRoute Global Reach について詳しく学習する](expressroute-global-reach.md)
2. [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
3. [ExpressRoute 回線を Azure 仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)
