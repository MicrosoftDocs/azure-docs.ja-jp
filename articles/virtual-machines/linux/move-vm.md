---
title: "Azure で Linux VM を移動する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで Linux VM を他の Azure サブスクリプションまたはリソース グループに移動します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 4695a9c934f97f2b2d448c4990e7ad5533e38e9f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>他のサブスクリプションまたはリソース グループへの Linux VM の移動
この記事では、リソース グループまたはサブスクリプション間で Linux VM を移動する方法について説明します。 サブスクリプション間での VM の移動は、個人のサブスクリプションで作成した VM を、会社のサブスクリプションに移動する場合に便利です。

> [!IMPORTANT]
>現時点では Managed Disks を移動することはできません。 
>
>移動の一環として新しいリソース ID が作成されます。 VM を移動したら、この新しいリソース ID を使用するために、ツールやスクリプトを更新する必要があります。 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Azure CLI を使用して VM を移動する
VM を適切に移動するには、VM とそのすべての関連リソースを移動する必要があります。 **azure group show** コマンドを使用して、リソース グループのすべてのリソースとその ID を表示します。 これはコマンドの出力をファイルにパイプするときに便利で、ID をコピーして、この後のコマンドに貼り付けることができます。

    azure group show <resourceGroupName>

VM とそのリソースを他のリソース グループに移動するには、 **azure resource move** CLI コマンドを使用します。 次の例では、VM と、その VM に必要な最も一般的なリソースを移動する方法について説明します。 **-i** パラメーターを使用して、移動するリソースの ID のコンマ区切りのリスト (スペースなし) を渡します。

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

VM とそのリソースを他のサブスクリプションに移動する場合は、**--destination-subscriptionId &#60;destinationSubscriptionID&#62;** パラメーターを追加して、移動先のサブスクリプションを指定します。

Windows コンピューターでコマンド プロンプトから操作する場合は、宣言するときに、変数名の前に **$** を追加する必要があります。 これは、Linux では必要ありません。

指定したリソースの移動を確認するように求められたら、 「 **Y** 」を入力して、リソースの移動を確定します。

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>次のステップ
リソース グループとサブスクリプションの間でさまざまな種類のリソースを移動できます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../../resource-group-move-resources.md)」を参照してください。    

