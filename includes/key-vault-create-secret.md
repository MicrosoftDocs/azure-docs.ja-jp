---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244706"
---
**mySecret** という名前で値が **Success!** のシークレットを作成しましょう。 シークレットとしては、パスワード、SQL 接続文字列、または安全性とアプリケーションから利用できることの両方を維持する必要がある他の情報などがあります。 

シークレットを新しく作成したキー コンテナーに追加するには、Azure CLI の [az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) コマンドを使用します。

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```