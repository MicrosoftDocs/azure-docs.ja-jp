---
title: Durable Functions の HTTP API - Azure Functions
description: Azure Functions の Durable Functions 拡張機能で HTTP API を実装する方法を説明します。
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697845"
---
# <a name="http-api-reference"></a>HTTP API リファレンス

Durable Functions 拡張機能では、[オーケストレーション](durable-functions-types-features-overview.md#orchestrator-functions)、 [エンティティ](durable-functions-types-features-overview.md#entity-functions)、および[タスク ハブ](durable-functions-task-hubs.md)で管理タスクを実行するために使用できる一連の組み込み HTTP API が公開されています。 これらの HTTP API は、Azure Functions ホストによって承認されているが Durable Functions 拡張機能によって直接処理される拡張性 webhook です。

拡張機能によって実装されるすべての HTTP API には、次のパラメーターが必要です。 すべてのパラメーターのデータ型は `string` です。

| パラメーター        | パラメーターの型  | 説明 |
|------------------|-----------------|-------------|
| **`taskHub`**    | クエリ文字列    | [タスク ハブ](durable-functions-task-hubs.md)の名前。 指定しない場合は、現在の関数アプリのタスク ハブの名前が想定されます。 |
| **`connection`** | クエリ文字列    | ストレージ アカウントの接続文字列の **名前**。 指定しない場合は、関数アプリの既定の接続文字列が想定されます。 |
| **`systemKey`**  | クエリ文字列    | API の呼び出しに必要な承認キー。 |

`systemKey` は、Azure Functions ホストによって自動生成される承認キーです。 このキーにより、Durable Task 拡張機能 API へのアクセスが特別に許可されます。また、このキーは[他の承認キー](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)と同じ方法で管理できます。 .NET の `CreateCheckStatusResponse` や `CreateHttpManagementPayload` API、または JavaScript の `createCheckStatusResponse` や `createHttpManagementPayload` API などの[オーケストレーション クライアント バインド](durable-functions-bindings.md#orchestration-client) API を使用して、正しい `taskHub`、`connection`、`systemKey` クエリ文字列値を含む URL を生成できます。

以降のセクションで、この拡張機能でサポートされる特定の HTTP API とその使用方法の例について説明します。

## <a name="start-orchestration"></a>オーケストレーションの開始

指定されたオーケストレーター関数の新しいインスタンスの実行を開始します。

### <a name="request"></a>要求

Functions ランタイム バージョン 1.x の場合、要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Functions ランタイム バージョン 2.x の URL 形式は、パラメーターがすべて同じですが、プレフィックスが若干異なります。

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド              | パラメーターのタイプ  | 説明 |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | 開始するオーケストレーター関数の名前。 |
| **`instanceId`**   | URL             | 省略可能なパラメーター。 オーケストレーション インスタンスの ID。 指定しない場合、オーケストレーター関数はランダムなインスタンス ID で開始します。 |
| **`{content}`**    | 要求内容 | 省略可能。 JSON 形式のオーケストレーター関数入力。 |

### <a name="response"></a>Response

返される可能性がある状態コード値は、いくつかあります。

* **HTTP 202 (Accepted)** : 指定されたオーケストレーター関数は実行を開始するようにスケジュールされました。 `Location` 応答ヘッダーには、オーケストレーションの状態をポーリングするための URL が含まれています。
* **HTTP 400 (Bad request)** : 指定されたオーケストレーター関数が存在しないか、指定されたインスタンス ID が無効だったか、要求コンテンツが有効な JSON ではありませんでした。

`RestartVMs` オーケストレーター関数を開始し、JSON オブジェクトペイロードを含む要求の例を次に示します。

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

**HTTP 202** の場合の応答ペイロードは、次のフィールドを持つ JSON オブジェクトです。

| フィールド                       | 説明                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |オーケストレーション インスタンスの ID。 |
| **`statusQueryGetUri`**     |オーケストレーション インスタンスの状態の URL。 |
| **`sendEventPostUri`**      |オーケストレーション インスタンスの "イベント発生" URL。 |
| **`terminatePostUri`**      |オーケストレーション インスタンスの "終了" URL。 |
| **`purgeHistoryDeleteUri`** |オーケストレーション インスタンスの "消去履歴" URL。 |
| **`rewindPostUri`**         |(プレビュー) オーケストレーション インスタンスの "巻き戻し" URL。 |

すべてのフィールドのデータ型は `string` です。

`abc123` を ID として持つオーケストレーション インスタンスの応答ペイロードの例を次に示します (読みやすいように書式設定されています)。

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

HTTP 応答は、*ポーリング コンシューマー パターン* と互換性があるように意図されています。 また、次の重要な応答ヘッダーも含まれています。

* **Location**: 状態エンドポイントの URL。 この URL には、`statusQueryGetUri` フィールドと同じ値が含まれます。
* **Retry-After**: ポーリング操作の間に待機する秒数。 既定値は `10` です。

非同期 HTTP ポーリング パターンの詳細については、[HTTP 非同期操作の追跡](durable-functions-http-features.md#async-operation-tracking)に関するドキュメントをご覧ください。

## <a name="get-instance-status"></a>インスタンスの状態を取得する

指定されたオーケストレーション インスタンスの状態を取得します。

### <a name="request"></a>要求

Functions ランタイム バージョン 1.x の場合、要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Functions ランタイム バージョン 2.x の URL 形式は、パラメーターがすべて同じですが、プレフィックスが若干異なります。

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド                   | パラメーターのタイプ  | 説明 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | オーケストレーション インスタンスの ID。 |
| **`showInput`**         | クエリ文字列    | 省略可能なパラメーター。 `false` に設定した場合、関数の入力は応答ペイロードに含まれなくなります。|
| **`showHistory`**       | クエリ文字列    | 省略可能なパラメーター。 `true` に設定した場合、オーケストレーションの実行履歴が応答ペイロードに含まれます。|
| **`showHistoryOutput`** | クエリ文字列    | 省略可能なパラメーター。 `true` に設定した場合、関数の出力はオーケストレーションの実行履歴に含まれます。|
| **`createdTimeFrom`**   | クエリ文字列    | 省略可能なパラメーター。 指定した場合、返されるインスタンスの一覧が特定の ISO8601 タイムスタンプの時刻以降に作成されたインスタンスにフィルター処理されます。|
| **`createdTimeTo`**     | クエリ文字列    | 省略可能なパラメーター。 指定した場合、返されるインスタンスの一覧が特定の ISO8601 タイムスタンプの時刻以前に作成されたインスタンスにフィルター処理されます。|
| **`runtimeStatus`**     | クエリ文字列    | 省略可能なパラメーター。 指定した場合、返されるインスタンスの一覧がランタイム状態に基づいてフィルター処理されます。 考えられるランタイム状態の値の一覧を確認するには、[インスタンスのクエリの実行](durable-functions-instance-management.md)に関する記事をご覧ください。 |

### <a name="response"></a>Response

返される可能性がある状態コード値は、いくつかあります。

* **HTTP 200 (OK)**: 指定されたインスタンスが完了状態。
* **HTTP 202 (Accepted)**: 指定されたインスタンスが処理中。
* **HTTP 400 (Bad Request)**: 指定されたインスタンスが失敗したか終了した。
* **HTTP 404 (Not Found)**: 指定されたインスタンスが存在しないか実行開始されていない。
* **HTTP 500 (Internal Server Error)**: 指定されたインスタンスがハンドルされない例外で失敗した。

**HTTP 200** と **HTTP 202** の場合の応答ペイロードは、次のフィールドを持つ JSON オブジェクトです。

| フィールド                 | データ型 | 説明 |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | string    | インスタンスの実行時状態。 値には、*Running*、*Pending*、*Failed*、*Canceled*、*Terminated*、*Completed* があります。 |
| **`input`**           | JSON      | インスタンスを初期化するために使用される JSON データ。 このフィールドは、`showInput` クエリ文字列パラメーターが `false`に設定されている場合は、`null` です。|
| **`customStatus`**    | JSON      | カスタム オーケストレーションの状態に使用された JSON データ。 セットされていない場合、このフィールドは`null`です。 |
| **`output`**          | JSON      | インスタンスの JSON の出力。 インスタンスが完了状態でない場合、このフィールドは `null` です。 |
| **`createdTime`**     | string    | インスタンスが作成された時刻。 ISO 8601 の拡張された表記を使用します。 |
| **`lastUpdatedTime`** | string    | インスタンスが最後に保持されていた時刻。 ISO 8601 の拡張された表記を使用します。 |
| **`historyEvents`**   | JSON      | オーケストレーションの実行履歴を含む JSON 配列。 このフィールドは、`showHistory` クエリ文字列パラメーターが `true`に設定されていない限り、`null` です。 |

オーケストレーションの実行履歴とアクティビティの出力を含む応答ペイロードの例を次に示します (読みやすい形式になっています)。

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** の応答には、前述の `statusQueryGetUri` フィールドと同じ URL を参照する **Location** 応答ヘッダーも含まれています。

## <a name="get-all-instances-status"></a>すべてのインスタンス ステータスを取得する

'インスタンスの状態を取得する' 要求から `instanceId` を削除して、すべてのインスタンスの状態のクエリを実行することもできます。 この場合、基本のパラメーターは 'インスタンスの状態を取得する' と同じです。 フィルター処理用のクエリ文字列パラメーターもサポートされています。

覚えておくべきことの 1 つは、`connection` と `code` が省略可能であることです。 関数に対する匿名認証がある場合、`code` は必要ありません。
AzureWebJobsStorage アプリ設定で定義されているのとは異なるストレージの接続文字列を使用しない場合は、接続クエリ文字列パラメーターを無視してかまいません。

### <a name="request"></a>要求

Functions ランタイム バージョン 1.x の場合、要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Functions ランタイム バージョン 2.x の URL 形式は、パラメーターがすべて同じですが、プレフィックスが若干異なります。

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド                   | パラメーターのタイプ  | 説明 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | オーケストレーション インスタンスの ID。 |
| **`showInput`**         | クエリ文字列    | 省略可能なパラメーター。 `false` に設定した場合、関数の入力は応答ペイロードに含まれなくなります。|
| **`showHistory`**       | クエリ文字列    | 省略可能なパラメーター。 `true` に設定した場合、オーケストレーションの実行履歴が応答ペイロードに含まれます。|
| **`showHistoryOutput`** | クエリ文字列    | 省略可能なパラメーター。 `true` に設定した場合、関数の出力はオーケストレーションの実行履歴に含まれます。|
| **`createdTimeFrom`**   | クエリ文字列    | 省略可能なパラメーター。 指定した場合、返されるインスタンスの一覧が特定の ISO8601 タイムスタンプの時刻以降に作成されたインスタンスにフィルター処理されます。|
| **`createdTimeTo`**     | クエリ文字列    | 省略可能なパラメーター。 指定した場合、返されるインスタンスの一覧が特定の ISO8601 タイムスタンプの時刻以前に作成されたインスタンスにフィルター処理されます。|
| **`runtimeStatus`**     | クエリ文字列    | 省略可能なパラメーター。 指定した場合、返されるインスタンスの一覧がランタイム状態に基づいてフィルター処理されます。 考えられるランタイム状態の値の一覧を確認するには、[インスタンスのクエリの実行](durable-functions-instance-management.md)に関する記事をご覧ください。 |
| **`top`**               | クエリ文字列    | 省略可能なパラメーター。 指定した場合、クエリによって返されるインスタンス数が制限されます。 |

### <a name="response"></a>Response

次に、オーケストレーション ステータスを含む応答ペイロードの例を示します (読みやすくなるように、形式を整えています)。

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> この操作は、インスタンスのテーブルに多数の行がある場合、Azure Storage の I/O の観点から非常にコスト効率が悪くなることがあります。 インスタンス テーブルの詳細については、「[Durable Functions のパフォーマンスとスケーリング (Azure Functions)](durable-functions-perf-and-scale.md#instances-table)」のドキュメントを参照してください。
>

さらに結果が存在する場合、継続トークンが応答ヘッダーに返されます。  ヘッダーの名前は `x-ms-continuation-token` です。

次の要求ヘッダーで継続トークンの値を設定すると、結果の次のページを取得できます。 この要求ヘッダーの名前も `x-ms-continuation-token` です。

## <a name="purge-single-instance-history"></a>単一インスタンス履歴を消去する

指定したオーケストレーション インスタンスの履歴および関連する成果物を削除します。

### <a name="request"></a>要求

Functions ランタイム バージョン 1.x の場合、要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Functions ランタイム バージョン 2.x の URL 形式は、パラメーターがすべて同じですが、プレフィックスが若干異なります。

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド             | パラメーターのタイプ  | 説明 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | オーケストレーション インスタンスの ID。 |

### <a name="response"></a>Response

以下の HTTP 状態コード値が返される可能性があります。

* **HTTP 200 (OK)** : インスタンス履歴は正常に消去されました。
* **HTTP 404 (Not Found)** : 指定されたインスタンスは存在しません。

**HTTP 200** の場合の応答ペイロードは、次のフィールドを持つ JSON オブジェクトです。

| フィールド                  | データ型 | 説明 |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | 整数 (integer)   | 削除されたインスタンスの数。 単一インスタンスの場合、この値は常に `1` です。 |

応答ペイロードの例を次に示します (読みやすいように書式設定されています)。

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>複数インスタンス履歴を消去する

'単一インスタンス履歴を消去する' 要求から `{instanceId}` を削除することで、タスク ハブ内の複数インスタンスの履歴および関連する成果物を削除することもできます。 インスタンス履歴を選択して消去するには、'すべてのインスタンス ステータスを取得する' 要求で説明したものと同じフィルターを使用します。

### <a name="request"></a>要求

Functions ランタイム バージョン 1.x の場合、要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Functions ランタイム バージョン 2.x の URL 形式は、パラメーターがすべて同じですが、プレフィックスが若干異なります。

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド                 | パラメーターのタイプ  | 説明 |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | クエリ文字列    | 消去されるインスタンスの一覧が特定の ISO8601 タイムスタンプの時刻以降に作成されたインスタンスにフィルター処理されます。|
| **`createdTimeTo`**   | クエリ文字列    | 省略可能なパラメーター。 指定した場合、消去されるインスタンスの一覧が特定の ISO8601 タイムスタンプの時刻以前に作成されたインスタンスにフィルター処理されます。|
| **`runtimeStatus`**   | クエリ文字列    | 省略可能なパラメーター。 指定した場合、消去されるインスタンスの一覧がランタイム状態に基づいてフィルター処理されます。 考えられるランタイム状態の値の一覧を確認するには、[インスタンスのクエリの実行](durable-functions-instance-management.md)に関する記事をご覧ください。 |

> [!NOTE]
> この操作は、インスタンスや履歴のテーブルに多数の行がある場合、Azure Storage の I/O の観点から非常にコスト効率が悪くなることがあります。 これらのテーブルの詳細については、「[Durable Functions のパフォーマンスとスケーリング (Azure Functions)](durable-functions-perf-and-scale.md#instances-table)」のドキュメントを参照してください。

### <a name="response"></a>Response

以下の HTTP 状態コード値が返される可能性があります。

* **HTTP 200 (OK)** : インスタンス履歴は正常に消去されました。
* **HTTP 404 (Not Found)** : フィルター式に一致するインスタンスが見つかりませんでした。

**HTTP 200** の場合の応答ペイロードは、次のフィールドを持つ JSON オブジェクトです。

| フィールド                   | データ型 | 説明 |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | 整数 (integer)   | 削除されたインスタンスの数。 |

応答ペイロードの例を次に示します (読みやすいように書式設定されています)。

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>イベントを発生させる

実行中のオーケストレーション インスタンスにイベント通知メッセージを送信します。

### <a name="request"></a>要求

Functions ランタイム バージョン 1.x の場合、要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Functions ランタイム バージョン 2.x の URL 形式は、パラメーターがすべて同じですが、プレフィックスが若干異なります。

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド             | パラメーターのタイプ  | 説明 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | オーケストレーション インスタンスの ID。 |
| **`eventName`**   | URL             | ターゲット オーケストレーション インスタンスが待機しているイベントの名前。 |
| **`{content}`**   | 要求内容 | JSON 形式のイベント ペイロード。 |

### <a name="response"></a>Response

返される可能性がある状態コード値は、いくつかあります。

* **HTTP 202 (Accepted)**: 発生したイベントが受け入れられて処理された。
* **HTTP 400 (Bad request)**: 要求内容が `application/json` タイプまたは有効な JSON でなかった。
* **HTTP 404 (Not Found)**: 指定されたインスタンスが見つからなかった。
* **HTTP 410 (Gone)**: 指定されたインスタンスが完了または失敗し、発生したイベントを処理できない。

**operation** という名前のイベントを待機するインスタンスに JSON 文字列 `"incr"` を送信する要求の例を次に示します。

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

この API の応答には内容は含まれません。

## <a name="terminate-instance"></a>インスタンスを終了する

実行中のオーケストレーション インスタンスを終了します。

### <a name="request"></a>要求

Functions ランタイム バージョン 1.x の場合、要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Functions ランタイム バージョン 2.x の URL 形式は、パラメーターがすべて同じですが、プレフィックスが若干異なります。

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド             | パラメーターの型  | 説明 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | オーケストレーション インスタンスの ID。 |
| **`reason`**      | クエリ文字列    | 省略可能。 オーケストレーション インスタンスの終了の理由。 |

### <a name="response"></a>Response

返される可能性がある状態コード値は、いくつかあります。

* **HTTP 202 (Accepted)**: 終了要求が受け入れられて処理された。
* **HTTP 404 (Not Found)**: 指定されたインスタンスが見つからなかった。
* **HTTP 410 (Gone)**: 指定されたインスタンスが完了したか失敗した。

実行中のインスタンスを終了させ、**バグ** の理由を示す要求の例を次に示します。

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

この API の応答には内容は含まれません。

## <a name="rewind-instance-preview"></a>rewind インスタンス (プレビュー)

最後に失敗した操作を再実行することにより、失敗したオーケストレーション インスタンスを実行状態に復元します。

### <a name="request"></a>要求

Functions ランタイム バージョン 1.x の場合、要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Functions ランタイム バージョン 2.x の URL 形式は、パラメーターがすべて同じですが、プレフィックスが若干異なります。

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド             | パラメーターの型  | 説明 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | オーケストレーション インスタンスの ID。 |
| **`reason`**      | クエリ文字列    | 省略可能。 オーケストレーション インスタンスを rewind する理由。 |

### <a name="response"></a>Response

返される可能性がある状態コード値は、いくつかあります。

* **HTTP 202 (Accepted)**: 巻き戻し要求が受け入れられて処理された。
* **HTTP 404 (Not Found)**: 指定されたインスタンスが見つからなかった。
* **HTTP 410 (Gone)**: 指定されたインスタンスが完了したか終了した。

失敗したインスタンスを rewind し、**修正** の理由を指定する要求の例を次に示します。

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

この API の応答には内容は含まれません。

## <a name="signal-entity"></a>エンティティへのシグナル通知

一方向の操作メッセージを [Durable Entity](durable-functions-types-features-overview.md#entity-functions) に送信します。 エンティティが存在しない場合、自動的に作成されます。

> [!NOTE]
> 持続エンティティは、Durable Functions 2.0 以降で使用できます。

### <a name="request"></a>要求

HTTP 要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド             | パラメーターのタイプ  | 説明 |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | エンティティの名前 (型)。 |
| **`entityKey`**   | URL             | エンティティのキー (一意の ID)。 |
| **`op`**          | クエリ文字列    | 省略可能。 呼び出すユーザー定義操作の名前。 |
| **`{content}`**   | 要求内容 | JSON 形式のイベント ペイロード。 |

次に示すのは、ユーザー定義の "Add" メッセージを、`steps` という名前の `Counter` エンティティに送信する要求の例です。 メッセージの内容は値 `5` です。 エンティティがまだ存在しない場合、次の要求によって作成されます。

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> [.NET のクラスベースのエンティティ](durable-functions-dotnet-entities.md#defining-entity-classes) の既定では、`delete` の `op` 値を指定すると、エンティティの状態が削除されます。 ただし、エンティティで `delete` という名前の操作が定義されている場合、そのユーザー定義の操作が代わりに呼び出されます。

### <a name="response"></a>Response

この操作には、複数の応答の可能性があります。

* **HTTP 202 (Accepted)** : 非同期処理のためにシグナル操作が受理されました。
* **HTTP 400 (Bad request)** : 要求内容が `application/json` タイプまたは有効な JSON でなかったか、`entityKey` の値が無効でした。
* **HTTP 404 (Not Found)** : 指定された `entityName` が見つかりませんでした。

成功した HTTP 要求の応答には内容は含まれません。 失敗した HTTP 要求は、応答の内容に JSON 形式のエラー情報が含まれている場合があります。

## <a name="get-entity"></a>エンティティの取得

指定されたエンティティの状態を取得します。

### <a name="request"></a>要求

HTTP 要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Response

この操作には、2 つの応答の可能性があります。

* **HTTP 200 (OK)** : 指定されたエンティティが存在します。
* **HTTP 404 (Not Found)** : 指定されたエンティティが見つかりませんでした。

成功した応答は、JSON でシリアル化されたエンティティの状態がその内容に含まれます。

### <a name="example"></a>例
次の例の HTTP 要求では、`steps` という名前の既存の `Counter` エンティティの状態を取得します。

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

`currentValue` フィールドに保存したステップ数しか `Counter` エンティティに含まれていなかった場合、応答の内容は次のようになります (読みやすいように整形しています)。

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>エンティティの一覧表示

複数のエンティティを照会するには、エンティティ名または最後の操作日を使用します。

### <a name="request"></a>要求

HTTP 要求は次のような形式です (わかりやすくするために複数行が示されています)。

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド                       | パラメーターのタイプ  | 説明 |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | 省略可能。 指定した場合、返されるエンティティの一覧をエンティティ名でフィルター処理します (大文字と小文字は区別されません)。 |
| **`fetchState`**            | クエリ文字列    | 省略可能なパラメーター。 `true` に設定すると、エンティティの状態が応答ペイロードに含まれます。 |
| **`lastOperationTimeFrom`** | クエリ文字列    | 省略可能なパラメーター。 指定した場合、返されるエンティティの一覧が特定の ISO8601 タイムスタンプの時刻以降に操作を実行したエンティティにフィルター処理されます。 |
| **`lastOperationTimeTo`**   | クエリ文字列    | 省略可能なパラメーター。 指定した場合、返されるエンティティの一覧が特定の ISO8601 タイムスタンプの時刻以前に操作を実行したエンティティにフィルター処理されます。 |
| **`top`**                   | クエリ文字列    | 省略可能なパラメーター。 指定した場合、クエリによって返されるエンティティ数が制限されます。 |


### <a name="response"></a>Response

成功した HTTP 200 応答には、JSON でシリアル化されたエンティティの配列と、必要に応じて各エンティティの状態が含まれます。

既定では、操作はクエリ条件に一致する最初の 100 エンティティを返します。 呼び出し元は、`top` のクエリ文字列パラメーター値を指定して、異なる結果の最大数を返すことができます。 返される結果を超える結果が存在する場合、継続トークンも応答ヘッダーに返されます。 ヘッダーの名前は `x-ms-continuation-token` です。

次の要求ヘッダーで継続トークンの値を設定すると、結果の次のページを取得できます。 この要求ヘッダーの名前も `x-ms-continuation-token` です。

### <a name="example---list-all-entities"></a>例 - すべてのエンティティを一覧表示する

次の HTTP 要求の例では、タスク ハブ内のすべてのエンティティが一覧表示されます。

```http
GET /runtime/webhooks/durabletask/entities
```

応答 JSON は次のようになります (読みやすいように書式設定されています)。

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>例 - エンティティの一覧のフィルター処理

次の HTTP 要求の例では、`counter` 型の最初の 2 つのエンティティのみを一覧表示し、その状態も取得します。

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

応答 JSON は次のようになります (読みやすいように書式設定されています)。

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Application Insights を使用して Durable Functions を監視する方法を確認する](durable-functions-diagnostics.md)