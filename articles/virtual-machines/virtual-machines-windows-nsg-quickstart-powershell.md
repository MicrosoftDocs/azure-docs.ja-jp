---
title: "Azure PowerShell を使用した VM へのポートの開放 | Microsoft Docs"
description: "Azure Resource Manager デプロイメント モデルと Azure PowerShell を使用して、Windows VM へのポートを開き、エンドポイントを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 91c2e6f53f8ba490d2943a60c7ad519d856707b9
ms.openlocfilehash: 0d57a27c0862b09e2628520a1e50a0cc56a55881
ms.lasthandoff: 02/10/2017


---
# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>PowerShell を使用した Azure の VM へのポートとエンドポイントの開放
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>クイック コマンド
ネットワーク セキュリティ グループと ACL 規則を作成するには、 [最新バージョンの Azure PowerShell がインストールされている](/powershell/azureps-cmdlets-docs)必要があります。 [これらの手順は、Azure Portal を使用して実行](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)することもできます。

Azure アカウントにログインします。

```powershell
Login-AzureRmAccount
```

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`myNetworkSecurityGroup`、および `myVnet` が含まれています。

規則を作成します。 次の例では、`myNetworkSecurityGroupRule` という名前の規則を作成して、ポート 80 での TCP トラフィックを許可します。

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

そして、次のように、ネットワーク セキュリティ グループを作成し、先ほど作成した HTTP 規則を割り当てます。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

ここで、ネットワーク セキュリティ グループをサブネットに割り当ててみましょう。 次の例では、`myVnet` という名前の既存の仮想ネットワークを `$vnet` 変数に割り当てます。

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

ネットワーク セキュリティ グループとサブネットを関連付けます。 次の例では、`mySubnet` という名前のサブネットをネットワーク セキュリティ グループに関連付けます。

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

最後に、仮想ネットワークを更新して、変更を有効にします。

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](../virtual-network/virtual-networks-create-nsg-arm-ps.md)に関するページをご覧ください。

ネットワーク セキュリティ グループと ACL 規則は、Azure Resource Manager のテンプレートの一部として定義できます。 詳細については、 [テンプレートを使用したネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-template.md)に関するページをご覧ください。

ポート フォワーディングを使用して、一意の外部ポートを VM 上の内部ポートにマップする必要がある場合は、ロード バランサーとネットワーク アドレス変換 (NAT) 規則を使用します。 たとえば、TCP ポート 8080 を外部に公開し、VM 上の TCP ポート 80 にトラフィックを送ることができます。 詳細については、 [インターネットに接続するロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-arm-ps.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)
* [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)
* [ロード バランサー用の Azure Resource Manager の概要](../load-balancer/load-balancer-arm.md)


