---
title: Azure PowerShell を使用して複数のパブリック IP アドレスを使用する Azure Firewall をデプロイする
description: 複数のパブリック IP アドレスを持つファイアウォールをデプロイして仮想ハブを保護します
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 652c7cbfbe63ef2ae9a0d54e05407152ea300f1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87006995"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>複数のパブリック IP アドレスを持つ Azure Firewall をデプロイする

Azure Firewall を使用して仮想ハブを保護する場合は、Azure PowerShell を使用して、複数のパブリック IP アドレスを持つファイアウォールをデプロイできます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>ファイアウォールをデプロイする

複数のパブリック IP アドレスを持つ Azure Firewall をデプロイして仮想ハブを保護するには、次の Azure PowerShell の例を使用します。

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>パブリック IP アドレスを更新する

Azure Firewall のパブリック IP アドレスは、Azure PowerShell を使用して更新できます。 次の例では、1 つのパブリック IP アドレスをファイアウォールから削除します。 3 つのパブリック IP アドレスで開始されています。

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>次のステップ

[セキュリティ保護付き仮想ハブとは](secured-virtual-hub.md)