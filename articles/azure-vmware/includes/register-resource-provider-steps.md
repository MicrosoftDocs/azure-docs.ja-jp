---
title: Azure VMware Solution リソース プロバイダーの登録
description: Azure VMware Solution リソース プロバイダーを登録する手順。
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555835"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Azure VMware Solution を使用するには、最初にリソース プロバイダーをサブスクリプションに登録する必要があります。 リソース プロバイダーの詳細については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。


### <a name="portal"></a>[ポータル](#tab/azure-portal)
 
1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal のメニューで、**[すべてのサービス]** を選択します。

1. **[すべてのサービス]** ボックスで **"サブスクリプション (subscription)"** と入力し、**[サブスクリプション]** を選択します。

1. サブスクリプションの一覧から、表示するサブスクリプションを選択します。

1. **[リソース プロバイダー]** を選択し、検索対象として「**Microsoft.AVS**」と入力します。 
 
1. リソースプロバイダーが登録されていない場合は、 **[登録]** を選択します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の使用を開始するには:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI を使用して、Azure VMware Solution のデプロイに使用する Azure サブスクリプションにサインインします。 [az provider register](/cli/azure/provider#az_provider_register) コマンドを使用して、`Microsoft.AVS` リソース プロバイダーを登録します。

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

[az provider list](/cli/azure/provider#az_provider_list) コマンドを使用すると、使用可能なすべてのプロバイダーを表示できます。

---


 
