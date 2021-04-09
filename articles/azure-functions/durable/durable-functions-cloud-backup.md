---
title: Durable Functions のファンアウト/ファンイン シナリオ - Azure
description: Azure Functions の Durable Functions 拡張機能でファンアウト ファンイン シナリオを実装する方法を説明します。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 91128033696af6a56488db7991987f1e384b719e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027650"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Durable Functions のファンアウト/ファンイン シナリオ - クラウド バックアップの例

"*ファンアウト/ファンイン*" は、複数の関数を同時に実行した後、その結果に対して集計を行うパターンを指します。 この記事では、[Durable Functions](durable-functions-overview.md) を使用してファンイン/ファンアウト シナリオを実装するサンプルについて説明します。 このサンプルは、アプリのサイトのコンテンツの一部またはすべてを Azure Storage にバックアップする永続関数です。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>シナリオの概要

このサンプルでは、関数は、指定されたディレクトリの下にあるすべてのファイルを BLOB ストレージに再帰的にアップロードします。 さらに、アップロードされたバイトの合計数をカウントします。

すべてを管理する単一の関数を記述できます。 その際に発生する最大の問題は **スケーラビリティ** です。 単一の関数は、単一の仮想マシンでのみ実行できるため、スループットは単一の VM のスループットによって制限されます。 別の問題として、**信頼性** があります。 途中でエラーが発生した場合、またはプロセス全体が 5 分以上かかる場合、バックアップは部分的に完了した状態で失敗する可能性があります。 これにより、再起動が必要になることがあります。

もっと堅牢な方法は、2 つの標準的な関数 (ファイルを列挙し、ファイル名をキューに追加する関数と、キューからファイルを読み取り、そのファイルを BLOB ストレージにアップロードする関数) を記述することです。 このアプローチにより、スループットと信頼性は向上しますが、キューのプロビジョニングと管理を行う必要があります。 さらに重要なのは、アップロードされた合計バイト数の報告などを行うと、**状態管理** と **調整** が非常に複雑になることです。

Durable Functions を使用する方法は、上記の利点を非常に少ないオーバーヘッドで実現できます。

## <a name="the-functions"></a>関数

この記事では、サンプル アプリで使用されている次の関数について説明します。

