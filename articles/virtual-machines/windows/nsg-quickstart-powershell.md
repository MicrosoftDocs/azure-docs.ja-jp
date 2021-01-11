---
title: Azure PowerShell を使用した VM へのポートの開放
description: Azure Resource Manager デプロイメント モデルと Azure PowerShell を使用して、Windows VM へのポートを開き、エンドポイントを作成する方法について説明します。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0eca3f2fea3cf72b05b301ae307c1179f9bca0be
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072368"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>PowerShell を使用して Azure の VM へのポートとエンドポイントを開放する方法
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>クイック コマンド
ネットワーク セキュリティ グループと ACL 規則を作成するには、 [最新バージョンの Azure PowerShell がインストールされている](/powershell/azure/)必要があります。 [これらの手順は、Azure Portal を使用して実行](nsg-quickstart-portal.md)することもできます。

Azure アカウントにログインします。

```powershell
Connect-AzAccount
```

以下の例では、パラメーター名を独自の値に置き換えてください。 たとえば、*myResourceGroup*、*myNetworkSecurityGroup*、*myVnet* といったパラメーター名にします。

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して規則を作成します。 次の例では、*myNetworkSecurityGroupRule* という名前の規則を作成して、ポート *80* での *TCP* トラフィックを許可します。

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

そして、次のように、[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用してネットワーク セキュリティ グループを作成し、先ほど作成した HTTP 規則を割り当てます。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成します。

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

ここで、ネットワーク セキュリティ グループをサブネットに割り当ててみましょう。 次の例では、[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用して、*myVnet* という名前の既存の仮想ネットワークを *$vnet* 変数に割り当てます。

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) を使用して、ネットワーク セキュリティ グループをサブネットに関連付けます。 次の例では、*mySubnet* という名前のサブネットをネットワーク セキュリティ グループに関連付けます。

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

最後に、[Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) を使用して仮想ディレクトリを更新し、変更を有効にします。

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](tutorial-virtual-network.md#secure-network-traffic)に関するページをご覧ください。

高可用性 Web アプリケーション用に、Azure Load Balancer の背後に VM を配置する必要があります。 ロード バランサーは、トラフィックをフィルターできるネットワーク セキュリティ グループとともに、VM のトラフィックを分散します。 詳細については、[Azure の Linux 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法](tutorial-load-balancer.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../../azure-resource-manager/management/overview.md)
* [ネットワーク セキュリティ グループについて](../../virtual-network/security-overview.md)
* [Azure Load Balancer の概要](../../load-balancer/load-balancer-overview.md)
