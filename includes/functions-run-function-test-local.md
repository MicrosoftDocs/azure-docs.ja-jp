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
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180949"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

次のコマンドで関数アプリを起動します。 アプリは、Azure 内にある同じ Azure Functions ランタイムを使用して実行されます。

```bash
func host start --build
```

C# プロジェクトをコンパイルするには、`--build` オプションが必須です。 JavaScript プロジェクトの場合、このオプションは必要ありません。

Functions ホストが起動すると、次のような出力が表示されます。この出力は、読みやすいように切り詰められています。

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

ランタイム出力から `HttpTrigger` 関数の URL をコピーして、それをブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 ローカルの関数によって返された GET 要求に対するブラウザーでの応答を次に示します。

![ブラウザーでローカルにテストする](./media/functions-run-function-test-local/functions-test-local-browser.png)

ローカルで関数を実行したので、これで、Azure で関数アプリとその他の必要なリソースを作成できます。