---
title: "Azure Functions におけるキュー ストレージ バインド | Microsoft Docs"
description: "Azure Functions で Azure Storage のトリガーとバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.openlocfilehash: 8f7227071edc519e588d55a9f06b77aeaf31aa37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Azure Functions における Queue Storage バインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Queue ストレージ バインドを構成したり、コーディングしたりする方法について説明します。 Azure Functions は、Azure キューのトリガーおよび出力のバインドをサポートしています。 すべてのバインドで使用できる機能については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Queue ストレージ トリガー
Azure Queue ストレージ トリガーを使用すると、新しいメッセージのキュー ストレージを監視し、それに対応できます。 

Functions ポータルの [**統合**] タブを使用して、キュー トリガーを定義します。 ポータルは、*function.json* の**バインド** セクションに次の定義を作成します。

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* `connection` プロパティには、ストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントを選択すると、[**統合**] タブの標準エディターによってこのアプリ設定が構成されます。

その他の設定は [host.json ファイル](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)で指定でき、ストレージ キュー トリガーをさらに微調整することができます。 たとえば、host.json でキューのポーリング間隔を変更できます。

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>キュー トリガーの使用
Node.js 関数では、`context.bindings.<name>` を使用してキュー データにアクセスします。


.NET 関数では、`CloudQueueMessage paramName` のようなメソッド パラメーターを使用してキュー ペイロードにアクセスします。 ここでは、`paramName` は[トリガー構成](#trigger)で指定した値です。 キュー メッセージを、次のいずれかの型に逆シリアル化できます。

* POCO オブジェクト。 キュー ペイロードが JSON オブジェクトである場合に使用します。 Functions ランタイムは、ペイロードを POCO オブジェクトに逆シリアル化します。 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>キュー トリガー メタデータ
キュー トリガーは、いくつかのメタデータ プロパティを提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 値は、[`CloudQueueMessage`] と同じセマンティクスを持ちます。

* **QueueTrigger** - キュー ペイロード (有効な文字列の場合)
* **DequeueCount** - `int` 型。 このメッセージがデキューされた回数。
* **ExpirationTime** - `DateTimeOffset?` 型。 メッセージが期限切れになる時刻。
* **Id** - `string` 型。 キュー メッセージ ID。
* **InsertionTime** - `DateTimeOffset?` 型。 メッセージがキューに追加された時刻。
* **NextVisibleTime** - `DateTimeOffset?` 型。 メッセージが次に表示される時刻。
* **PopReceipt** - `string` 型。 メッセージのポップ受信。

キュー メタデータの使用方法については、「[トリガー サンプル](#triggersample)」を参照してください。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>トリガー サンプル
キュー トリガーを定義する、次の function.json があるとします。

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

キュー メタデータを取得し、記録する言語固有のサンプルを参照してください。

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C# でのトリガー サンプル #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js でのトリガー サンプル

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>有害キュー メッセージの処理
キュー トリガー関数が失敗すると、Azure Functions は、その関数を特定のキュー メッセージに対して (最初の試行を含め) 最大 5 回再試行します。 試行が 5 回とも失敗した場合、Functions ランタイム は、*&lt;originalqueuename>-poison* という名前のキュー ストレージにメッセージを追加します。 メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。 

有害メッセージを手動で処理するには、キュー メッセージの `dequeueCount` を確認します (「[キュー トリガー メタデータ](#meta)」を参照)。

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>キュー ストレージの出力バインド
Azure キュー ストレージの出力バインドにより、キューにメッセージを書き込むことができます。 

Functions ポータルの [**統合**] タブを使用して、キュー 出力バインドを定義します。 ポータルは、*function.json* の**バインド** セクションに次の定義を作成します。

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* `connection` プロパティには、ストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントを選択すると、[**統合**] タブの標準エディターによってこのアプリ設定が構成されます。

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>キュー出力バインドの使用
Node.js 関数の場合、`context.bindings.<name>` を使用して出力キューにアクセスします。

.NET 関数の場合は、次のいずれかの型に出力できます。 `T` 型パラメーターがあるときは、`T` は `string` または POCO などのサポートされている出力タイプである必要があります。

* `out T` (JSON としてシリアル化されています)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

メソッドの戻り値の型を出力バインドとして使用することもできます。

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>キュー出力サンプル
次の *function.json* は、キュー出力バインドを持つ HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

受信 HTTP ペイロードを含むキュー メッセージを出力する言語固有のサンプルを参照してください。

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>C# でのキュー出力サンプル #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

複数のメッセージを送信する場合は、`ICollector` を使用します。

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Node.js でのキュー出力サンプル

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

複数のメッセージを送信する場合は、次のようになります。

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>次のステップ

キュー ストレージのトリガーとバインドを使用する関数の例については、「[他の Azure サービスに接続する関数を Azure Function を使用して作成する](functions-create-an-azure-connected-function.md)」を参照してください。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

[`CloudQueueMessage`]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
