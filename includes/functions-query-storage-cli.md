---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: ca576290ea511dc54b89ecebef72ca2a42e9169f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329600"
---
### <a name="query-the-storage-queue"></a>ストレージ キューに対するクエリを実行する

次の例のように、[`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) コマンドを使用して、アカウントのストレージ キューを表示できます。

```azurecli-interactive
az storage queue list --output tsv
```

このコマンドの出力には、`outqueue` という名前のキューが含まれています。これは、関数の実行時に作成されたキューです。

次に、[`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) コマンドを使用して、このキュー内のメッセージを表示します (以下の例を参照)。

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

返される文字列は、関数をテストするために送信したメッセージと同じものであるはずです。

> [!NOTE]  
> 前の例では、返される文字列を base64 からデコードしています。 これは、Queue ストレージ バインドが [base64 文字列](../articles/azure-functions/functions-bindings-storage-queue.md#encoding)として Azure Storage との間で書き込みおよび読み取りを行うためです。