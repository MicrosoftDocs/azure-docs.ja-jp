---
title: Durable Functions での関数チェーン - Azure
description: 一連の関数を実行する Durable Functions のサンプルを実行する方法を説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee5b18ddc734335ddac2a7d3352de0e4388f445d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933269"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions での関数チェーン - Hello シーケンス サンプル

関数チェーンとは、特定の順序で一連の関数を実行するパターンです。 ある関数の出力が、別の関数の入力に適用される必要がある、ということがよくあります。 この記事では、Durable Functions のクイックスタート ([C#](durable-functions-create-first-csharp.md) または [JavaScript](quickstart-js-vscode.md)) を実行するときに作成するチェーンのシーケンスについて説明します。 Durable Functions について詳しくは、「[Durable Functions overview](durable-functions-overview.md)」(Durable Functions の概要) をご覧ください。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>関数

この記事では、サンプル アプリで使用されている次の関数について説明します。

* `E1_HelloSequence`:1 つのシーケンスで `E1_SayHello` を複数回呼び出す orchestrator 機能。 `E1_SayHello` 呼び出しからの出力を格納し、結果を記録します。
* `E1_SayHello`:文字列の先頭に "Hello" を付加するアクティビティ関数。

以下のセクションでは、C# スクリプトと JavaScript で使用される構成とコードについて説明します。 Visual Studio 開発用のコードは、この記事の最後に記載されています。

> [!NOTE]
> JavaScript Durable Functions は、Functions 2.x ランタイムでのみ利用できます。

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>function.json ファイル

Visual Studio Code または Azure Portal を開発に使用する場合は、こちらのオーケストレーター関数の *function.json* ファイルの内容をご覧ください。 ほとんどの orchestrator *function.json* ファイルは、このような内容です。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

重要な点は、`orchestrationTrigger` というバインドの種類です。 すべての orchestrator 機能は、このトリガーの種類を使用する必要があります。

> [!WARNING]
> Orchestrator 機能の "I/O なし" の規則に従うには、`orchestrationTrigger` トリガー バインドを使用する場合に、入力または出力バインドを使用しないでください。  他の入力または出力バインドが必要な場合は、`activityTrigger` 関数のコンテキストで使用する必要があります。それらがオーケストレーターによって呼び出されます。

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C# スクリプト (Visual Studio Code と Azure Portal のサンプル コード)

以下がソース コードです。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

すべての C# オーケストレーション関数は、`Microsoft.Azure.WebJobs.Extensions.DurableTask` アセンブリにある `DurableOrchestrationContext` 型のパラメーターを持つ必要があります。 C# スクリプトを使用している場合、アセンブリは `#r` 表記を使用して参照できます。 このコンテキスト オブジェクトでは、他の*アクティビティ*関数を呼び出し、その [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) メソッドを使用して入力パラメーターを渡すことができます。

このコードでは、`E1_SayHello` を異なるパラメーター値で 3 回続けて呼び出します。 各呼び出しの戻り値が `outputs` 一覧に追加され、それが関数の末尾に返されます。

### <a name="javascript"></a>JavaScript

以下がソース コードです。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

すべての JavaScript オーケストレーション関数に、[`durable-functions` module](https://www.npmjs.com/package/durable-functions) モジュールが含まれている必要があります。 これは、Durable Functions を JavaScript で記述することができるライブラリです。 オーケストレーション関数と他の JavaScript 関数には、次の 3 つの大きな違いがあります。

1. この関数は[ジェネレーター関数](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)です。
2. この関数は、`durable-functions` モジュールの `orchestrator` メソッドの呼び出しにラップされます (ここでは `df`)。
3. この関数は同期的であることが必要です。 "orchestrator" メソッドは呼び出し元の "context.done" を処理するため、この関数は単に "制御を戻す" 必要があります。

`context` オブジェクトには、他の "*アクティビティ*" 関数を呼び出し、`callActivity` メソッドを使用して入力パラメーターを渡すことができる、`df` オブジェクトが含まれています。 このコードでは、異なるパラメーター値で `E1_SayHello` を 3 回続けて呼び出しています。`yield` を使用して実行を示すと、非同期アクティビティ関数呼び出しが返されるのを待つ必要があります。 各呼び出しの戻り値が `outputs` 一覧に追加され、それが関数の末尾に返されます。

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>function.json ファイル

アクティビティ関数 `E1_SayHello` の *function.json* ファイルは、`E1_HelloSequence` のそれに似ていますが、バインドの種類 `orchestrationTrigger` の代わりにバインドの種類 `activityTrigger` を使用する点が違います。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> オーケストレーション関数によって呼び出される関数は、`activityTrigger` バインドを使用する必要があります。

`E1_SayHello` の実装は、比較的単純な文字列の書式設定操作です。

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

この関数は [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 型のパラメーターを持ち、オーケストレーター関数の [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) への呼び出しによって渡された入力を取得するのに使用されます。

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

JavaScript オーケストレーション関数とは異なり、アクティビティ関数には特別な設定は不要です。 オーケストレーター関数によって渡される入力は、`activityTrigger` バインドという名前で `context.bindings` オブジェクト (この例では `context.bindings.name`) に配置されます。 サンプル コードに示すように、バインド名はエクスポートされた関数のパラメーターとして設定し、直接アクセスできます。

## <a name="run-the-sample"></a>サンプルを実行する

`E1_HelloSequence` オーケストレーションを実行するには、次の HTTP POST 要求を送信します。

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
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

この時点で、オーケストレーションはキューに登録され、すぐに実行を開始します。 `Location` ヘッダー内の URL は、実行状態を確認するのに使用できます。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

この結果がオーケストレーションの状態です。 迅速に実行して完了し、次のような応答で*完了*状態にあることがわかります(簡潔にするため省略しています)。

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

このように、インスタンスの `runtimeStatus` は*完了*しており、`output` は orchestrator 関数の実行の JSON でシリアル化された結果を格納します。

> [!NOTE]
> orchestrator 関数を開始した HTTP POST エンドポイントは、"HttpStart" という名前の HTTP トリガー関数としてサンプル アプリに実装されています。 同様のスターター ロジックを `queueTrigger`、`eventHubTrigger`、`timerTrigger` などの他のトリガーの種類に実装することができます。

関数の実行ログを確認しましょう。 `E1_HelloSequence` 関数が、[オーケストレーションの信頼性](durable-functions-orchestrations.md#reliability)に関するトピックで説明されている再生の動作のため、複数回、開始し完了しています。 その一方で、`E1_SayHello` の実行は 3 回だけでした。これらの関数の実行は再生されなかったためです。

## <a name="visual-studio-sample-code"></a>Visual Studio サンプル コード

Visual Studio プロジェクトの単一の C# ファイルとしてのオーケストレーションを次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>次の手順

このサンプルでは、単純な関数チェーンのオーケストレーションについて説明しました。 次のサンプルでは、ファンアウト/ファンイン パターンの実装方法について説明します。

> [!div class="nextstepaction"]
> [ファンアウト/ファンイン サンプルの実行](durable-functions-cloud-backup.md)
