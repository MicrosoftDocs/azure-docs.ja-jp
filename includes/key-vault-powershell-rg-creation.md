---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070216"
---
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 Azure PowerShell の [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
