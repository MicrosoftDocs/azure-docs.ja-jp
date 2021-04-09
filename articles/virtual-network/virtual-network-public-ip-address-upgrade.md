---
title: パブリック IP アドレスのアップグレード
titleSuffix: Azure Virtual Network
description: パブリック IP アドレスを Basic から Standard にアップグレードします。
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 33c767d847d9e70e95b3ee1648be7852aa5cec98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100522888"
---
# <a name="upgrade-public-ip-addresses"></a>パブリック IP アドレスのアップグレード

Azure パブリック IP アドレスは、SKU (Basic または Standard) を使用して作成され、SKU によって機能の側面 (割り当て方法、機能のサポート、関連付けることができるリソースを含む) が決まります。 

この記事では、次のシナリオを確認します。
* Basic SKU パブリック IP を Standard SKU パブリック IP にアップグレードする方法 (PowerShell または CLI を使用)
* 従来の Azure 予約済み IP を Azure Resource Manager の Basic SKU パブリック IP に移行する方法

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>パブリック IP アドレスを Basic SKU から Standard SKU にアップグレードする

パブリック IP をアップグレードする場合、その IP をリソースに関連付けることはできません (パブリック IP の関連付けを解除する方法の詳細については、[このページ](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)をご覧ください)。

>[!IMPORTANT]
>Basic から Standard SKU にアップグレードされたパブリック IP には引き続き、[可用性ゾーン](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)の保証はありません。  IP アドレスを関連付けるリソースを選択するときは、この点に留意してください。

---
# <a name="basic-to-standard---powershell"></a>[**Basic から Standard へ - PowerShell**](#tab/option-upgrade-powershell)

次の例では、[このページ](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic)で示した例 (**myResourceGroup** 内の Basic パブリック IP **myBasicPublicIP**) を使用して、Basic SKU パブリック IP が既に作成されていると仮定します。

IP をアップグレードするには、単純に PowerShell を使用して以下のコマンドを実行します。  IP アドレスが既に静的に割り当てられている場合、そのセクションはスキップできます。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Basic から Standard へ - CLI**](#tab/option-upgrade-cli)

次の例では、[このページ](./create-public-ip-cli.md?tabs=option-create-public-ip-basic)で示した例 (**myResourceGroup** 内の Basic パブリック IP **myBasicPublicIP**) を使用して、Basic SKU パブリック IP が既に作成されていると仮定します。

IP をアップグレードするには、単純に Azure CLI を使用して以下のコマンドを実行します。  IP アドレスが既に静的に割り当てられている場合、そのセクションはスキップできます。

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>従来の予約済み IP を静的パブリック IP にアップグレード (移行) する

Azure Resource Manager の新機能を活用するには、既存の静的パブリック IP アドレス (予約済み IP と呼ばれる) を従来のモデルから最新 Azure Resource Manager モデルに移行します。  移行されたパブリック IP は、SKU の種類が Basic になります。


---

# <a name="reserved-to-basic---powershell"></a>[**予約済みから Basic へ - PowerShell**](#tab/option-migrate-powershell)

次の例では、**myResourceGroup** 内の従来の Azure 予約済み IP **myReservedIP** が既に作成されていると仮定します。 移行のもう 1 つの前提条件は、Azure Resource Manager サブスクリプションが移行用に登録されていることを確認することです。 これについては、[このページ](../virtual-machines/migration-classic-resource-manager-ps.md)の手順 3 および 4 で詳しく説明します。

予約済み IP を移行するには、PowerShell を使用して以下のコマンドを実行します。  IP アドレスがどのサービスにも関連付けられていない場合 (下の例では **myService** というサービスがあります)、この手順は省略できます。

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
前のコマンドを実行すると、移行をブロックする警告とエラーが表示されます。 検証に成功した場合は、移行を準備してコミットするために次の手順に進むことができます。
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
移行された予約済み IP の名前を使用して Azure Resource Manager に新しいリソース グループ (上の例では、リソース グループ **myReservedIP-Migrated**) が作成されます。

# <a name="reserved-to-basic---cli"></a>[**予約済みから Basic へ - CLI**](#tab/option-migrate-cli)

次の例では、**myResourceGroup** 内の従来の Azure 予約済み IP **myReservedIP** が既に作成されていると仮定します。 移行のもう 1 つの前提条件は、Azure Resource Manager サブスクリプションが移行用に登録されていることを確認することです。 これについては、[このページ](../virtual-machines/migration-classic-resource-manager-cli.md)の手順 3 および 4 で詳しく説明します。

予約済み IP を移行するには、Azure CLI を使用して以下のコマンドを実行します。  IP アドレスがどのサービスにも関連付けられていない場合 (下の例では **myService** というサービスとデプロイ **myDeployment** があります)、この手順は省略できます。

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
前のコマンドを実行すると、移行をブロックする警告とエラーが表示されます。 検証に成功した場合は、移行を準備してコミットするために次の手順に進むことができます。
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
移行された予約済み IP の名前を使用して Azure Resource Manager に新しいリソース グループ (上の例では、リソース グループ **myReservedIP-Migrated**) が作成されます。

---

## <a name="limitations"></a>制限事項

* Basic パブリック IP をアップグレードする場合、その IP を Azure リソースに関連付けることはできません。  パブリック IP の関連付けを解除する方法の詳細については、[このページ](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)をご覧ください。  同様に、予約済み IP を移行する場合、その IP をクラウド サービスに関連付けることはできません。  予約済み IP の関連付けを解除する方法の詳細については、[このページ](./remove-public-ip-address-vm.md)をご覧ください。  
* Basic SKU から Standard SKU にアップグレードされたパブリック IP は、引き続き[可用性ゾーン](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)を使用できないため、ゾーン冗長またはゾーン ベースの Azure リソースに関連付けることはできません。  これは、可用性ゾーンを提供するリージョンにのみ適用されます。
* Standard から Basic にダウングレードすることはできません。

## <a name="next-steps"></a>次の手順

- Azure での[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)の詳細 (SKU の種類の違いと[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)を含む) を確認する
- [Azure Public Load Balancer を Basic SKU から Standard SKU にアップグレードする](../load-balancer/upgrade-basic-standard.md)方法を確認する
- [従来の Azure 予約済み IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) と[従来のリソースの Azure Resource Manager への移行](../virtual-machines/migration-classic-resource-manager-overview.md)を理解する
