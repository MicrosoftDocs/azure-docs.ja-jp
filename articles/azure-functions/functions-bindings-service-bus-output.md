---
title: Azure Functions における Azure Service Bus の出力バインド
description: Azure Functions から Azure Service Bus メッセージを送信する方法について説明します。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ae2be8dbcb4839c7d16b864c484c3360fdcfe324
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425585"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Functions における Azure Service Bus の出力バインド

キューまたはトピック メッセージを送信するには、Azure Service Bus 出力バインドを使用します。

セットアップと構成の詳細については、[概要](functions-bindings-service-bus.md)に関する記事を参照してください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、Service Bus キュー メッセージを送信する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの Service Bus 出力バインドと、そのバインドを使用する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、タイマー トリガーを使用して、15 秒ごとにキュー メッセージを送信します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

単一のメッセージを作成する C# スクリプト コードを次に示します。

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

複数のメッセージを作成する C# スクリプト コードを次に示します。

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

次の例は、HTTP 要求によってトリガーされたときに Service Bus キュー `myqueue` にメッセージを送信する Java 関数を示しています。

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 [Java 関数ランタイム ライブラリ ](/java/api/overview/azure/functions/runtime) で、その値が Service Bus キューに書き込まれる関数のパラメーターに関する `@QueueOutput` 注釈を使用します。  パラメーターの型は `OutputBinding<T>` にする必要があります。T は POJO の Java の任意のネイティブ型です。

Java 関数は、Service Bus トピックにも書き込むことができます。 次の例では、出力バインディングの構成を記述する`@ServiceBusTopicOutput`注釈を使用します。 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの Service Bus 出力バインドと、そのバインドを使用する [JavaScript スクリプト関数](functions-reference-node.md)を示しています。 この関数は、タイマー トリガーを使用して、15 秒ごとにキュー メッセージを送信します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

単一のメッセージを作成する JavaScript スクリプト コードを次に示します。

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

複数のメッセージを作成する JavaScript スクリプト コードを次に示します。

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、*function.json* ファイル内の Service Bus 出力バインディングと、そのバインディングを使用する [PowerShell 関数](functions-reference-powershell.md)を示しています。 

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

関数の出力としてメッセージを作成する PowerShell を次に示します。

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

次の例では、Python で Service Bus キューに書き出す方法を示します。

Service Bus のバインディング定義は *function.json* で定義され、そこで *type* は `serviceBus` に設定されます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

*_\_init_\_.py* で、値を `set` メソッドに渡すことでメッセージをキューに書き出すことができます。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs) 属性を使用します。

属性のコンストラクターは、キューの名前、またはトピックとサブスクリプションを受け取ります。 接続のアクセス権を指定することもできます。 アクセス権の設定を選択する方法については、「[出力 - 構成](#configuration)」セクションで説明されています。 関数の戻り値に適用される属性に適用される属性を示す例は次のとおりです。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用する Service Bus 接続文字列を含むアプリ設定の名前を指定できます。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

完全な例については、「[出力 - 例](#example)」を参照してください。

`ServiceBusAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルで使用する Service Bus アカウントを指定できます。  詳細については、[トリガー - 属性](functions-bindings-service-bus-trigger.md#attributes-and-annotations)をご覧ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput` と `ServiceBusTopicOutput` 注釈を使用して、関数の出力としてメッセージを書き込むことができます。 これらの注釈で修飾されたパラメーターは `OutputBinding<T>` として宣言されている必要があります。ここで、`T` はメッセージの型に対応する型です。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `ServiceBus` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "serviceBus" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "out" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューまたはトピック メッセージを表す変数の名前。 "$return" に設定して、関数の戻り値を参照します。 |
|**queueName**|**QueueName**|キューの名前。  トピックではなくキューのメッセージを送信する場合にのみ設定します。
|**topicName**|**TopicName**|トピックの名前。 キューではなくトピックのメッセージを送信する場合にのみ設定します。|
|**connection**|**接続**|このバインドに使用する Service Bus 接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。<br><br>接続文字列は、[管理資格情報の取得](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)に関する記事の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。|
|**accessRights** (v1 のみ)|**Access (アクセス)**|接続文字列のアクセス権。 使用できる値は `manage` と `listen` です。 既定値は `manage` で、`connection` が **管理** アクセス許可を持つことを示します。 **管理** アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を "listen" に設定します。 設定しないと、Functions ランタイムが管理権限を必要とする操作の試行に失敗する可能性があります。 最新バージョンの Service Bus SDK が管理の操作をサポートしていないため、Azure Functions バージョン 2.x 以降ではこのプロパティを利用できません。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

Azure Functions 1.x では、キューが存在しない場合に `accessRights` を `manage` に設定していると、ランタイムによってキューが作成されます。 Functions バージョン 2.x 以降では、キューまたはトピックが既に存在する必要があります。存在しないキューまたはトピックを指定した場合、関数は失敗します。 

# <a name="c"></a>[C#](#tab/csharp)

出力バインドには、次のパラメーターの型を使用します。

* `out T paramName` - `T` は任意の JSON シリアル化可能な型にすることができます。 関数が終了したときにこのパラメーターの値が null の場合、Functions は、null オブジェクトでメッセージを作成します。
* `out string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out BrokeredMessage` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません (Functions 1.x の場合)。
* `out Message` - パラメーター値が null の場合は、関数が終了したときに、Functions はメッセージを作成しません (Functions 2.x 以降の場合)
* `ICollector<T>` または `IAsyncCollector<T>` (非同期メソッドの場合) - 複数のメッセージを作成する場合。 `Add` メソッドを呼び出すときに、メッセージが作成されます。

C# 関数を使用する場合:

* 非同期関数には、`out` パラメーターの代わりに戻り値または `IAsyncCollector` が必要です。

* セッション ID にアクセスするには、 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 型にバインドし、`sessionId` プロパティを使用します。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

出力バインドには、次のパラメーターの型を使用します。

* `out T paramName` - `T` は任意の JSON シリアル化可能な型にすることができます。 関数が終了したときにこのパラメーターの値が null の場合、Functions は、null オブジェクトでメッセージを作成します。
* `out string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out BrokeredMessage` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません (Functions 1.x の場合)。
* `out Message` - パラメーター値が null の場合は、関数が終了したときに、Functions はメッセージを作成しません (Functions 2.x 以降の場合)
* `ICollector<T>` または `IAsyncCollector<T>`- 複数のメッセージを作成する場合。 `Add` メソッドを呼び出すときに、メッセージが作成されます。

C# 関数を使用する場合:

* 非同期関数には、`out` パラメーターの代わりに戻り値または `IAsyncCollector` が必要です。

* セッション ID にアクセスするには、 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 型にバインドし、`sessionId` プロパティを使用します。

# <a name="java"></a>[Java](#tab/java)

組み込みの出力バインドではなく、[Azure Service Bus SDK](../service-bus-messaging/index.yml) を使用します。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name from function.json>` を使用して、キューまたはトピックにアクセスします。 文字列、バイト配列、または (JSON に逆シリアル化された) JavaScript オブジェクトを `context.binding.<name>` に割り当てることができます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Service Bus への出力は `Push-OutputBinding` コマンドレット経由で取得できます。ここでは、*function.json* ファイル内のバインディングの name パラメーターで指定された名前に一致する引数を渡します。

# <a name="python"></a>[Python](#tab/python)

組み込みの出力バインドではなく、[Azure Service Bus SDK](../service-bus-messaging/index.yml) を使用します。

---

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| Service Bus | [Service Bus のエラー コード](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Service Bus の制限](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x 以降でこのバインドに使用可能なグローバル構成設定について説明します。 次の host.json ファイルの例には、このバインドの設定のみが含まれています。 グローバル構成設定の詳細については、[Azure Functions の host.json のリファレンス](functions-host-json.md)を参照してください。

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

`isSessionsEnabled` を `true` に設定している場合は、`sessionHandlerOptions` が有効になります。  `isSessionsEnabled` を `false` に設定している場合は、`messageHandlerOptions` が有効になります。

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|prefetchCount|0|メッセージの受信者が同時に要求できるメッセージ数を取得または設定します。|
|maxAutoRenewDuration|00:05:00|メッセージ ロックが自動的に更新される最大間隔。|
|autoComplete|true|トリガーが処理後に自動的に complete を呼び出す必要があるか、または関数コードで complete を手動で呼び出すかどうか。<br><br>`false` に設定することは、C# でのみサポートされています。<br><br>`true` に設定した場合、関数の実行が正常に完了するとトリガーによって自動的にメッセージが完了され、それ以外の場合はメッセージが破棄されます。<br><br>`false` に設定する場合は、[MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) を呼び出し、メッセージを完了、破棄、または配信不能にする必要があります。 例外がスローされた場合 (かつ `MessageReceiver` メソッドが呼び出されなかった場合)、ロックは維持されます。 ロックが期限切れになると、メッセージはキューに再登録されて `DeliveryCount` はインクリメントされ、ロックは自動的に更新されます。<br><br>C# 以外の関数では、関数で例外が発生すると、ランタイムによってバックグラウンドで `abandonAsync` が呼び出されます。 例外が発生しなかった場合は、バックグラウンドで `completeAsync` が呼び出されます。 |
|maxConcurrentCalls|16|スケーリングされたインスタンスごとにメッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数。 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。|
|maxConcurrentSessions|2000|スケーリングされたインスタンスごとに同時に処理できるセッションの最大数。|

## <a name="next-steps"></a>次のステップ

- [Service Bus キューまたはトピック メッセージが作成されたときに関数を実行する (トリガー)](./functions-bindings-service-bus-trigger.md)