* `E2_BackupSiteContent`:`E2_GetFileList` を呼び出してバックアップするファイルのリストを取得してから、`E2_CopyFileToBlob` を呼び出して各ファイルをバックアップする[オーケストレーター関数](durable-functions-bindings.md#orchestration-trigger)。
* `E2_GetFileList`:ディレクトリ内のファイルのリストを返す[アクティビティ関数](durable-functions-bindings.md#activity-trigger)。
* `E2_CopyFileToBlob`:1 つのファイルを Azure Blob Storage にバックアップするアクティビティ関数。

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent オーケストレーター関数

このオーケストレーター関数は、基本的に次の操作を行います。

1. `rootDirectory` 値を入力パラメーターとして使用します。
2. `rootDirectory` の下のファイルの再帰リストを取得する関数を呼び出します。
3. 複数の並列関数を呼び出して、各ファイルを Azure Blob Storage にアップロードします。
4. すべてのアップロードが完了するまで待機します。
5. Azure Blob ストレージにアップロードされたバイト数の合計を返します。

# <a name="c"></a>[C#](#tab/csharp)

オーケストレーター関数を実装するコードを次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

`await Task.WhenAll(tasks);` 行に注目してください。 `E2_CopyFileToBlob` 関数への個々の呼び出しがすべて待機されていて並列実行が可能な *わけではありません*。 このタスク配列を `Task.WhenAll` に渡すと、"*すべてのコピー操作が完了するまで*" 完了することがない 1 つのタスクが戻ります。 .NET のタスク並列ライブラリ (TPL) を知っていれば、これは新しい事柄ではありません。 違いは、これらのタスクが複数の仮想マシンで同時に実行される可能性があることと、Durable Functions 拡張機能によって、プロセスのリサイクルに対してエンド ツー エンドの実行が回復することが保証されることです。

`Task.WhenAll` から応答が返ることは、すべての関数呼び出しが完了し、値が戻っていることを意味します。 `E2_CopyFileToBlob` への各呼び出しがアップロードしたバイト数を返しているため、バイト数の合計を計算することは、これらの返された値をすべて合計するだけの操作です。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

この関数では、オーケストレーター関数の標準的な *function.json* が使用されます。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

オーケストレーター関数を実装するコードを次に示します。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

`yield context.df.Task.all(tasks);` 行に注目してください。 `E2_CopyFileToBlob` 関数への個々の呼び出しがすべて中断され、並列実行が可能になる *わけではありません*。 このタスク配列を `context.df.Task.all` に渡すと、"*すべてのコピー操作が完了するまで*" 完了することがない 1 つのタスクが戻ります。 JavaScript の [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) に慣れている方には、これは目新しいものではないでしょう。 違いは、これらのタスクが複数の仮想マシンで同時に実行される可能性があることと、Durable Functions 拡張機能によって、プロセスのリサイクルに対してエンド ツー エンドの実行が回復することが保証されることです。

> [!NOTE]
> タスクは概念的には JavaScript の Promise に似ていますが、タスクの並列化を管理するために、オーケストレーター関数は `Promise.all` と `Promise.race` の代わりに `context.df.Task.all` と `context.df.Task.any` を使用する必要があります。

`context.df.Task.all` が中断した後、すべての関数呼び出しが完了し、戻り値が返されたことがわかります。 `E2_CopyFileToBlob` への各呼び出しがアップロードしたバイト数を返しているため、バイト数の合計を計算することは、これらの返された値をすべて合計するだけの操作です。

# <a name="python"></a>[Python](#tab/python)

この関数では、オーケストレーター関数の標準的な *function.json* が使用されます。

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/function.json)]

オーケストレーター関数を実装するコードを次に示します。

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/\_\_init\_\_.py)]

`yield context.task_all(tasks);` 行に注目してください。 `E2_CopyFileToBlob` 関数への個々の呼び出しがすべて中断され、並列実行が可能になる *わけではありません*。 このタスク配列を `context.task_all` に渡すと、"*すべてのコピー操作が完了するまで*" 完了することがない 1 つのタスクが戻ります。 Python の [`asyncio.gather`](https://docs.python.org/3/library/asyncio-task.html#asyncio.gather) に慣れている方には、これは目新しいものではないでしょう。 違いは、これらのタスクが複数の仮想マシンで同時に実行される可能性があることと、Durable Functions 拡張機能によって、プロセスのリサイクルに対してエンド ツー エンドの実行が回復することが保証されることです。

> [!NOTE]
> タスクは、概念的には Python の awaitable に似ていますが、タスクの並列化を管理するために、オーケストレーター関数では `yield` と `context.task_all` および `context.task_any` API を使用する必要があります。

`context.task_all` が中断した後、すべての関数呼び出しが完了し、戻り値が返されたことがわかります。 `E2_CopyFileToBlob` を呼び出すたびに、アップロードされたバイト数が返されるので、すべての戻り値を合計することによって合計バイト数を計算できます。

---

### <a name="helper-activity-functions"></a>ヘルパー アクティビティ関数

ヘルパー アクティビティ関数は、他のサンプルと同じように、`activityTrigger` トリガー バインドを使う標準的な関数です。

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList アクティビティ関数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E2_GetFileList` の *function.json* ファイルは次のようになります。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

その実装を次に示します。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

関数は、`readdirp` モジュール (バージョン 2.x) を使用してディレクトリ構造を再帰的に読み取ります。

# <a name="python"></a>[Python](#tab/python)

`E2_GetFileList` の *function.json* ファイルは次のようになります。

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/function.json)]

その実装を次に示します。

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/\_\_init\_\_.py)]

---

> [!NOTE]
> このコードをオーケストレーター関数に直接配置できないことを疑問に思うかもしれません。 配置することは可能ですが、それを行うと、オーケストレーター関数の基本ルールの 1 つである、ローカル ファイル システムへのアクセスを含めて I/O 操作を行うべきではないというルールを破ることになります。 詳細については、「[オーケストレーター関数コードの制約](durable-functions-code-constraints.md)」を参照してください。

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob アクティビティ関数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> サンプル コードを実行するには、`Microsoft.Azure.WebJobs.Extensions.Storage` NuGet パッケージをインストールする必要があります。

関数は、Azure Functions のバインドの高度な機能を使用します (つまり、[`Binder` パラメーター](../functions-dotnet-class-library.md#binding-at-runtime) の使用) が、このチュートリアルでは、詳細を気にする必要はありません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E2_CopyFileToBlob` の *function.json* ファイルも同じように単純です。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

JavaScript の実装では、[Azure Storage SDK for Node](https://github.com/Azure/azure-storage-node) を使用して、ファイルを Azure Blob Storage にアップロードします。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

# <a name="python"></a>[Python](#tab/python)

`E2_CopyFileToBlob` の *function.json* ファイルも同じように単純です。

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/function.json)]

