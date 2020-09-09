---
title: Azure CLI を使用して VM を移動する
description: Azure CLI を使用して、VM を別の Azure サブスクリプションまたはリソース グループに移動します。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: db4c7e0126616e2d8bd120e7430c70b89c5cf36d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291097"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>他のサブスクリプションまたはリソース グループへ VM を移動する
この記事では、リソース グループまたはサブスクリプション間で仮想マシン (VM) を移動する方法について説明します。 サブスクリプション間での VM の移動は、個人のサブスクリプションで作成した VM を、会社のサブスクリプションに移動する場合に便利です。

> [!IMPORTANT]
>移動の一環として新しいリソース ID が作成されます。 VM を移動したら、この新しいリソース ID を使用するために、ご使用のツールやスクリプトを更新する必要があります。
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Azure CLI を使用して VM を移動する


Azure CLI を使用して VM を移動するには、先に、移動元と移動先のサブスクリプションが同じテナント内に存在することを確認する必要があります。 両方のサブスクリプションのテナント ID が同じであることを確認するには、[az account show](/cli/azure/account) を使用します。

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
移動元と移動先のサブスクリプションのテナント ID が同じでない場合、新しいテナントにリソースを移動するには、[サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)に問い合わせる必要があります。

VM を適切に移動するには、VM とそのすべての関連リソースを移動する必要があります。 [az resource list](/cli/azure/resource) コマンドを使用して、リソース グループ内のすべてのリソースとその ID を表示します。 これはコマンドの出力をファイルにパイプするときに便利で、ID をコピーして、この後のコマンドに貼り付けることができます。

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

VM とそのリソースを他のリソース グループに移動するには、[az resource move](/cli/azure/resource) を使用します。 次の例では、VM と、その VM に必要な最も一般的なリソースを移動する方法について説明します。 **-ids** パラメーターを使用して、移動するリソースの ID のコンマ区切りのリスト (スペースなし) を渡します。

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

VM とそのリソースを他のサブスクリプションに移動する場合は、 **--destination-subscriptionId** パラメーターを追加して、移動先のサブスクリプションを指定します。

指定したリソースの移動の確認を求められたら、「**Y**」を入力して確認します。

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>次のステップ
リソース グループとサブスクリプションの間でさまざまな種類のリソースを移動できます。 詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。    
