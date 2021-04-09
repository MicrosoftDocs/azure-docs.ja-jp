---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "67180979"
---
## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトが実行された後で、次のコマンドを使用して、リソース グループ、Azure Cache for Redis インスタンス、およびリソース グループ内のすべての関連リソースを削除できます。

```azurecli
az group delete --name contosoGroup
```