Python の実装では、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) を使用して、ファイルを Azure Blob Storage にアップロードします。

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/\_\_init\_\_.py)]

---

この実装は、ディスクからファイルを読み込み、"backups" コンテナー内の同じ名前の BLOB に内容を非同期でストリーミングします。 戻り値はストレージにコピーされたバイト数であり、この数値がオーケストレーター関数によって集計の合計を計算するために使用されます。

> [!NOTE]
> これは、I/O 操作を `activityTrigger` 関数に移動させる完璧な例です。 作業をさまざまなマシンに分散できるだけではなく、進行状況のチェックポイント処理のメリットも得ることができます。 ホスト プロセスが何らかの理由で終了した場合でも、どのアップロードが完了しているかがわかります。

## <a name="run-the-sample"></a>サンプルを実行する

次の HTTP POST 要求を送信することによって、Windows でオーケストレーションを開始できます。

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

または、Linux Function App で (現在、Python は Linux for App Service でのみ実行されます)、次のようにオーケストレーションを開始することもできます。

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"/home/site/wwwroot"
```

> [!NOTE]
> 呼び出している `HttpStart` 関数は、JSON 形式のコンテンツでのみ動作します。 このため、`Content-Type: application/json` ヘッダーは必須であり、ディレクトリ パスは JSON 文字列としてエンコードされます。 さらに、HTTP スニペットでは、既定の `api/` プレフィックスをすべての HTTP トリガー関数 URL から削除するエントリが `host.json` ファイルにあることを想定しています。 この構成のマークアップはサンプルの `host.json` ファイルにあります。

この HTTP 要求で `E2_BackupSiteContent` オーケストレーターがトリガーされ、文字列 `D:\home\LogFiles` がパラメーターとして渡されます。 応答は、このバックアップ操作の状態を取得するためのリンクを提供します。

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

この操作は、関数アプリ内のログ ファイルの数によっては、完了するまで数分かかる場合があります。 前の HTTP 202 応答の `Location` ヘッダー内の URL をクエリすることで、最新の状態を取得できます。

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

ここでは、関数はまだ実行中です。 オーケストレーターの状態と最終更新時間に保存された入力を確認できます。 `Location` ヘッダーの値を引き続き使用して、完了するまでポーリングできます。 状態が "Completed" になると、次のような HTTP 応答値が表示されます。

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

これで、オーケストレーションが完了したこと、完了までにかかったおおよその時間を確認できます。 `output` フィールドの値から、約 450 KB のログがアップロードされたことも確認できます。

## <a name="next-steps"></a>次のステップ

このサンプルでは、ファンアウト/ファンイン パターンの実装方法について説明しました。 次のサンプルでは、[永続的タイマー](durable-functions-timers.md)を使用して監視パターンを実装する方法を示します。

> [!div class="nextstepaction"]
> [モニターのサンプルを実行する](durable-functions-monitor.md)