---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673311"
---
Java プロジェクトでは、バインドは関数メソッドのバインド注釈として定義されます。 その後、これらの注釈に基づいて *function.json* ファイルが自動的に生成されます。

_src/main/java_ の下の対象の関数コードの場所を参照し、*Function.java* プロジェクト ファイルを開きます。`run` メソッド定義に、次のパラメーターを追加します。

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

`msg` パラメーターは [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 型です。これは、関数の完了時に出力バインドにメッセージとして書き込まれる文字列のコレクションを表します。 この場合、出力は `outqueue` という名前のストレージ キューです。 このストレージ アカウントの接続文字列は、`connection` メソッドによって設定されます。 接続文字列自体ではなく、ストレージ アカウントの接続文字列を含むアプリケーション設定を渡します。

`run` メソッドの定義は次の例のようになります。  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```