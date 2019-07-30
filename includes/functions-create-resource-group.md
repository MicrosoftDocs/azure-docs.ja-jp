---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444092"
---
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group) でリソース グループを作成します。 Azure リソース グループとは、Function App、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、`myResourceGroup` という名前のリソース グループを作成します。  
Cloud Shell を使用していない場合は、まず `az login` でサインインします。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

通常は、近くの[リージョン](https://azure.microsoft.com/global-infrastructure/regions/)にリソース グループおよびリソースを作成します。 
