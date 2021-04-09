---
title: Durable Functions のモニター (Python) - Azure
description: Azure Functions の Durable Functions 拡張機能を使用して状態モニターを実装する方法 (Python) について説明します。
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434765"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Durable Functions のモニター シナリオ - GitHub issue の監視のサンプル

モニター パターンは、ワークフローの柔軟な "繰り返し" プロセスを参照します。たとえば、特定の条件が満たされるまでポーリングします。 この記事では、Durable Functions を使って監視を実装するサンプルを説明します。

## <a name="prerequisites"></a>前提条件

* [クイックスタートの記事を完了していること](quickstart-python-vscode.md)
* [GitHub からサンプル プロジェクトを複製またはダウンロードしていること](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>シナリオの概要

このサンプルは、GitHub リポジトリ内の issue の数を監視し、open の issue が 3 件以上ある場合にユーザーに警告します。 タイマーによって定期的にトリガーされる関数を使用して、オープンされている issue の数を一定の間隔で要求できます。 ただし、このアプローチの 1 つの問題は **有効期間管理** です。 送信する必要があるアラートが 1 つだけの場合は、3 件以上の issue が検出された後に、モニター自体を無効にする必要があります。 特にメリットがあるのは、監視パターンがそれ自体の実行を終了できることです。

* モニターは、スケジュールではなく一定間隔で実行されます。タイマーは "*実行*" を毎時トリガーし、モニターはアクション間で 1 時間 "*待機*" します。 モニターのアクションは、指定されない限り重複しません。これは実行時間の長いタスクで重要です。
* モニターの間隔は動的にすることができます。待機時間は、いくつかの条件に基づいて変化することがあります。
* モニターは、ある条件が満たされたときに終了することも、別のプロセスによって終了することもできます。
* モニターは、パラメーターを受け取ることができます。 サンプルでは、要求されたすべてのパブリック GitHub リポジトリと電話番号に同じリポジトリ監視プロセスを適用する方法を示します。
* モニターはスケーラブルです。 各モニターはオーケストレーション インスタンスであるため、新しい関数を作成したり、コードをさらに定義したりしなくても、複数のモニターを作成できます。
* モニターは、より大規模なワークフローと簡単に統合できます。 モニターは、より複雑なオーケストレーション関数の 1 つのセクション、つまり[サブ オーケストレーション](durable-functions-sub-orchestrations.md)にすることができます。

## <a name="configuration"></a>構成

### <a name="configuring-twilio-integration"></a>Twilio 統合の構成

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>関数

この記事では、サンプル アプリで使用されている次の関数について説明します。

* `E3_Monitor`:`E3_TooManyOpenIssues` を定期的に呼び出す[オーケストレーター関数](durable-functions-bindings.md#orchestration-trigger)です。 `E3_TooManyOpenIssues` の戻り値が `True` である場合は、`E3_SendAlert` を呼び出します。
* `E3_TooManyOpenIssues`:リポジトリに存在する open の issue が多すぎるかどうかを確認する[アクティビティ関数](durable-functions-bindings.md#activity-trigger)。 デモのために、open の issue が 3 件以上あれば多すぎるとみなします。
* `E3_SendAlert`:Twilio 経由で SMS メッセージを送信するアクティビティ関数です。

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor オーケストレーター関数


**E3_Monitor** 関数は、オーケストレーター関数用の標準的な *function.json* を使います。

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

関数を実装するコードを次に示します。

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


このオーケストレーター関数は、次のアクションを行います。

1. 監視する "*リポジトリ*" と、SMS 通知の送信先の "*電話番号*" を取得します。
2. 監視の有効期限を決定します。 サンプルでは、簡略化のためにハード コーディングされた値を使います。
3. **E3_TooManyOpenIssues** を呼び出して、要求されたリポジトリで open の issue が多すぎるかどうかを判断します。
4. issue が多すぎる場合は、**E3_SendAlert** を呼び出して、要求された電話番号に SMS 通知を送信します。
5. 持続的タイマーを作成して、次のポーリング間隔でオーケストレーションを再開します。 サンプルでは、簡略化のためにハード コーディングされた値を使います。
6. 現在の UTC 時間がモニターの有効期限を経過するか SMS アラートが送信されるまで、実行を続けます。

オーケストレーター関数を複数回呼び出すことによって、複数のオーケストレーター インスタンスを同時に実行できます。 監視するリポジトリと、SMS アラートの送信先の電話番号を指定できます。 最後に、オーケストレーター関数は、タイマーの待機中は実行されて "*いない*" ため、その間は課金されることはありません。


### <a name="e3_toomanyopenissues-activity-function"></a>E3_TooManyOpenIssues アクティビティ関数

他のサンプルと同様に、ヘルパー アクティビティ関数は、`activityTrigger` トリガー バインドを使う標準的な関数です。 **E3_TooManyOpenIssues** 関数は、リポジトリで現在 open である issue の一覧を取得し、それらが "多すぎる" (サンプルでは 3 件以上) かどうかを判断します。


*function.json* の定義は次のようになります。

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

その実装を次に示します。

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>E3_SendAlert アクティビティ関数

**E3_SendAlert** 関数は、Twilio バインディングを使用して、解決待ちである open の issue が少なくとも 3 件あることをエンド ユーザーに通知する SMS メッセージを送信します。


その *function.json* は単純です。

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

SMS メッセージを送信するコードを次に示します。

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>サンプルを実行する

[GitHub](https://github.com/) アカウントが必要になります。 それを使用して、issue をオープンできる一時的なパブリック リポジトリを作成します。

サンプルに含まれる HTTP によってトリガーされる関数を使って、次の HTTP POST 要求を送信することによりオーケストレーションを開始できます。

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

たとえば、GitHub のユーザー名が `foo` で、リポジトリが `bar` である場合、`"repo"` の値は `"foo/bar"` にする必要があります。

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** インスタンスが開始され、open の issue について、指定されたリポジトリのクエリが実行されます。 open の issue が少なくとも 3 件発見された場合は、アラートを送信するアクティビティ関数が呼び出され、そうでない場合はタイマーが設定されます。 タイマーの期限が切れると、オーケストレーションが再開されます。

オーケストレーションのアクティビティは、Azure Functions ポータルで関数ログを調べることで確認できます。

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

タイムアウトになるか、open の issue が 3 件以上検出されると、オーケストレーションは完了します。 別の関数内で `terminate` API を使用することも、上の 202 応答で参照されている **terminatePostUri** HTTP POST Webhook を呼び出すこともできます。 Webhook を使用するには、`{text}` を早期終了の理由に置き換えます。 HTTP POST URL は概ね次のようになります。

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>次のステップ

このサンプルでは、Durable Functions で[持続的タイマー](durable-functions-timers.md)と条件付きロジックを使って外部ソースの状態を監視する方法を示しました。 次のサンプルでは、外部イベントと[持続的タイマー](durable-functions-timers.md)を使用してユーザーの操作を処理する方法について説明します。

> [!div class="nextstepaction"]
> [人との対話が含まれるサンプルを実行する](durable-functions-phone-verification.md)
