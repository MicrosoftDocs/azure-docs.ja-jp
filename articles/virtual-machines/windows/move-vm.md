---
title: Azure で Windows VM リソースを移動する
description: Resource Manager デプロイ モデルで Windows VM を他の Azure サブスクリプションまたはリソース グループに移動します。
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: c5d3445144178ce855a5ce904b42d6e68a8e7d29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555246"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>他の Azure サブスクリプションまたはリソース グループへの Windows VM の移動
この記事では、リソース グループまたはサブスクリプション間で Windows 仮想マシン (VM) を移動する方法について説明します。 サブスクリプション間での移動は、個人のサブスクリプションで作成した VM を、会社のサブスクリプションに移動して作業を続ける場合に便利です。 移動するために VM を停止する必要はありません。移動の間も、継続して実行される必要があります。

> [!IMPORTANT]
>移動の一環として新しいリソース ID が作成されます。 VM を移動したら、この新しいリソース ID を使用するために、ご使用のツールやスクリプトを更新する必要があります。
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>PowerShell を使用した VM の移動

仮想マシンを他のリソース グループに移動するには、依存リソースも必ずすべて移動する必要があります。 これらのリソースごとにリソース ID を含むリストを取得するには、[Get-AzResource](/powershell/module/az.resources/get-azresource) コマンドレットを使用します。

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

前のコマンドの出力を使用して、各リソースを移動先に移動するためのリソース ID のコンマ区切りリストを作成し、そのリストを [Move-AzResource](/powershell/module/az.resources/move-azresource) で使用できます。

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

リソースを他のサブスクリプションに移動するには、 **-DestinationSubscriptionId** パラメーターを含めます。

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


指定したリソースの移動の確認を求められたら、「**Y**」を入力して確認します。

## <a name="next-steps"></a>次のステップ
リソース グループとサブスクリプションの間でさまざまな種類のリソースを移動できます。 詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。    
