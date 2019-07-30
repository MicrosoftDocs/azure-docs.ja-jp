---
title: Azure で Windows VM リソースを移動する | Microsoft Docs
description: Resource Manager デプロイ モデルで Windows VM を他の Azure サブスクリプションまたはリソース グループに移動します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 6b189c4bfcc61084ed197649d376cae8fdf2eb56
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723092"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>他の Azure サブスクリプションまたはリソース グループへの Windows VM の移動
この記事では、リソース グループまたはサブスクリプション間で Windows 仮想マシン (VM) を移動する方法について説明します。 サブスクリプション間での移動は、個人のサブスクリプションで作成した VM を、会社のサブスクリプションに移動して作業を続ける場合に便利です。 移動するために VM を開始する必要はありません。移動の間も、継続して実行される必要があります。

> [!IMPORTANT]
>移動の一環として新しいリソース ID が作成されます。 VM を移動したら、この新しいリソース ID を使用するために、ご使用のツールやスクリプトを更新する必要があります。 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>PowerShell を使用した VM の移動

仮想マシンを他のリソース グループに移動するには、依存リソースも必ずすべて移動する必要があります。 これらのリソースごとにリソース ID を含むリストを取得するには、[Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) コマンドレットを使用します。

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

[Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) に対し、リソース ID のコンマ区切りリストとして前のコマンドの出力を使用すると、各リソースを移動先に移動できます。 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
リソースを他のサブスクリプションに移動するには、 **-DestinationSubscriptionId** パラメーターを含めます。 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


指定したリソースの移動の確認を求められたら、「**Y**」を入力して確認します。

## <a name="next-steps"></a>次の手順
リソース グループとサブスクリプションの間でさまざまな種類のリソースを移動できます。 詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../resource-group-move-resources.md)」を参照してください。    

