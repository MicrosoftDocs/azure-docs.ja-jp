---
title: Durable Functions での関数チェーン - Azure
description: 一連の関数を実行する Durable Functions のサンプルを実行する方法を説明します。
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028252"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions での関数チェーン - Hello シーケンス サンプル

関数チェーンとは、特定の順序で一連の関数を実行するパターンです。 ある関数の出力が、別の関数の入力に適用される必要がある、ということがよくあります。 この記事では、Durable Functions のクイックスタート ([C#](durable-functions-create-first-csharp.md)、[JavaScript](quickstart-js-vscode.md)、または [Python](quickstart-python-vscode.md)) を実行するときに作成するチェーンのシーケンスについて説明します。 Durable Functions について詳しくは、「[Durable Functions overview](durable-functions-overview.md)」(Durable Functions の概要) をご覧ください。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>関数

この記事では、サンプル アプリで使用されている次の関数について説明します。

* `E1_HelloSequence`:1 つの [オーケストレーター機能](durable-functions-bindings.md#orchestration-trigger)。1 つのシーケンスで `E1_SayHello` を複数回呼び出します。 `E1_SayHello` 呼び出しからの出力を格納し、結果を記録します。
* `E1_SayHello`:文字列の先頭に "Hello" を付加する [アクティビティ関数](durable-functions-bindings.md#activity-trigger)。
* `HttpStart`:オーケストレーターのインスタンスを起動する HTTP によってトリガーされる[永続的なクライアント](durable-functions-bindings.md#orchestration-client)関数。

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence オーケストレーター関数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

すべての C# オーケストレーション関数は、`Microsoft.Azure.WebJobs.Extensions.DurableTask` アセンブリにある `DurableOrchestrationContext` 型のパラメーターを持つ必要があります。 このコンテキスト オブジェクトでは、他の *アクティビティ* 関数を呼び出し、その `CallActivityAsync` メソッドを使用して入力パラメーターを渡すことができます。

このコードでは、`E1_SayHello` を異なるパラメーター値で 3 回続けて呼び出します。 各呼び出しの戻り値が `outputs` 一覧に追加され、それが関数の末尾に返されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> JavaScript Durable Functions は、Functions 3.0 ランタイムでのみ利用できます。

#### <a name="functionjson"></a>function.json

Visual Studio Code または Azure Portal を開発に使用する場合は、こちらのオーケストレーター関数の *function.json* ファイルの内容をご覧ください。 ほとんどの orchestrator *function.json* ファイルは、このような内容です。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

重要な点は、`orchestrationTrigger` というバインドの種類です。 すべての orchestrator 機能は、このトリガーの種類を使用する必要があります。

> [!WARNING]
> Orchestrator 機能の "I/O なし" の規則に従うには、`orchestrationTrigger` トリガー バインドを使用する場合に、入力または出力バインドを使用しないでください。  他の入力または出力バインドが必要な場合は、`activityTrigger` 関数のコンテキストで使用する必要があります。それらがオーケストレーターによって呼び出されます。 詳細については、「[オーケストレーター関数コードの制約](durable-functions-code-constraints.md)」の記事を参照してください。

#### <a name="indexjs"></a>index.js

オーケストレーター関数をこちらに示します。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

すべての JavaScript オーケストレーション関数に、[`durable-functions` module](https://www.npmjs.com/package/durable-functions) モジュールが含まれている必要があります。 それは、Durable Functions を JavaScript で記述することができるライブラリです。 オーケストレーター関数と他の JavaScript 関数には、次の 3 つの大きな違いがあります。

1. オーケストレーター関数は[ジェネレーター関数](/scripting/javascript/advanced/iterators-and-generators-javascript)です。
2. この関数は、`durable-functions` モジュールの `orchestrator` メソッドの呼び出しにラップされます (ここでは `df`)。
3. この関数は同期的であることが必要です。 "orchestrator" メソッドは呼び出し元の "context.done" を処理するため、この関数は単に "制御を戻す" 必要があります。

`context` オブジェクトには、`df` 永続的なオーケストレーション コンテキスト オブジェクトが含まれています。このオブジェクトを使用すると、他の *アクティビティ* 関数を呼び出し、その `callActivity` メソッドを使用して入力パラメーターを渡すことができます。 このコードでは、異なるパラメーター値で `E1_SayHello` を 3 回続けて呼び出しています。`yield` を使用して実行を示すと、非同期アクティビティ関数呼び出しが返されるのを待つ必要があります。 各呼び出しの戻り値が `outputs` 配列に追加されます。これは、関数の末尾に返されます。

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python Durable Functions は、Functions 3.0 ランタイムでのみ利用できます。


#### <a name="functionjson"></a>function.json

Visual Studio Code または Azure Portal を開発に使用する場合は、こちらのオーケストレーター関数の *function.json* ファイルの内容をご覧ください。 ほとんどの orchestrator *function.json* ファイルは、このような内容です。

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

重要な点は、`orchestrationTrigger` というバインドの種類です。 すべての orchestrator 機能は、このトリガーの種類を使用する必要があります。

> [!WARNING]
> Orchestrator 機能の "I/O なし" の規則に従うには、`orchestrationTrigger` トリガー バインドを使用する場合に、入力または出力バインドを使用しないでください。  他の入力または出力バインドが必要な場合は、`activityTrigger` 関数のコンテキストで使用する必要があります。それらがオーケストレーターによって呼び出されます。 詳細については、「[オーケストレーター関数コードの制約](durable-functions-code-constraints.md)」の記事を参照してください。

#### <a name="__init__py"></a>\_\_init\_\_.py

オーケストレーター関数をこちらに示します。

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

すべての Python オーケストレーション関数に、[`durable-functions` パッケージ](https://pypi.org/project/azure-functions-durable)が含まれている必要があります。 これは、Durable Functions を Python で記述できるようにするライブラリです。 オーケストレーター関数と他の Python 関数には、次の 2 つの大きな違いがあります。

1. オーケストレーター関数は[ジェネレーター関数](https://wiki.python.org/moin/Generators)です。
2. この "_ファイル_" では、ファイルの末尾に `main = df.Orchestrator.create(<orchestrator function name>)` を指定することによって、オーケストレーター関数をオーケストレーターとして登録しています。 これにより、ファイルで宣言されている他のヘルパー関数と区別できます。

`context` オブジェクトでは、他の "*アクティビティ*" 関数を呼び出し、その `call_activity` メソッドを使用して入力パラメーターを渡すことができます。 このコードでは、異なるパラメーター値で `E1_SayHello` を 3 回続けて呼び出しています。`yield` を使用して実行を示すと、非同期アクティビティ関数呼び出しが返されるのを待つ必要があります。 各呼び出しの戻り値は関数の末尾に返されます。

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello アクティビティ関数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

アクティビティは、`ActivityTrigger` 属性を使用します。 指定された `IDurableActivityContext` を使用して、`GetInput<T>` を使用した入力値へのアクセスなど、アクティビティ関連のアクションを実行します。

`E1_SayHello` の実装は、比較的単純な文字列の書式設定操作です。

`IDurableActivityContext`にバインドする代わりに、アクティビティ関数に渡される型に直接バインドすることができます。 次に例を示します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

アクティビティ関数 `E1_SayHello` の *function.json* ファイルは、`E1_HelloSequence` のそれに似ていますが、バインドの種類 `orchestrationTrigger` の代わりにバインドの種類 `activityTrigger` を使用する点が違います。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> オーケストレーション関数によって呼び出されるすべてのアクティビティ関数は、`activityTrigger` バインドを使用する必要があります。

`E1_SayHello` の実装は、比較的単純な文字列の書式設定操作です。

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

オーケストレーション関数とは異なり、アクティビティ関数には特別な設定は不要です。 オーケストレーター関数によって渡される入力は、`activityTrigger` バインドという名前で `context.bindings` オブジェクト (この例では `context.bindings.name`) に配置されます。 サンプル コードに示すように、バインド名はエクスポートされた関数のパラメーターとして設定し、直接アクセスできます。

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

アクティビティ関数 `E1_SayHello` の *function.json* ファイルは、`E1_HelloSequence` のそれに似ていますが、バインドの種類 `orchestrationTrigger` の代わりにバインドの種類 `activityTrigger` を使用する点が違います。

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> オーケストレーション関数によって呼び出されるすべてのアクティビティ関数は、`activityTrigger` バインドを使用する必要があります。

`E1_SayHello` の実装は、比較的単純な文字列の書式設定操作です。

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/\_\_init\_\_.py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

オーケストレーター関数とは異なり、アクティビティ関数には特別な設定は不要です。 オーケストレーター関数によって渡された入力には、関数のパラメーターとして直接アクセスできます。

---

### <a name="httpstart-client-function"></a>HttpStart クライアント関数

オーケストレーター関数のインスタンスを開始するには、クライアント関数を使用します。 `E1_HelloSequence` のインスタンスを開始するには、`HttpStart` HTTP によってトリガーされる関数を使用します。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

オーケストレーター操作をするには、関数に `DurableClient` 入力バインドが含まれている必要があります。 クライアントを使用して、オーケストレーションを開始します。 また、新しいオーケストレーションの状態を確認するための URL を含む HTTP 応答を返すこともできます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

オーケストレーター操作をするには、関数に `durableClient` 入力バインドが含まれている必要があります。

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

`DurableOrchestrationClient` オブジェクトを取得するには、`df.getClient` を使用します。 クライアントを使用して、オーケストレーションを開始します。 また、新しいオーケストレーションの状態を確認するための URL を含む HTTP 応答を返すこともできます。

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

オーケストレーター操作をするには、関数に `durableClient` 入力バインドが含まれている必要があります。

#### <a name="httpstart__init__py"></a>HttpStart/\_\_init\_\_.py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

`DurableOrchestrationClient` を使用して、Durable Functions クライアントを取得します。 クライアントを使用して、オーケストレーションを開始します。 また、新しいオーケストレーションの状態を確認するための URL を含む HTTP 応答を返すこともできます。

---

## <a name="run-the-sample"></a>サンプルを実行する

`E1_HelloSequence` オーケストレーションを実行するには、次の HTTP POST 要求を `HttpStart` 関数に送信します。

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> 前の HTTP スニペットでは、既定の `api/` プレフィックスをすべての HTTP トリガー関数 URL から削除するエントリが `host.json` ファイルにあることを想定しています。 この構成のマークアップはサンプルの `host.json` ファイルにあります。

たとえば、"myfunctionapp" という関数アプリのサンプルを実行する場合、"{host}" を "myfunctionapp.azurewebsites.net" に置き換えます。

結果は次のような HTTP 202 応答です (簡潔にするため省略しています)。

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

この時点で、オーケストレーションはキューに登録され、すぐに実行を開始します。 `Location` ヘッダー内の URL は、実行状態を確認するのに使用できます。

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

この結果がオーケストレーションの状態です。 迅速に実行して完了し、次のような応答で *完了* 状態にあることがわかります(簡潔にするため省略しています)。

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

このように、インスタンスの `runtimeStatus` は *完了* しており、`output` は orchestrator 関数の実行の JSON でシリアル化された結果を格納します。

> [!NOTE]
> 同様のスターター ロジックを `queueTrigger`、`eventHubTrigger`、`timerTrigger` などの他のトリガーの種類に実装することができます。

関数の実行ログを確認しましょう。 `E1_HelloSequence` 関数が、[オーケストレーションの信頼性](durable-functions-orchestrations.md#reliability)に関するトピックで説明されている再生の動作のため、複数回、開始し完了しています。 その一方で、`E1_SayHello` の実行は 3 回だけでした。これらの関数の実行は再生されなかったためです。

## <a name="next-steps"></a>次のステップ

このサンプルでは、単純な関数チェーンのオーケストレーションについて説明しました。 次のサンプルでは、ファンアウト/ファンイン パターンの実装方法について説明します。

> [!div class="nextstepaction"]
> [ファンアウト/ファンイン サンプルの実行](durable-functions-cloud-backup.md)
