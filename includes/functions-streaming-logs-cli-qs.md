---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424853"
---
凖リアルタイムの[ストリーミング ログ](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)を表示するには、次のコマンドを実行します。

```console
func azure functionapp logstream <APP_NAME> 
```

別のターミナル ウィンドウまたはブラウザーで、もう一度リモート関数を呼び出します。 Azure で実行された関数の詳細ログがターミナルに表示されます。 