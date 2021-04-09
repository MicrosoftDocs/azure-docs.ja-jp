---
title: Azure Functions の Azure Service Bus トリガー
description: Azure Service Bus メッセージの作成時に Azure 関数を実行する方法について説明します。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4b95c25400317b2baac694f4ba2b1b1dc1eae098
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435156"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Functions の Azure Service Bus トリガー

Service Bus トリガーを使用して、Service Bus キューまたはトピックからのメッセージに応答します。 拡張機能バージョン 3.1.0 以降では、セッションが有効なキューまたはトピックでトリガーを実行できます。

セットアップと構成の詳細については、[概要](functions-bindings-service-bus.md)に関するページをご覧ください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、[メッセージ メタデータ](#message-metadata)を読み取り、Service Bus キュー メッセージをログに記録する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
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

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この機能は[メッセージ メタデータ](#message-metadata)を読み取り、Service Bus のキュー メッセージをログに記録します。

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

# <a name="java"></a>[Java](#tab/java)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この機能は[メッセージ メタデータ](#message-metadata)を読み取り、Service Bus のキュー メッセージをログに記録します。

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、*function.json* ファイル内の Service Bus トリガー バインディングと、そのバインディングを使用する [PowerShell 関数](functions-reference-powershell.md)を示しています。 

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "name": "mySbMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "topicName": "mytopic",
      "subscriptionName": "mysubscription",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Service Bus メッセージが送信されたときに実行される関数を次に示します。

```powershell
param([string] $mySbMsg, $TriggerMetadata)

Write-Host "PowerShell ServiceBus queue trigger function processed message: $mySbMsg"
```

# <a name="python"></a>[Python](#tab/python)

次の例では、トリガーを使用して ServiceBus キュー メッセージを読み取る方法を示します。

Service Bus のバインディングは *function.json* で定義され、そこで *type* は `serviceBusTrigger` に設定されます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

*_\_init_\_.py* のコードによってパラメーターが `func.ServiceBusMessage` として宣言され、関数でキュー メッセージを読み取ることができるようになります。

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
        'metadata' : msg.metadata
    })

    logging.info(result)
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、以下の属性を使用して Service Bus トリガーを構成します。

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  属性のコンストラクターは、キューの名前、またはトピックとサブスクリプションを受け取ります。 Azure Functions バージョン 1.x では、接続のアクセス権を指定することもできます。 アクセス権を指定しない場合、既定値は `Manage` になります。 詳細については、「[トリガー - 構成](#configuration)」セクションをご覧ください。

  文字列パラメーターで使用される属性を示す例は次のとおりです。

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  `Connection` プロパティは定義されていないため、関数は `AzureWebJobsServiceBus` という名前のアプリ設定を探します。これは Service Bus 接続文字列の既定の名前です。 また、次の例で示すように、`Connection` プロパティを設定して、使用する Service Bus 接続文字列を含むアプリケーション設定の名前を指定することもできます。

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  完全な例については、「[トリガー - 例](#example)」を参照してください。

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

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

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueTrigger` 注釈を使用すると、Service Bus キュー メッセージの作成時に実行される関数を作成できます。 使用可能な構成オプションには、キュー名と接続文字列名があります。

`ServiceBusTopicTrigger` 注釈を使用すると、関数をトリガーするデータを対象とするトピックとサブスクリプションを指定できます。

詳細については、トリガーの[例](#example)を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `ServiceBusTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "serviceBusTrigger" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "in" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューまたはトピック メッセージを表す変数の名前。 |
|**queueName**|**QueueName**|監視するキューの名前。  トピックではなくキューを監視する場合にのみ設定します。
|**topicName**|**TopicName**|監視するトピックの名前。 キューではなくトピックを監視する場合にのみ設定します。|
|**subscriptionName**|**SubscriptionName**|監視するサブスクリプションの名前。 キューではなくトピックを監視する場合にのみ設定します。|
|**connection**|**接続**|このバインドに使用する Service Bus 接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。<br><br>接続文字列は、[管理資格情報の取得](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)に関する記事の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。 |
|**accessRights**|**Access (アクセス)**|接続文字列のアクセス権。 使用できる値は `manage` と `listen` です。 既定値は `manage` で、`connection` が **管理** アクセス許可を持つことを示します。 **管理** アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を "listen" に設定します。 設定しないと、Functions ランタイムが管理権限を必要とする操作の試行に失敗する可能性があります。 最新バージョンの Service Bus SDK が管理の操作をサポートしていないため、Azure Functions バージョン 2.x 以降ではこのプロパティを利用できません。|
|**isSessionsEnabled**|**IsSessionsEnabled**|[セッション対応の](../service-bus-messaging/message-sessions.md)キューまたはサブスクリプションに接続する場合は `true`。 それ以外の場合は `false` (既定値)。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

キューまたはトピック メッセージに次のパラメーター型を使用できます。

* `string` - メッセージがテキストである場合。
* `byte[]` - バイナリ データの場合に便利です。
* カスタム型 - メッセージに JSON が含まれている場合、Azure Functions は JSON データの逆シリアル化を試みます。
* `BrokeredMessage` - [BrokeredMessage.GetBody\<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) メソッドで逆シリアル化されたメッセージを返します。
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) - メッセージ コンテナーからのメッセージを受信および確認するために使用します ([`autoComplete`](functions-bindings-service-bus-output.md#hostjson-settings) が `false` に設定されている場合に必要です)

これらのパラメーター型は Azure Functions バージョン 1.x 用です。2.x 以降では、`BrokeredMessage` の代わりに [`Message`](/dotnet/api/microsoft.azure.servicebus.message) を使用してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

キューまたはトピック メッセージに次のパラメーター型を使用できます。

* `string` - メッセージがテキストである場合。
* `byte[]` - バイナリ データの場合に便利です。
* カスタム型 - メッセージに JSON が含まれている場合、Azure Functions は JSON データの逆シリアル化を試みます。
* `BrokeredMessage` - [BrokeredMessage.GetBody\<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) メソッドで逆シリアル化されたメッセージを返します。

これらのパラメーターは Azure Functions バージョン 1.x 用です。2.x 以降では、`BrokeredMessage` の代わりに [`Message`](/dotnet/api/microsoft.azure.servicebus.message) を使用してください。

# <a name="java"></a>[Java](#tab/java)

受信 Service Bus メッセージは、`ServiceBusQueueMessage` または `ServiceBusTopicMessage` パラメーターを使用して利用できます。

[詳細についての例を参照してください。](#example)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name from function.json>` を使用して、キューまたはトピック メッセージにアクセスします。 Service Bus メッセージが文字列または JSON オブジェクトとして関数に渡されます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Service Bus インスタンスは、*function.json* ファイルの name プロパティで構成されているパラメーター経由で使用できます。

# <a name="python"></a>[Python](#tab/python)

キュー メッセージは、`func.ServiceBusMessage` として型指定されたパラメーターを介して関数で使用できます。 Service Bus メッセージが文字列または JSON オブジェクトとして関数に渡されます。

---

## <a name="poison-messages"></a>有害メッセージ

Azure Functions では、有害メッセージの処理を制御することも構成することもできません。 Service Bus 自体で、有害なメッセージが処理されます。

## <a name="peeklock-behavior"></a>PeekLock 動作

Functions ランタイムは、メッセージを [PeekLock モード](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)で受信します。 それは、関数が正常に終了した場合はメッセージに対して `Complete` を呼び出し、関数が失敗した場合は `Abandon` を呼び出します。 関数が実行されている限り、関数の実行時間が `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。

`maxAutoRenewDuration` は *host.json* に構成可能です。これは [OnMessageOptions.MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration) にマップされます。 この設定に許可される最大値は、Service Bus のドキュメントに従って 5 分ですが、Functions の制限時間は既定の 5 分から 10 分に増やすことができます。 Service Bus 関数の場合は、Service Bus の更新制限を超えるので増やしません。

## <a name="message-metadata"></a>メッセージのメタデータ

Service Bus トリガーには、いくつかの[メタデータ プロパティ](./functions-bindings-expressions-patterns.md#trigger-metadata)があります。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらのプロパティは [Message](/dotnet/api/microsoft.azure.servicebus.message) クラスのメンバーです。

|プロパティ|種類|説明|
|--------|----|-----------|
|`ContentType`|`string`|アプリケーション固有のロジックのために送信者と受信者が利用するコンテンツ タイプ識別子。|
|`CorrelationId`|`string`|関連付け ID。|
|`DeadLetterSource`|`string`|配信不能のソース。|
|`DeliveryCount`|`Int32`|配信回数。|
|`EnqueuedTimeUtc`|`DateTime`|エンキューされた時刻 (UTC)。|
|`ExpiresAtUtc`|`DateTime`|有効期限 (UTC)。|
|`Label`|`string`|アプリケーション固有のラベル。|
|`MessageId`|`string`|有効になっている場合に、重複するメッセージを識別するために Service Bus が使用できるユーザー定義の値|
|`MessageReceiver`|`MessageReceiver`|Service Bus メッセージ受信者。 使用すると、メッセージの破棄、完了、または配信不能とすることができます。|
|`MessageSession`|`MessageSession`|セッションが有効なキューとトピック専用のメッセージ受信者。|
|`ReplyTo`|`string`|キュー アドレスへの返信。|
|`SequenceNumber`|`long`|Service Bus によってメッセージに割り当てられる一意の番号。|
|`To`|`string`|送信先アドレス。|
|`UserProperties`|`IDictionary<string, object>`|送信者によって設定されたプロパティ。|

この記事の前半でこれらのプロパティを使用している[コード例](#example)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Functions から Azure Service Bus メッセージを送信する (出力バインド)](./functions-bindings-service-bus-output.md)
