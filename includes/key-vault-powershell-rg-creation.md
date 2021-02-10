---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070216"
---
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 Azure PowerShell の [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
