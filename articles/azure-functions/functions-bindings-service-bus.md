---
title: Azure Functions における Azure Service Bus のバインド
description: Azure Functions で Azure Service Bus トリガーとバインドを使用する方法を説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 46e6858376fa70b4b57b6106f8292b842f206d01
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480228"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions における Azure Service Bus のバインド

この記事では、Azure Functions で Azure Service Bus のバインドを連携する方法について説明します。 Azure Functions は、Service Bus のキューおよびトピックのトリガーおよび出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Service Bus のバインドは [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet パッケージ、バージョン 2.x で提供されます。 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

Service Bus のバインドは [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>トリガー

Service Bus トリガーを使用して、Service Bus キューまたはトピックからのメッセージに応答します。 

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [C#](#trigger---c-example)
* [C# スクリプト (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、[メッセージ メタデータ](#trigger---message-metadata)を読み取り、Service Bus キュー メッセージをログに記録する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

この例は、Azure Functions バージョン 1.x 用です。 このコードを 2.x で機能させるには、次のようにします。

- [アクセス権パラメーターを省略する](#trigger---configuration)
- ログ パラメーターの型を `TraceWriter` から `ILogger` に変更する
- `log.Info` を `log.LogInformation` に変更する

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この機能は[メッセージ メタデータ](#trigger---message-metadata)を読み取り、Service Bus のキュー メッセージをログに記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>トリガー - F# の例

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数は、Service Bus キュー メッセージを記録します。 

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

F# スクリプト コードを次に示します。

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>トリガー - Java の例

次の Java 関数は、[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)からの `@ServiceBusQueueTrigger` 注釈を使用して、Service Bus キュー トリガーの構成について記述します。 この関数は、キューにあるメッセージを取得し、ログに追加します。

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Service Bus トピックにメッセージが追加されたときに、Java 関数もトリガーできます。 次の例では、トリガー構成を記述する `@ServiceBusTopicTrigger` 注釈を使用します。

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この機能は[メッセージ メタデータ](#trigger---message-metadata)を読み取り、Service Bus のキュー メッセージをログに記録します。 

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

JavaScript スクリプト コードを次に示します。

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

## <a name="trigger---attributes"></a>トリガー - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、以下の属性を使用して Service Bus トリガーを構成します。

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  属性のコンストラクターは、キューの名前、またはトピックとサブスクリプションを受け取ります。 Azure Functions バージョン 1.x では、接続のアクセス権を指定することもできます。 アクセス権を指定しない場合、既定値は `Manage` になります。 詳細については、「[トリガー - 構成](#trigger---configuration)」セクションをご覧ください。

  文字列パラメーターで使用される属性を示す例は次のとおりです。

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用する Service Bus アカウントを指定できます。

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  完全な例については、「[トリガー - C# の例](#trigger---c-example)」を参照してください。

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  使用する Service Bus アカウントを指定する別の方法を示します。 コンストラクターは、Service Bus 接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

使用する Service Bus アカウントは、次の順序で決定されます。

* `ServiceBusTrigger` 属性の `Connection` プロパティ。
* `ServiceBusTrigger` 属性と同じパラメーターに適用された `ServiceBusAccount` 属性。
* 関数に適用される `ServiceBusAccount` 属性。
* クラスに適用される `ServiceBusAccount` 属性。
* "AzureWebJobsServiceBus" アプリの設定。

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `ServiceBusTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "serviceBusTrigger" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "in" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューまたはトピック メッセージを表す変数の名前。 "$return" に設定して、関数の戻り値を参照します。 |
|**queueName**|**QueueName**|監視するキューの名前。  トピックではなくキューを監視する場合にのみ設定します。
|**topicName**|**TopicName**|監視するトピックの名前。 キューではなくトピックを監視する場合にのみ設定します。|
|**subscriptionName**|**SubscriptionName**|監視するサブスクリプションの名前。 キューではなくトピックを監視する場合にのみ設定します。|
|**connection**|**Connection**|このバインドに使用する Service Bus 接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。<br><br>接続文字列は、[管理資格情報の取得](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)に関する記事の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。 |
|**accessRights**|**Access (アクセス)**|接続文字列のアクセス権。 使用できる値は `manage` と `listen` です。 既定値は `manage` で、`connection` が**管理**アクセス許可を持つことを示します。 **管理**アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を "listen" に設定します。 設定しないと、Functions ランタイムが管理権限を必要とする操作の試行に失敗する可能性があります。 最新バージョンの Storage SDK が管理の操作をサポートしていないため、Azure Functions バージョン 2.x ではこのプロパティを利用できません。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>トリガー - 使用方法

C# と C# スクリプトでは、キューまたはトピック メッセージに次のパラメーター型を使用できます。

* `string` - メッセージがテキストである場合。
* `byte[]` - バイナリ データの場合に便利です。
* カスタム型 - メッセージに JSON が含まれている場合、Azure Functions は JSON データの逆シリアル化を試みます。
* `BrokeredMessage` - [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) メソッドで逆シリアル化されたメッセージを返します。

これらのパラメーターは Azure Functions バージョン 1.x 用です。2.x では、`BrokeredMessage` の代わりに [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) を使用してください。

JavaScript で、`context.bindings.<name from function.json>` を使用して、キューまたはトピック メッセージにアクセスします。 Service Bus メッセージが文字列または JSON オブジェクトとして関数に渡されます。

## <a name="trigger---poison-messages"></a>トリガー - 有害メッセージ

Azure Functions では、有害メッセージの処理を制御することも構成することもできません。 Service Bus 自体で、有害なメッセージが処理されます。

## <a name="trigger---peeklock-behavior"></a>トリガー - PeekLock 動作

Functions ランタイムは、メッセージを [PeekLock モード](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)で受信します。 それは、関数が正常に終了した場合はメッセージに対して `Complete` を呼び出し、関数が失敗した場合は `Abandon` を呼び出します。 関数が実行されている限り、関数の実行時間が `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。 

`maxAutoRenewDuration` は *host.json* に構成可能です。これは [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet) にマップされます。 この設定に許可される最大値は、Service Bus のドキュメントに従って 5 分ですが、Functions の制限時間は既定の 5 分から 10 分に増やすことができます。 Service Bus 関数の場合は、Service Bus の更新制限を超えるので増やしません。

## <a name="trigger---message-metadata"></a>トリガー - メッセージのメタデータ

Service Bus トリガーには、いくつかの[メタデータ プロパティ](./functions-bindings-expressions-patterns.md#trigger-metadata)があります。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらは [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) クラスのプロパティです。

|プロパティ|Type|説明|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|配信回数。|
|`DeadLetterSource`|`string`|配信不能のソース。|
|`ExpiresAtUtc`|`DateTime`|有効期限 (UTC)。|
|`EnqueuedTimeUtc`|`DateTime`|エンキューされた時刻 (UTC)。|
|`MessageId`|`string`|有効になっている場合に、重複するメッセージを識別するために Service Bus が使用できるユーザー定義の値|
|`ContentType`|`string`|アプリケーション固有のロジックのために送信者と受信者が利用するコンテンツ タイプ識別子。|
|`ReplyTo`|`string`|キュー アドレスへの返信。|
|`SequenceNumber`|`Int64`|Service Bus によってメッセージに割り当てられる一意の番号。|
|`To`|`string`|送信先アドレス。|
|`Label`|`string`|アプリケーション固有のラベル。|
|`CorrelationId`|`string`|関連付け ID。|

> [!NOTE]
> 現在、セッションで有効になっているキューやサブスクリプションで動作する Service Bus トリガーはプレビュー段階です。 これに関連した今後の更新については、[この項目](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458)を追跡してください。 

この記事の前半でこれらのプロパティを使用している[コード例](#trigger---example)を参照してください。

## <a name="trigger---hostjson-properties"></a>トリガー - host.json のプロパティ

[host.json](functions-host-json.md#servicebus) ファイルには、Service Bus トリガーの動作を制御する設定が含まれています。

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------|
|maxConcurrentCalls|16|メッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。 一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、`maxConcurrentCalls` を 1 に設定します。 |
|prefetchCount|該当なし|基になる MessageReceiver に使用される既定の PrefetchCount。|
|maxAutoRenewDuration|00:05:00|メッセージ ロックが自動的に更新される最大間隔。|

## <a name="output"></a>Output

キューまたはトピック メッセージを送信するには、Azure Service Bus 出力バインドを使用します。

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [C#](#output---c-example)
* [C# スクリプト (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>出力 - C# の例

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

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

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
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>出力 - F# の例

次の例は、*function.json* ファイルの Service Bus 出力バインドと、そのバインドを使用する [F# スクリプト関数](functions-reference-fsharp.md)を示しています。 この関数は、タイマー トリガーを使用して、15 秒ごとにキュー メッセージを送信します。

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

単一のメッセージを作成する F# スクリプト コードを次に示します。

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>出力 - Java の例

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

### <a name="output---javascript-example"></a>出力 - JavaScript の例

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

## <a name="output---attributes"></a>出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs) 属性を使用します。

属性のコンストラクターは、キューの名前、またはトピックとサブスクリプションを受け取ります。 接続のアクセス権を指定することもできます。 アクセス権の設定を選択する方法については、「[出力 - 構成](#output---configuration)」セクションで説明されています。 関数の戻り値に適用される属性に適用される属性を示す例は次のとおりです。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用する Service Bus アカウントを指定できます。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

完全な例については、「[出力 - C# の例](#output---c-example)」を参照してください。

`ServiceBusAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルで使用する Service Bus アカウントを指定できます。  詳細については、[トリガー - 属性](#trigger---attributes)をご覧ください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `ServiceBus` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "serviceBus" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "out" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューまたはトピックを表す変数の名前。 "$return" に設定して、関数の戻り値を参照します。 |
|**queueName**|**QueueName**|キューの名前。  トピックではなくキューのメッセージを送信する場合にのみ設定します。
|**topicName**|**TopicName**|監視するトピックの名前。 キューではなくトピックのメッセージを送信する場合にのみ設定します。|
|**connection**|**Connection**|このバインドに使用する Service Bus 接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。<br><br>接続文字列は、[管理資格情報の取得](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)に関する記事の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。|
|**accessRights**|**Access (アクセス)**|接続文字列のアクセス権。 使用できる値は `manage` と `listen` です。 既定値は `manage` で、`connection` が**管理**アクセス許可を持つことを示します。 **管理**アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を "listen" に設定します。 設定しないと、Functions ランタイムが管理権限を必要とする操作の試行に失敗する可能性があります。 最新バージョンの Storage SDK が管理の操作をサポートしていないため、Azure Functions バージョン 2.x ではこのプロパティを利用できません。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

Azure Functions 1.x では、キューが存在しない場合に `accessRights` を `manage` に設定していると、ランタイムによってキューが作成されます。 Azure Functions バージョン 2.x では、キューまたはトピックが既に存在する必要があります。存在しないキューまたはトピックを指定した場合、関数は失敗します。 

C# とC# スクリプトでは、出力バインドに次のパラメーター型を使用できます。

* `out T paramName` - `T` は任意の JSON シリアル化可能な型にすることができます。 関数が終了したときにこのパラメーターの値が null の場合、Functions は、null オブジェクトでメッセージを作成します。
* `out string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out BrokeredMessage` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `ICollector<T>` または `IAsyncCollector<T>`- 複数のメッセージを作成する場合。 `Add` メソッドを呼び出すときに、メッセージが作成されます。

非同期関数では、`out` パラメーターの代わりに戻り値または `IAsyncCollector` を使用します。

これらのパラメーターは Azure Functions バージョン 1.x 用です。2.x では、`BrokeredMessage` の代わりに [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) を使用してください。

JavaScript で、`context.bindings.<name from function.json>` を使用して、キューまたはトピックにアクセスします。 文字列、バイト配列、または (JSON に逆シリアル化された) Javascript オブジェクトを `context.binding.<name>` に割り当てることができます。

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| Service Bus | [Service Bus のエラー コード](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus の制限](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x でこのバインディングに使用可能なグローバル構成設定について説明します。 次の host.json ファイルの例には、このバインディングのバージョン 2.x の設定のみが含まれています。 バージョン 2.x でのグローバル構成設定の詳細については、[Azure Functions バージョン 2.x の host.json のリファレンス](functions-host-json.md)を参照してください。

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|メッセージ ロックが自動的に更新される最大間隔。|
|autoComplete|true|トリガーをすぐに完了としてマークする (オートコンプリート) か、呼び出しの処理が完了するまで待機するか。|
|maxConcurrentCalls|16|メッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。 一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、`maxConcurrentCalls` を 1 に設定します。 |
|prefetchCount|該当なし|基になる MessageReceiver に使用される既定の PrefetchCount。|


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
