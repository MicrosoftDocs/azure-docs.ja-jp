---
title: "Azure Functions でのトリガーとバインドの使用 | Microsoft Docs"
description: "Azure Functions で、トリガーとバインドを使用してコード実行をオンライン イベントおよびクラウドベース サービスに接続する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a56d71d437814ed08b2e0a05d9acc8448f6b9ae5
ms.lasthandoff: 03/17/2017


---

# <a name="learn-how-to-work-with-triggers-and-bindings-in-azure-functions"></a>Azure Functions でのトリガーとバインドの使用方法 
このトピックでは、Azure Functions でトリガーおよびバインドを使用して、さまざまなトリガーや Azure サービス、その他のクラウドベース サービスにコードを接続する方法を示します。 主に、すべてのバインド タイプでサポートされる高度なバインド機能と構文について説明します。  

特定の種類のトリガーまたはバインドを使用する方法の詳細については、次の参照トピックのいずれかを参照してください。

| | | | |  
| --- | --- | --- | --- |  
| [HTTP/webhook](functions-bindings-http-webhook.md) | [Timer](functions-bindings-timer.md) | [Mobile Apps](functions-bindings-mobile-apps.md) | [Service Bus](functions-bindings-service-bus.md)  |  
| [DocumentDB](functions-bindings-documentdb.md) |  [Storage Blob](functions-bindings-storage-blob.md) | [ストレージ キュー](functions-bindings-storage-queue.md) |  [ストレージ テーブル](functions-bindings-storage-table.md) |  
| [Event Hubs](functions-bindings-event-hubs.md) | [Notification Hubs](functions-bindings-notification-hubs.md) | [SendGrid](functions-bindings-sendgrid.md) | [Twilio](functions-bindings-twilio.md) |   
| | | | |  

