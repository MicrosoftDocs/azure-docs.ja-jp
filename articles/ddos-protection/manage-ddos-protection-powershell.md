---
title: Azure PowerShell を使用した Azure DDoS Protection プランの作成と構成
description: Azure PowerShell を使用して DDoS Protection プランを作成する方法について説明します
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 69f9b5a74566879ecf8f15f23e689ebb731da45a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97814144"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>クイックスタート: Azure PowerShell を使用した Azure DDoS Protection Standard の作成と構成

Azure PowerShell を使用して Azure DDoS Protection Standard の使用を開始します。 

DDoS Protection プランでは、サブスクリプションの境界を越えて、DDoS Protection Standard が有効になった仮想ネットワークのセットを定義します。 組織で 1 つの DDoS Protection プランを構成し、複数のサブスクリプションから同じプランに仮想ネットワークをリンクできます。 

このクイックスタートでは、DDoS 保護プランを作成し、それを仮想ネットワークにリンクします。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>DDoS Protection プランを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。

リソース グループを作成するには、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用します。 この例では、リソース グループに _MyResourceGroup_ という名前を付け、"_米国東部_" の場所を使用します。

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

次に、_MyDdosProtectionPlan_ という名前の DDoS 保護プランを作成します。

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>新しい仮想ネットワークに対して DDoS を有効にする

### <a name="enable-ddos-for-a-new-virtual-network"></a>新しい仮想ネットワークに対して DDoS を有効にする

仮想ネットワークを作成するときに、DDoS 保護を有効にすることができます。 この例では、仮想ネットワークに _MyVnet_ という名前を付けます。 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>既存の仮想ネットワークに対して DDoS を有効にする

DDoS 保護プランを作成するときに、既存の仮想ネットワークを関連付けることができます。

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>検証とテスト

まず、DDoS 保護プランの詳細を確認します。

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

コマンドで DDoS 保護プランの正しい詳細が返されることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルのためにリソースを保持しておくことができます。 不要になったら、_MyResourceGroup_ リソース グループを削除します。 リソース グループを削除する際に、DDoS 保護プランとその関連リソースもすべて削除します。 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

仮想ネットワークに対して DDoS 保護を無効にするには: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

DDoS 保護プランを削除する場合は、最初にそのプランからすべての仮想ネットワークの関連付けを解除する必要があります。

## <a name="next-steps"></a>次のステップ

DDoS 保護プラン用にテレメトリを表示および構成する方法を学習するには、チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DDoS 保護テレメトリの表示と構成](telemetry.md)