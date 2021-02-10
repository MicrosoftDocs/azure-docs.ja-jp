---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070279"
---
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 [az group create](/cli/azure/group#az_group_create) コマンドを使用して、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