これらの記事は、「[Azure Functions developer reference (Azure Functions 開発者用リファレンス)](functions-reference.md)」と [C#](functions-reference-csharp.md)、[F#](functions-reference-fsharp.md)、または [Node.js](functions-reference-node.md) の開発者用リファレンスを既に読んでいることを前提としています。

## <a name="overview"></a>概要
トリガーは、カスタム コードをトリガーするために使用されるイベント応答です。 これにより、Azure プラットフォーム全体またはオンプレミスのイベントに応答できます。 バインドは、コードを目的のトリガーや関連する入出力データに接続するために使用される必要なメタデータを表します。 各関数の *function.json* ファイルには、すべての関連バインドが含まれます。 関数の入力バインドと出力バインドの数に制限はありません。 ただし、各関数でサポートされるトリガーのバインドは 1 つのみです。  

Azure Function アプリに統合できるさまざまなバインドの詳細については、以下の表を参照してください。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

トリガーとバインド全般をより深く理解するために、Azure Storage キューにドロップされた新しい項目を処理するいくつかのコードを実行する必要があるとします。 Azure Functions には、これをサポートするための Azure キュー トリガーが用意されています。 キューを監視するには次の情報が必要です。

* キューが存在するストレージ アカウント。
* キュー名。
* キューにドロップされた新しい項目を参照するためにコードで使用する変数名。  

1 つのキュー トリガー バインドには、1 つの Azure 関数についての上記の情報が含まれます。 1 つのキュー トリガー バインドを含んでいる *function.json* の例を次に示します。 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

コードでは、新しいキュー項目の処理方法に応じてさまざまなタイプの出力を送信できます。 たとえば、Azure Storage テーブルに新しいレコードを書き込む必要があるとします。  これを行うには、Azure Storage テーブルへの出力バインドを作成します。 キュー トリガーに使用できるストレージ テーブルの出力バインドを含んでいる *function.json* の例を次に示します。 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

次の C# 関数は、キューにドロップされる新しい項目に応答し、Azure Storage テーブルに新しいユーザー エントリを書き込みます。

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

その他のコード例とサポート対象の Azure ストレージ タイプに関するさらに具体的な情報については、「[Azure Functions における Azure Storage のトリガーとバインド](functions-bindings-storage.md)」を参照してください。

より高度なバインド機能を Azure Portal で使用するには、関数の **[統合]** タブにある **[詳細エディター]** オプションをクリックします。 詳細エディターを使用すると、*function.json* を Portal で直接編集できます。

## <a name="random-guids"></a>ランダム GUID
Azure Functions には、バインドでランダム GUID を生成するための構文が用意されています。 次のバインド構文は、Storage コンテナー内の一意の名前を持つ新しい BLOB に出力を書き込みます。 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>単一の出力を返す場合
関数コードが単一の出力を返す場合、`$return` という名前の出力バインドを使用すると、より自然な関数シグネチャをコードで保持できます。 これは、戻り値がサポートされている言語 (C#、Node.js、F#) でのみ使用できます。 バインドは、キュー トリガーに使用される次の BLOB 出力バインドと同じようになります。

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

次の C# コードは、関数シグネチャで `out` パラメーターを使用せずに、より自然に出力を返します。

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

非同期の例:

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


これと同じアプローチを Node.js で示すと次のようになります。

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

F# の例を次に示します。

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

また、単一の出力を `$return` で指定することにより、これを複数の出力パラメーターで使用できます。

## <a name="resolving-app-settings"></a>アプリケーション設定の解決
アプリケーション設定を使用して、実行時環境の一部として機密情報を保存することをお勧めします。 機密情報をアプリケーションの構成ファイルとは別に保存することで、アプリケーション ファイルの格納にパブリック リポジトリを使用した場合にこれらの情報が危険にさらされるリスクを制限します。  

Azure Functions ランタイムは、`%your app setting%` のようにアプリケーション設定の名前がパーセント記号で囲まれている場合に、アプリケーション設定を値に解決します。 次の [Twilio バインド](functions-bindings-twilio.md)では、`TWILIO_ACCT_PHONE` というアプリケーション設定の名前をバインドの `from` フィールド用に使用しています。 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>パラメーターのバインド
出力バインド プロパティの設定を静的に構成する代わりに、トリガーの入力バインドに含まれるデータに動的にバインドされるように設定を構成できます。 新しい注文が Azure Storage キューを使用して処理されるシナリオを考えてみましょう。 それぞれの新しいキュー項目は、少なくとも次のプロパティを含んでいる JSON 文字列です。

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address",
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

注文が受信されたことを示す更新情報として、Twilio アカウントを使用して顧客に SMS テキスト メッセージを送信します。  Twilio 出力バインドの `body` および `to` フィールドを、入力に含まれていた `name` および `mobileNumber` に動的にバインドされるように構成できます。

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

あとは、関数コードで出力パラメータを次のように初期化するだけです。 実行時に、出力プロパティが目的の入力データにバインドされます。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js:

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>実行時の高度なバインド (命令型のバインド)

*function.json* を使用した標準的な入出力バインドのパターンは "[*宣言型*](https://en.wikipedia.org/wiki/Declarative_programming)" のバインドと呼ばれます。バインドは JSON 宣言によって定義されます。 ただし、[命令型](https://en.wikipedia.org/wiki/Imperative_programming)のバインドも使用できます。 このパターンを使用すると、サポートされている任意の数の入力バインドと出力バインドに関数コード内でバインドできます。
関数の設計時ではなく、関数の実行時にバインド パスまたは他の入力の計算が必要になった場合に、命令型のバインドが必要になることがあります。 

次のように命令型のバインドを定義します。

- 必要な命令型のバインドの *function.json* にエントリを**含めないで**ください。
- 入力パラメーター [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) または [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) を渡します。 
- 次の C# パターンを使用してデータ バインドを実行します。

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

ここで、`BindingTypeAttribute` はバインドを定義する .NET 属性、`T` はそのバインドの種類でサポートされている入力または出力の型です。 `T` も `out` パラメーター型 (`out JObject` など) にすることはできません。 たとえば、Mobile Apps テーブルの出力バインドは [6 種類の出力](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)をサポートしますが、`T` に使用できるのは [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) のみです。
    
次のコード例は、実行時に BLOB パスが定義された [Storage Blob の出力バインド](functions-bindings-storage-blob.md#storage-blob-output-binding)を作成し、この BLOB に文字列を書き込みます。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) は [Storage Blob](functions-bindings-storage-blob.md) の入力バインドまたは出力バインドを定義します。[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) はサポートされている出力バインドの種類です。
このコードは、ストレージ アカウントの接続文字列 (`AzureWebJobsStorage`) の既定のアプリケーション設定を取得します。 [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) を追加し、属性の配列を `BindAsync<T>()` に渡すことで、使用するカスタムなアプリケーション設定を指定できます。 たとえば、次のように入力します。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

以下の表に、それぞれのバインドの種類で使われる対応する .NET 属性と参照するパッケージを示します。

> [!div class="mx-codeBreakAll"]
| バインド | Attribute | 参照の追加 |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs)、[`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs)、[`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| ストレージ キュー | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| ストレージ テーブル | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [関数のテスト](functions-test-a-function.md)
* [関数のスケール](functions-scale.md)


