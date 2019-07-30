---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180954"
---
## <a name="create-a-function"></a>関数を作成する

次のコマンドは、HTTP でトリガーされる `MyHttpTrigger` という名前の関数を作成します。

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

コマンドを実行すると、次のような出力が表示されます。

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
