---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108976"
---
## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトが実行された後で、次のコマンドを使用して、リソース グループ、Azure Cache for Redis インスタンス、およびリソース グループ内のすべての関連リソースを削除できます。

```azurecli
az group delete --name contosoGroup
```