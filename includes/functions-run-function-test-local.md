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
ms.openlocfilehash: dcfa65c655d1508510282fe66e90e4076278b29b
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949942"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

次のコマンドで関数アプリを起動します。 アプリは、Azure 内にある同じ Azure Functions ランタイムを使用して実行されます。 起動コマンドは、使用するプロジェクトの言語によって異なります。

### <a name="c"></a>C\#

```command
func start --build
```

### <a name="javascript"></a>JavaScript

```command
func start
```

### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

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