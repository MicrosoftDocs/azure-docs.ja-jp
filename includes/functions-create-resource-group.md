---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0fba49ec5a610d03ece2dce7fa5bb0d9d094ab4c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203152"
---
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Function App、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、`myResourceGroup` という名前のリソース グループを作成します。  
Cloud Shell を使用していない場合は、まず `az login` を使用してサインインします。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

通常は、近くの[リージョン](https://azure.microsoft.com/global-infrastructure/regions/)にリソース グループおよびリソースを作成します。 
