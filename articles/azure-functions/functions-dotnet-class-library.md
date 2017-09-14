---
title: "Azure Functions での .NET クラス ライブラリの使用 |Microsoft Docs"
description: "Azure Functions で使用するために .NET クラス ライブラリを作成する方法について説明します。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/09/2017
ms.author: donnam
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 0613bb96d3afb85ff7e684246b128e4eef518d23
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="using-net-class-libraries-with-azure-functions"></a>Azure Functions での .NET クラス ライブラリの使用

Azure Functions は、スクリプト ファイルに加えて、1 つまたは複数の関数の実装としてのクラス ライブラリの公開をサポートします。 [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件 

この記事の前提条件は次のとおりです。

- [Visual Studio 2017 15.3 プレビュー](https://www.visualstudio.com/vs/preview/)。 **ASP.NET および Web 開発**と **Azure 開発**ワークロードのインストール
- [Azure Function Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=AndrewBHall-MSFT.AzureFunctionToolsforVisualStudio2017)

## <a name="functions-class-library-project"></a>関数クラス ライブラリ プロジェクト

Visual Studio で新しい Azure Functions プロジェクトを作成します。 新しいプロジェクト テンプレートによって、*host.json* ファイルと *local.settings.json* ファイルが作成されます。 [host.json で Azure Functions のランタイム設定をカスタマイズする](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)ことができます。 

*local.settings.json* ファイルには、アプリの設定、接続文字列、および Azure Functions Core Tools の設定が格納されます。 構造の詳細については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md#local-settings)」を参照してください。

### <a name="functionname-attribute"></a>FunctionName 属性

[ `FunctionNameAttribute` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) 属性は、関数のエントリ ポイントとしてメソッドをマークします。 これは、正確に 1 つのトリガーと、0 以上の入力および出力のバインドで使用する必要があります。

### <a name="conversion-to-functionjson"></a>function.json への変換

Azure Functions プロジェクトをビルドする場合、`[FunctionName]` で定義された関数名と一致するディレクトリ内に `function.json` ファイルが生成されます。 このファイルは、トリガーとバインドを指定し、プロジェクトのアセンブリ ファイルをポイントします。

この変換は、NuGet パッケージ [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions) によって実行されます。 ソースは、GitHub リポジトリ [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk) で利用できます。

## <a name="triggers-and-bindings"></a>トリガーとバインド

次の表は、Azure Functions クラス ライブラリ プロジェクトで使用できるトリガーとバインドの一覧を示しています。 すべての属性は、名前空間 `Microsoft.Azure.WebJobs` に含まれています。

| バインド | Attribute | NuGet パッケージ |
|------   | ------    | ------        |
| [Blob ストレージのトリガー、入力、出力](#blob-storage) | [BlobAttribute]、[StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [Blob ストレージ] |
| [Cosmos DB の入力および出力バインド](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [イベント ハブのトリガーと出力](#event-hub) | [EventHubTriggerAttribute]、[EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [外部ファイルの入力と出力](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [HTTP と webhook のトリガー](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [Mobile Apps の入力と出力](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [Notification Hubs output](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [Queue Storage トリガーと出力](#queue) | [QueueAttribute]、[StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [SendGrid の出力](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [Service Bus トリガー と出力](#service-bus) | [ServiceBusAttribute]、[ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [Table ストレージの入力と出力](#table) | [TableAttribute]、[StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [タイマー トリガー](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Twilio 出力](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-and-output-bindings"></a>Blob Storage のトリガー、入力、出力のバインド

Azure Functions は、Azure BLOB Storage のトリガー、入力、出力のバインドをサポートしています。 バインドの式とメタデータの詳細については、「[Azure Functions における Blob Storage のバインディング](functions-bindings-storage-blob.md)」を参照してください。

Blob のトリガーは、`[BlobTrigger]` 属性を使用して定義されます。 `[StorageAccount]` 属性を使用して、関数またはクラス全体で使用されるストレージ アカウントを定義することができます。

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Blob の入力と出力は、`[Blob]` 属性と、読み取りまたは書き込みを示す `FileAccess` パラメーターを使用して定義します。 次の例は、blob トリガーと blob 出力バインドを使用します。

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

<a name="cosmos-db"></a>

### <a name="cosmos-db-input-and-output-bindings"></a>Cosmos DB の入力および出力バインド

Azure Functions は、Cosmos DB の入力および出力バインドをサポートしています。 Cosmos DB バインディングの機能の詳細については、「[Azure Functions における Cosmos DB のバインド](functions-bindings-documentdb.md)」を参照してください。

Cosmos DB のドキュメントにバインドするには、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.DocumentDB] で `[DocumentDB]` 属性を使用します。 次の例には、キューのトリガーと DocumentDB API 出力インドがあります。

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", ConnectionStringSetting = "DocDBConnection")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>イベント ハブのトリガーと出力

Azure Functions は、イベント ハブのトリガーおよび出力バインドをサポートしています。 詳細については、「[Azure Functions における Event Hub のバインド](functions-bindings-event-hubs.md)」を参照してください。

型 `[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` と `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` は、NuGet パッケージ [Microsoft.Azure.WebJobs.ServiceBus] で定義されます。 

次の例では、イベント ハブ トリガーを使用します。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

次の例には、メソッドの戻り値を出力として使用するイベント ハブ出力があります。

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>外部ファイルの入力と出力

Azure Functions は、Google Drive、Dropbox、OneDrive などの外部ファイルのトリガー、入力、および出力のバインドをサポートしています。 詳細については、「[Azure Functions 外部ファイル バインディング](functions-bindings-external-file.md)」を参照してください。 属性 `[ExternalFileTrigger]` と `[ExternalFile]` は、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.ApiHub] で定義されます。

次の C# の例は、外部ファイルの入力と出力のバインドを示しています。 コードは、入力ファイルを出力ファイルにコピーします。

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP と webhook

`HttpTrigger` 属性を使用して、HTTP トリガーまたは webhook を定義します。 この属性は、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.Http] で定義されます。 承認レベル、webhook の種類、ルート、およびメソッドをカスタマイズすることができます。 次の例は、匿名認証および webhook の種類 _genericJson_ を使用して HTTP トリガーを定義します。

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>Mobile Apps の入力と出力

Azure Functions は、Mobile Apps の入力および出力バインドをサポートしています。 詳細については、「[Azure Functions における Mobile Apps のバインド](functions-bindings-mobile-apps.md)」を参照してください。 属性 `[MobileTable]` は、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.MobileApps] で定義されます。

次の例は、Mobile Apps の出力のバインドを示しています。

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>Notification Hubs の出力

Azure Functions は、Notification Hubs の出力バインドをサポートします。 詳細については、「[Azure Functions における通知ハブの出力バインド](functions-bindings-notification-hubs.md)」を参照してください。 属性 `[NotificationHub]` は、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] で定義されます。

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>Queue Storage トリガーと出力

