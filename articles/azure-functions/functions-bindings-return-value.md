---
title: Azure Function からの戻り値の使用
description: Azure Functions の戻り値を管理する方法について学習します
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 58ee9b682bc97dc4044d811392cf4ff5b51a69fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491360"
---
# <a name="using-the-azure-function-return-value"></a>Azure Function の戻り値の使用

この記事では、戻り値が関数内でどのように機能するかについて説明します。

戻り値がある言語では、その戻り値に関数の[出力バインディング](./functions-triggers-bindings.md#binding-direction)をバインドできます。

* C# クラス ライブラリでは、メソッド戻り値に出力バインディング属性を適用します。
* Java では、出力バインドの注釈を関数メソッドに適用します。
* その他の言語では、*function.json* 内の `name` プロパティを `$return` に設定します。

複数の出力バインディングが存在する場合は、そのうちの 1 つにのみ戻り値を使用します。

C# と C# スクリプトでは、`out` パラメーターや[コレクター オブジェクト](functions-reference-csharp.md#writing-multiple-output-values)を使用してデータを出力バインディングに送信できます。

# <a name="c"></a>[C#](#tab/csharp)

出力バインディングに戻り値を使用する C# コードと非同期の例を次に示します。

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

C# スクリプト コードと非同期の例を次に示します。

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F#](#tab/fsharp)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

F# コードを次に示します。

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

JavaScript では、戻り値は `context.done` の 2 番目のパラメーターに入ります。

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "Response",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{blobname}"
}
```

http 出力バインディングに戻り値を使用する PowerShell コードを次に示します。

```powershell
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $blobname
    })
```

# <a name="python"></a>[Python](#tab/python)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Python コードを次に示します。

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

出力バインディングに戻り値を使用する Java コードを次に示します。

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のバインド エラーの処理](./functions-bindings-errors.md)
