---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f953c7ae1bba774a69bffddb148c93609c9a85c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855613"
---
## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースの作業が完了したら、次のコマンドを使用して、ご利用のサブスクリプションから削除してください。

```azurecli-interactive
az group delete --name myResourceGroup
```

確認を求められたら `y` と入力します。 このコマンドを実行すると、`myResourceGroup` リソース グループだけでなく、そこに属しているリソースもすべて削除されます。