Azure Functions は、Azure キューのトリガーおよび出力バインドをサポートしています。 詳細については、「[Azure Functions における Storage キュー バインド](functions-bindings-storage-queue.md)」を参照してください。

次の例は、`[Queue]` 属性を使用し、関数の戻り値の型とキューの出力バインドを使用する方法を示しています。 キュー トリガーを定義するには、`[QueueTrigger]` 属性を使用します。

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }

    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```

<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>SendGrid の出力

Azure Functions は、プログラムによって電子メールを送信するための SendGrid 出力バインドをサポートしています。 詳細については、「[Azure Functions における SendGrid のバインディング](functions-bindings-sendgrid.md)」を参照してください。

属性 `[SendGrid]` は、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.SendGrid] で定義されます。

Service Bus キュー トリガーの使用と`SendGridMessage`を使用した SendGrid 出力バインドの例を次に示します。

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>Service Bus トリガー と出力

Azure Functions は、Service Bus のキューおよびトピックのトリガーおよび出力バインドをサポートしています。 バインドの構成の詳細については、「[Azure Functions における Service Bus のバインド](functions-bindings-service-bus.md)」を参照してください。

属性 `[ServiceBusTrigger]` と `[ServiceBus]` は、NuGet パッケージ [Microsoft.Azure.WebJobs.ServiceBus] で定義されます。 

Service Bus キュー トリガーの例を次に示します。

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

メソッドの戻り値の型を出力として使用する Service Bus 出力バインドの例を次に示します。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>Table ストレージの入力と出力

Azure Functions は、Azure Table ストレージの入力および出力のバインドをサポートしています。 詳細については、「[Azure Functions における Storage テーブルのバインド](functions-bindings-storage-table.md)」を参照してください。

次の例は、Table ストレージの出力および入力バインディングを示す 2 つの関数を含むクラスです。 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>タイマー トリガー

Azure Functions には、定義したスケジュールに基づいて関数コードを実行することができるタイマー トリガー バインディングがあります。 バインドの機能の詳細については、「[Azure Functions におけるタイマー トリガー](functions-bindings-timer.md)」を参照してください。

従量課金プランでは、[CRON 式](http://en.wikipedia.org/wiki/Cron#CRON_expression)を使用してスケジュールを定義することができます。 App Service プランを使用している場合は、TimeSpan 文字列も使用できます。 

次の例では、5 分ごとに実行されるタイマー トリガーを定義します。

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Twilio 出力

Azure Functions は、関数で SMS テキスト メッセージを送信できるようにする Twilio 出力バインドをサポートしています。 詳細については、「[Twilio 出力バインディングを使用した Azure Functions からの SMS メッセージの送信](functions-bindings-twilio.md)」を参照してください。 

属性 `[TwilioSms]` は、パッケージ [Microsoft.Azure.WebJobs.Extensions.Twilio] で定義されます。

次の C# の例では、キュー トリガーと Twilio 出力のバインドを使用します。

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>次のステップ

C# スクリプトでの Azure 関数の使用の詳細については、「[Azure Functions C\# 開発者向けリファレンス](functions-reference-csharp.md)」を参照してください。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs

