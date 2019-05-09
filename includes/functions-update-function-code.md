---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 3b0be31afbce78c0de8dc919245e92bee7af04b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964196"
---
## <a name="update-the-function"></a>関数を更新する

既定では、テンプレートによって、要求時に関数キーを要求する関数が作成されます。 Azure で関数をテストしやすくするために、匿名アクセスを許可するように関数を更新する必要があります。 この変更を行う方法は、関数プロジェクトの言語によって異なります。

### <a name="c"></a>C\#

新しい関数である MyHttpTrigger.cs コード ファイルを開き、関数定義の中の **AuthorizationLevel** 属性の値を `Anonymous` に更新し、変更を保存します。

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

新しい関数である function.json ファイルをテキスト エディターで開き、**bindings.httpTrigger** 内の **authLevel** プロパティ を `anonymous` に更新し、変更を保存します。

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

これで、関数キーを指定することなく Azure で関数を呼び出すことができます。 ローカルでの実行時に関数キーが要求されることはありません。
