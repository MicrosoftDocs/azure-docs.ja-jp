---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512810"
---
**mySecret** という名前で値が **Success!** のシークレットを作成しましょう。 シークレットとしては、パスワード、SQL 接続文字列、または安全性とアプリケーションから利用できることの両方を維持する必要がある他の情報などがあります。 

シークレットを新しく作成したキー コンテナーに追加するには、Azure CLI の [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) コマンドを使用します。

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```