---
title: Azure PowerShell を使用して負荷分散規則とアウトバウンド規則を構成する
titlesuffix: Azure Load Balancer
description: この記事では、Azure PowerShell を使用して Standard Load Balancer の負荷分散規則とアウトバウンド規則を構成する方法について説明します。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b621d56a76a51f9ce9df10f88f5c3f2f4babfc03
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816025"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Azure PowerShell を使用して Standard Load Balancer の負荷分散規則とアウトバウンド規則を構成する

この記事では、Azure PowerShell を使用して Standard Load Balancer のアウトバウンド規則を構成する方法について説明します。  

この記事のシナリオを完了すると、ロード バランサーのリソースに 2 つのフロントエンドとそれに関連付けられた規則が追加されます。 1 つのフロントエンドは受信トラフィック用、もう 1 つは送信トラフィック用です。  

各フロントエンドからはパブリック IP アドレスが参照されます。 このシナリオでは、受信トラフィックのパブリック IP アドレスが送信トラフィックのアドレスと異なります。   負荷分散規則によって、受信の負荷分散のみが提供されます。 アウトバウンド規則によって、VM の送信ネットワーク アドレス変換 (NAT) が制御されます。  

このシナリオでは 2 つのバックエンド プールを使用します。1 つは受信トラフィック用、もう 1 つは送信トラフィック用です。 これらのプールで機能を表し、シナリオに柔軟性を提供します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Azure アカウントに接続する
[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) コマンドを使用して Azure サブスクリプションにサインインします。 その後、画面の指示に従います。
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) を使用してリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイに使用する論理コンテナーです。 リソースは、グループから管理されます。

次の例では、*myresourcegroupoutbound* という名前のリソース グループを *eastus2* に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>仮想ネットワークの作成
*myvnetoutbound* という名前の仮想ネットワークを作成します。 そのサブネットに *mysubnetoutbound* という名前を付けます。 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) と [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0) を使用して、*myresourcegroupoutbound* に配置します。

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>受信パブリック IP アドレスを作成する 

インターネット上の Web アプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 Standard Load Balancer でサポートされるのは Standard パブリック IP アドレスだけです。 

[New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) を使用して、*myresourcegroupoutbound* に *mypublicipinbound* という名前の Standard パブリック IP アドレスを作成します。

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>送信パブリック IP アドレスを作成する 

[New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) を使用して、ロード バランサーのフロントエンド送信構成の Standard IP アドレスを作成します。

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Azure Load Balancer を作成する

このセクションでは、ロード バランサーの次のコンポーネントを作成および構成する方法について説明します。
  - ロード バランサーの受信ネットワーク トラフィックを受け取るフロントエンド IP
  - フロントエンド IP から負荷分散されたネットワーク トラフィックを送信する先のバックエンド プール。
  - 送信接続用のバックエンド プール
  - バックエンド VM インスタンスの正常性を判断する正常性プローブ
  - VM へのトラフィックの分散方法を定義する、ロード バランサーのインバウンド規則
  - VM からのトラフィックの分散方法を定義する、ロード バランサーのアウトバウンド規則

### <a name="create-an-inbound-front-end-ip"></a>受信フロントエンド IP を作成する
[New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) を使用して、ロード バランサーの受信フロントエンド IP 構成を作成します。 ロード バランサーには、*myfrontendinbound* という名前の受信フロントエンド IP 構成を含める必要があります。 この構成をパブリック IP アドレス *mypublicipinbound* に関連付けます。

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>送信フロントエンド IP を作成する
[New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) を使用して、ロード バランサーの送信フロントエンド IP 構成を作成します。 このロード バランサーには、*myfrontendoutbound* という名前の送信フロントエンド IP 構成を含める必要があります。 この構成をパブリック IP アドレス *mypublicipoutbound* に関連付けます。

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>受信バックエンド プールを作成する
[New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) を使用して、ロード バランサーのバックエンド受信プールを作成します。 プールに *bepoolinbound* という名前を付けます。

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>送信バックエンド プールを作成する
次のコマンドを使用して別のバックエンド アドレス プールを作成し、[New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) を使用して VM のプールの送信接続を定義します。 このプールに *bepooloutbound* と名前を付けます。 

個別の送信プールを作成することで、柔軟性を最大限に高めることができます。 ただし、この手順を省略し、必要に応じて受信 *bepoolinbound* のみを使用することもできます。  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>正常性プローブの作成

正常性プローブでは、すべての VM インスタンスを調べて、ネットワーク トラフィックを送信できることを確認します。 プローブ チェックに失敗した VM インスタンスは、オンラインに戻り、プローブ チェックによって正常であると判断されるまで、ロード バランサーから削除されます。 

VM の正常性を監視するには、[New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) を使用して正常性プローブを作成します。 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則では、受信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するバックエンド プールを定義します。 また、必要なソースと宛先のポートも定義します。 

[New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) を使用して *myinboundlbrule* という名前のロード バランサー規則を作成します。 この規則では、フロントエンド プール *myfrontendinbound* でポート 80 をリッスンします。 また、ポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *bepoolinbound* に送信します。 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>この負荷分散規則では、 **-DisableOutboundSNAT** パラメーターがあるので、自動送信セキュア NAT (SNAT) が無効になります。 送信 NAT は、アウトバウンド規則でのみ提供されます。

### <a name="create-an-outbound-rule"></a>アウトバウンド規則を作成する

アウトバウンド規則では、フロントエンドのパブリック IP を定義します。これは、フロントエンド *myfrontendoutbound* で表されます。 このフロントエンドは、規則が適用されるバックエンド プールだけでなく、すべての送信 NAT トラフィックに使用されます。  

次のコマンドを使用して、*bepool* バックエンド プール内のすべての VM (NIC IP 構成内) の送信ネットワーク変換用に、アウトバウンド規則 *myoutboundrule* を作成します。  このコマンドで、送信のアイドルのタイムアウトを 4 から 15 分に変更します。 1,024 個ではなく 10,000 個の SNAT ポートが割り当てられます。 詳細については、「[New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)」を参照してください。

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
送信用に別のプールを使用したくない場合は、前のコマンドのアドレス プール引数を変更して、代わりに *$bepoolin* を指定してもかまいません。  結果の構成を柔軟で読みやすくするために、個別のプールを使用することをお勧めします。

### <a name="create-a-load-balancer"></a>ロード バランサーの作成

次のコマンドを使用し、[New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) を使用して受信 IP アドレスのロード バランサーを作成します。 ロード バランサーに *lb* という名前を付けます。これには受信フロントエンド IP 構成を含める必要があります。 そのバックエンド プール *bepoolinbound* は、前の手順で作成したパブリック IP アドレス *mypublicipinbound* に関連付ける必要があります。

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

この時点で、それぞれの NIC リソースの IP 構成を更新することにより、*bepoolinbound* および *bepooloutbound* バックエンド プールの両方に引き続き VM を追加できます。 [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) を使用してリソース構成を更新します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ロード バランサー、および関連リソースが不要になったら、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) を使用してそれらを削除できます。

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>次の手順
この記事では、標準のロード バランサーを作成し、受信と送信の両方のロード バランサー トラフィック規則を構成し、バックエンド プールの VM の正常性プローブを構成しました。 

さらに学習するには、[Azure Load Balancer に関するチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)に進みます。
