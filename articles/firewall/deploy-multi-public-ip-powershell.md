---
title: PowerShell を使用して複数のパブリック IP アドレスを使用する Azure Firewall をデプロイする
description: この記事では、Azure PowerShell を使用して、複数のパブリック IP アドレスを使用する Azure Firewall をデプロイする方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: cbad025a0d0c4d679ea9cdc7557c81b5145798fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023671"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure PowerShell を使用して複数のパブリック IP アドレスを使用する Azure Firewall をデプロイする

この機能により、次のシナリオが可能になります。

- **DNAT** - 複数の標準ポート インスタンスをバックエンド サーバーに変換できます。 たとえば、2 つのパブリック IP アドレスがある場合、両方の IP アドレス用の TCP ポート 3389 (RDP) を変換できます。
- **SNAT** -送信 SNAT 接続に追加のポートを使用できるので、SNAT ポートが不足する可能性が低減されます。 現時点では、Azure Firewall は、接続に使用する送信元パブリック IP アドレスをランダムに選択します。 ネットワークにダウンストリーム フィルターがある場合、ファイアウォールに関連付けられているすべてのパブリック IP アドレスを許可する必要があります。 この構成を簡略化するには、[パブリック IP アドレス プレフィックス](../virtual-network/public-ip-address-prefix.md)を使用することを検討してください。
 
Azure portal、Azure PowerShell、Azure CLI、REST、テンプレートで、複数のパブリック IP アドレスを持つ Azure Firewall を使用できます。 最大 250 個のパブリック IP アドレスを使用する Azure Firewall をデプロイできます。

以下の Azure PowerShell の例は、Azure Firewall のパブリック IP アドレスを構成、追加、および削除する方法を示しています。

> [!NOTE]
> Azure Firewall のパブリック IP アドレス構成ページから最初の ipConfiguration を削除することはできません。 IP アドレスを変更する場合は、Azure PowerShell を使用できます。

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>2 つ以上のパブリック IP アドレスを使用するファイアウォールを作成する

この例では、2 つのパブリック IP アドレスで仮想ネットワーク *vnet* に接続されたファイアウォールを作成します。

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>既存のファイアウォールにパブリック IP アドレスを追加する

この例では、パブリック IP アドレス *azFwPublicIp1* がファイアウォールにアタッチされます。

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>既存のファイアウォールからパブリック IP アドレスを削除する

この例では、パブリック IP アドレス *azFwPublicIp1* がファイアウォールからデタッチされます。

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>次のステップ

* [クイック スタート: 複数のパブリック IP アドレスを使用した Azure Firewall の作成 - Resource Manager テンプレート](quick-create-multiple-ip-template.md)
