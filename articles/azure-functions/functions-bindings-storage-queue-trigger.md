---
title: Azure Functions の Azure Queue storage トリガー
description: Azure Queue storage のデータ変更時に Azure 関数を実行する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: f4477a09f151695b826d0becf28e92ceaf3f9e85
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453208"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure Functions の Azure Queue storage トリガー

Queue storage トリガーは、メッセージが Azure Queue storage に追加されると関数を実行します。

## <a name="encoding"></a>エンコード

Azure Functions で想定されているのは *base64* でエンコードされた文字列です。 (データを *base64* でエンコードされた文字列として準備するために) エンコードの種類を調整する場合、それらはすべて呼び出し元のサービスに実装する必要があります。

## <a name="example"></a>例

キュー トリガーを使用して、キューで新しい項目を受け取ったときに関数を開始します。 キュー メッセージは、関数への入力として提供されます。

# <a name="c"></a>[C#](#tab/csharp)

次の例は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルのキュー トリガー バインディングと、バインディングを使用する [C# スクリプト (.csx)](functions-reference-csharp.md) コードを示しています。 この関数は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込みます。

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
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
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

function.json の `name` プロパティで名前が指定された `myQueueItem` については、「[使用方法](#usage)」セクションを参照してください。  ここに表示されているその他すべての変数については、「[メッセージのメタデータ](#message-metadata)」セクションを参照してください。

# <a name="java"></a>[Java](#tab/java)

次の Java の例は、キュー `myqueuename` に格納されるトリガーされたメッセージを記録するストレージ キュー トリガー関数を示しています。

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルのキュー トリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込みます。

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

> [!NOTE]
> name パラメーターは、キュー項目ペイロードを含む JavaScript コードの `context.bindings.<name>` として反映されます。 このペイロードは、関数に対する 2 番目のパラメーターとしても渡されます。

JavaScript コードを次に示します。

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

function.json の `name` プロパティで名前が指定された `myQueueItem` については、「[使用方法](#usage)」セクションを参照してください。  ここに表示されているその他すべての変数については、「[メッセージのメタデータ](#message-metadata)」セクションを参照してください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例では、トリガーを使用してキュー メッセージを読み取って関数に渡す方法を示します。

ストレージ キュー トリガーは *function.json* ファイルで定義され、そこで `type` は `queueTrigger` に設定されます。

```json
{
  "bindings": [
    {
      "name": "QueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

*Run.ps1* ファイルのコードによってパラメーターが `$QueueItem` として宣言され、関数でキュー メッセージを読み取ることができるようになります。

```powershell
# Input bindings are passed in via param block.
param([string] $QueueItem, $TriggerMetadata)

# Write out the queue message and metadata to the information log.
Write-Host "PowerShell queue trigger function processed work item: $QueueItem"
Write-Host "Queue item expiration time: $($TriggerMetadata.ExpirationTime)"
Write-Host "Queue item insertion time: $($TriggerMetadata.InsertionTime)"
Write-Host "Queue item next visible time: $($TriggerMetadata.NextVisibleTime)"
Write-Host "ID: $($TriggerMetadata.Id)"
Write-Host "Pop receipt: $($TriggerMetadata.PopReceipt)"
Write-Host "Dequeue count: $($TriggerMetadata.DequeueCount)"
```

# <a name="python"></a>[Python](#tab/python)

次の例では、トリガーを使用してキュー メッセージを読み取って関数に渡す方法を示します。

ストレージ キュー トリガーは *function.json* で定義され、そこで *type* は `queueTrigger` に設定されます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

*_\_init_\_.py* のコードによってパラメーターが `func.QueueMessage` として宣言され、関数でキュー メッセージを読み取ることができるようになります。

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

 ---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、以下の属性を使用してキュー トリガーを構成します。

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  次の例のように、属性のコンストラクターは監視するキューの名前を受け取ります。

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウント接続ストリングを含むアプリ設定を指定できます。

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  完全な例については、「[例](#example)」を参照してください。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  使用するストレージ アカウントを指定する別の方法を提供します。 コンストラクターは、ストレージ接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

使用するストレージ アカウントは、次の順序で決定されます。

* `QueueTrigger` 属性の `Connection` プロパティ。
* `QueueTrigger` 属性と同じパラメーターに適用された `StorageAccount` 属性。
* 関数に適用される `StorageAccount` 属性。
* クラスに適用される `StorageAccount` 属性。
* "AzureWebJobsStorage" アプリ設定。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`QueueTrigger` 注釈を使用すると、関数をトリガーするキューにアクセスできます。 次の例では、`message` パラメーターを使用して、キュー メッセージを関数で使用できるようにします。

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| プロパティ    | 説明 |
|-------------|-----------------------------|
|`name`       | 関数シグネチャのパラメーター名を宣言します。 関数がトリガーされると、このパラメーターの値にはキュー メッセージの内容が含められます。 |
|`queueName`  | ストレージ アカウントのキュー名を宣言します。 |
|`connection` | ストレージ アカウントの接続文字列を示します。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `QueueTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし| `queueTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction**| 該当なし | *function.json* ファイルの場合のみ。 `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし |関数コードでキュー項目ペイロードを含む変数の名前。  |
|**queueName** | **QueueName**| ポーリングするキューの名前。 |
|**connection** | **Connection** |このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。<br><br>たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "MyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列の代わりに[バージョン 5.x またはそれ以降の拡張機能](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)を使用している場合は、接続を定義する構成セクションへの参照を指定できます。 「[接続](./functions-reference.md#connections)」を参照してください。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Default

`string paramName` のようなメソッド パラメーターを使用してメッセージ データにアクセスします。 次の型のいずれにでもバインドできます。

* オブジェクト - Functions ランタイムは、JSON ペイロードを、コードで定義されている任意のクラスのインスタンスに逆シリアル化します。
* `string`
* `byte[]`
* [CloudQueueMessage]

`CloudQueueMessage` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

### <a name="additional-types"></a>その他の型

[Storage 拡張機能の 5.0.0 またはそれ以降のバージョン](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)を使用するアプリでは、[Azure SDK for .NET](/dotnet/api/overview/azure/storage.queues-readme) の型を使用することもできます。 このバージョンでは、次の型を優先して、レガシ `CloudQueueMessage` 型のサポートがなくなります。

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
 
これらの型の使用例については、[拡張機能の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)に関するページを参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

### <a name="default"></a>Default

`string paramName` のようなメソッド パラメーターを使用してメッセージ データにアクセスします。 `paramName` は *function.json* の `name` プロパティで指定された値です。 次の型のいずれにでもバインドできます。

* オブジェクト - Functions ランタイムは、JSON ペイロードを、コードで定義されている任意のクラスのインスタンスに逆シリアル化します。 
* `string`
* `byte[]`
* [CloudQueueMessage]

`CloudQueueMessage` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

### <a name="additional-types"></a>その他の型

[Storage 拡張機能の 5.0.0 またはそれ以降のバージョン](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)を使用するアプリでは、[Azure SDK for .NET](/dotnet/api/overview/azure/storage.queues-readme) の型を使用することもできます。 このバージョンでは、次の型を優先して、レガシ `CloudQueueMessage` 型のサポートがなくなります。

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)

これらの型の使用例については、[拡張機能の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)に関するページを参照してください。

# <a name="java"></a>[Java](#tab/java)

[QueueTrigger](/java/api/com.microsoft.azure.functions.annotation.queuetrigger) 注釈を使用すると、関数をトリガーしたキュー メッセージにアクセスできます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

キュー項目ペイロードは、`context.bindings.<NAME>` を介して使用できます。ここで、`<NAME>` は *function.json* で定義されている名前と一致します。 ペイロードが JSON の場合、値はオブジェクトに逆シリアル化されます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

*function.json* ファイルのバインドの `name` パラメーターで指定された名前と一致する文字列パラメーターを使用して、キュー メッセージにアクセスします。

# <a name="python"></a>[Python](#tab/python)

[QueueMessage](/python/api/azure-functions/azure.functions.queuemessage) として型指定されたパラメーターを使用して、キュー メッセージにアクセスします。

---

## <a name="message-metadata"></a>メッセージのメタデータ

キュー トリガーは、いくつかの[メタデータ プロパティ](./functions-bindings-expressions-patterns.md#trigger-metadata)を提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらのプロパティは、[CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) クラスのメンバーです。

|プロパティ|種類|説明|
|--------|----|-----------|
|`QueueTrigger`|`string`|キュー ペイロード (有効な文字列の場合)。 キュー メッセージ ペイロードが文字列の場合、`QueueTrigger` は、*function.json* の `name` プロパティで指定された変数と同じ値になります。|
|`DequeueCount`|`int`|このメッセージがデキューされた回数。|
|`ExpirationTime`|`DateTimeOffset`|メッセージが期限切れになる時刻。|
|`Id`|`string`|キュー メッセージ ID。|
|`InsertionTime`|`DateTimeOffset`|メッセージがキューに追加された時刻。|
|`NextVisibleTime`|`DateTimeOffset`|メッセージが次に表示される時刻。|
|`PopReceipt`|`string`|メッセージのポップ受信。|

## <a name="poison-messages"></a>有害メッセージ

キュー トリガー関数が失敗すると、Azure Functions は、その関数を特定のキュー メッセージに対して (最初の試行を含め) 最大 5 回再試行します。 試行が 5 回とも失敗した場合、Functions ランタイム は、 *&lt;originalqueuename>-poison* という名前のキューにメッセージを追加します。 メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。

有害メッセージを手動で処理するには、キュー メッセージの [dequeueCount](#message-metadata) を確認します。

## <a name="polling-algorithm"></a>ポーリング アルゴリズム

キュー トリガーは、アイドル状態のキューのポーリングがストレージ トランザクション コストに与える影響を軽減するために、ランダムな指数バックオフ アルゴリズムを実装します。

アルゴリズムでは次のロジックが使用されます。

- メッセージが見つかると、ランタイムは 2 秒間待機してから、別のメッセージを確認します
- メッセージが見つからない場合は、約 4 秒間待機してから再試行します。
- 再試行後もキュー メッセージが取得できなかった場合、待ち時間が最大になるまで再試行が続けられます。既定の最大待ち時間は 1 分間です。
- 最大待ち時間は、[host.json ファイル](functions-host-json-v1.md#queues)内の `maxPollingInterval` プロパティで構成できます。

ローカル開発の場合、最大ポーリング間隔は既定で 2 秒に設定されます。

課金に関しては、ランタイムによるポーリングに費やされた時間は "無料" であり、お使いのアカウントに対してカウントされません。

## <a name="concurrency"></a>コンカレンシー

待機中のキュー メッセージが複数存在する場合、キュー トリガーはメッセージのバッチを取得し、関数インスタンスを同時に呼び出してそれらを処理します。 既定では、このバッチ サイズは 16 です。 処理されている数が 8 まで減少すると、このランタイムは別のバッチを取得し、それらのメッセージの処理を開始します。 そのため、1 つの仮想マシン (VM) 上で 1 関数あたりに処理されている同時実行メッセージの最大数は 24 です。 この制限は、各 VM 上のキューによってトリガーされる各関数に個別に適用されます。 関数アプリが複数の VM にスケールアウトした場合、各 VM はトリガーを待機し、関数を実行しようとします。 たとえば、関数アプリが 3 つの VM にスケールアウトした場合、キューによってトリガーされる 1 つの関数の同時実行インスタンスの既定の最大数は 72 です。

新しいバッチを取得するためのバッチ サイズとしきい値は、[host.json ファイル](functions-host-json.md#queues)で構成できます。 関数アプリ内のキューによってトリガーされる関数の並列実行を最小限に抑えたい場合は、このバッチ サイズを 1 に設定できます。 この設定によってコンカレンシーが解消されるのは、関数アプリが 1 つの仮想マシン (VM) 上で実行される場合に限ります。 

キュー トリガーは、関数がキュー メッセージを複数回同時に処理することを自動的に防止します。

## <a name="hostjson-properties"></a>host.json プロパティ

[host.json](functions-host-json.md#queues) ファイルには、キュー トリガーの動作を制御する設定が含まれています。 使用可能な設定の詳細については、「[host.json 設定](functions-bindings-storage-queue.md#hostjson-settings)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [キュー ストレージ メッセージを書き込む (出力バインド)](./functions-bindings-storage-queue-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
