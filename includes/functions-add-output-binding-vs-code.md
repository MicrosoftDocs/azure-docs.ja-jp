---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493949"
---
Visual Studio Code では、便利な一連のプロンプトに従って、function.json ファイルにバインドを追加できます。 

バインドを追加するには、コマンド パレット (F1) を開き、「**Azure Functions: バインドの追加...** 」と入力します。次に、新しいバインドの関数を選択して、プロンプトに従います。プロンプトは、関数に追加されているバインドの種類によって異なります。 

以下は、新しいストレージ出力バインドを定義するためのプロンプト例です。

| Prompt | 値 | 説明 |
| -------- | ----- | ----------- |
| **Select binding direction (バインド方向を選択する)** | `out` | バインドは出力バインドです。 |
| **Select binding with direction (方向を使用してバインドを選択する)** | `Azure Queue Storage` | バインドは Azure Storage キュー バインドです。 |
| **コードでこのバインドの特定に使用する名前** | `msg` | コードで参照されているバインド パラメーターを識別する名前。 |
| **The queue to which the message will be sent (メッセージの送信先のキュー)** | `outqueue` | バインドが書き込むキューの名前。 *queueName* が存在しない場合は、バインドによって最初に使用されるときに作成されます。 |
| **Select setting from "local.settings.json" ("local.settings.json" から設定を選択する)** | `MyStorageConnection` | ストレージ アカウントの接続文字列を含むアプリケーション設定の名前。 `AzureWebJobsStorage` 設定には、関数アプリで作成したストレージ アカウントの接続文字列が含まれています。 |

また、関数フォルダー内の **function.json** ファイルを直接右クリック (macOS では Ctrl キーを押しながらクリック) して、 **[バインドの追加]** を選択し、同じプロンプトに従うこともできます。

この例では、次のバインドが function.json ファイルの `bindings` 配列に追加されます。

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```