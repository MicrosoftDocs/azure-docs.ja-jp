---
title: "Azure Functions における通知ハブのバインド | Microsoft Docs"
description: "Azure Functions で Azure Notification Hub のバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: a64af1a9f969c5ce5a7e2f18ca23451ac96f1a13
ms.openlocfilehash: 7abd7b0921c029ff159935d89905d3c502aba643


---
# <a name="azure-functions-notification-hub-output-binding"></a>Azure Functions における通知ハブの出力バインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Notification Hub のバインドを構成したりコーディングしたりする方法について説明します。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

関数を使用して、構成された Azure Notification Hub で数行のコードを使ってプッシュ通知を送信できます。 ただし、Azure Notification Hub は、使用するプラットフォーム通知サービス (PNS) 用に構成する必要があります。 Azure Notification Hub の構成方法や、通知を受け取るために登録するクライアント アプリケーションの開発方法の詳細については、 [Notification Hubs の使用](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) に関するページを参照して、上部の目的のクライアント プラットフォームをクリックしてください。

送信できる通知は、ネイティブ通知またはテンプレート通知です。 ネイティブ通知は、出力バインドの `platform` プロパティで構成された特定の通知プラットフォームを対象としています。 テンプレート通知は、複数のプラットフォームを対象として使用できます。   

## <a name="notification-hub-output-binding-properties"></a>Notification Hub 出力バインドのプロパティ
function.json ファイルは、次のプロパティを提供します。

* `name` : 通知ハブ メッセージの関数コードで使用される変数名。
* `type` : *"notificationHub"*に設定する必要があります。
* `tagExpression`: タグ式。これにより、タグ式に一致する通知を受信するように登録した一連のデバイスに通知を配信するように指定できます。  詳細については、「[ルーティングとタグ式](../notification-hubs/notification-hubs-tags-segment-push-message.md)」を参照してください。
* `hubName` : Azure ポータル内の通知ハブ リソースの名前。
* `connection` : この接続文字列は、使用している通知ハブの **DefaultFullSharedAccessSignature** 値に設定された *アプリケーション設定* の接続文字列である必要があります。
* `direction` : *"out"*に設定する必要があります。 
* `platform` : platform プロパティは、通知の対象となる通知プラットフォームを示します。 次のいずれかの値を指定する必要があります。
  * `template` : これは、出力バインドで platform プロパティが省略された場合の既定のプラットフォームです。 テンプレート通知は、Azure Notification Hub に構成されているすべてのプラットフォームを対象として使用できます。 Azure Notification Hub でテンプレートを使用してクロスプラットフォームの通知を送信する一般的な方法の詳細については、「[Templates (テンプレート)](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)」を参照してください。
  * `apns` : Apple Push Notification Service。 APNS 向けに Notification Hub を構成する方法と、クライアント アプリで通知を受信する方法の詳細については、「[Azure Notification Hubs から iOS へのプッシュ通知の送信](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)」を参照してください。 
  * `adm` : [Amazon Device Messaging](https://developer.amazon.com/device-messaging)。 ADM 向けに Notification Hub を構成する方法と、Kindle アプリで通知を受信する方法の詳細については、「[Getting Started with Notification Hubs for Kindle apps](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)」(Kindle アプリ向けの Notification Hubs の概要) を参照してください。 
  * `gcm` : [Google Cloud Messaging](https://developers.google.com/cloud-messaging/)。 GCM の新しいバージョンである Firebase Cloud Messaging もサポートされます。 GCM/FCM 向けに Notification Hub を構成する方法と、Android クライアント アプリで通知を受信する方法の詳細については、「[Azure Notification Hubs から Android へのプッシュ通知の送信](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)」を参照してください。
  * `wns` : [Windows プラットフォーム向けの Windows プッシュ通知サービス](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。 WNS では Windows Phone 8.1 以降もサポートされます。 WNS 向けに Notification Hub を構成する方法と、Windows プラットフォーム (UWP) アプリで通知を受信する方法の詳細については、「[Getting started with Notification Hubs for Windows Universal Platform Apps](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)」(Windows ユニバーサル プラットフォーム アプリ向けの Notification Hubs の概要) を参照してください。
  * `mpns` : [Microsoft プッシュ通知サービス](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx)。 このプラットフォームでは、Windows Phone 8 およびそれ以前の Windows Phone プラットフォームがサポートされます。 MPNS 向けに Notification Hub を構成する方法と、Windows Phone アプリで通知を受信する方法の詳細については、「[Windows Phone での Azure Notification Hubs を使用したプッシュ通知の送信](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)」を参照してください。

function.json の例:

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>Notification Hub の接続文字列の設定
Notification Hub 出力バインドを使用するには、ハブの接続文字列を設定する必要があります。 そのためには、*[統合]* タブで、通知ハブを選択するか、新しいハブを作成します。 

*DefaultFullSharedAccessSignature* の接続文字列を通知ハブに追加して、既存のハブの接続文字列を手動で追加することもできます。 この接続文字列により、通知メッセージを送信するための関数アクセス権限が付与されます。 *DefaultFullSharedAccessSignature* 接続文字列の値には、Azure ポータルの通知ハブ リソースのメイン ブレードにある **[キー]** ボタンからアクセスできます。 ハブの接続文字列を手動で追加するには、次の手順を実行します。 

1. Azure Portal の **[Function App]** ブレードで、**[Function App の設定]、[App Service の設定に移動]** の順にクリックします。
2. **[設定]** ブレードで、**[アプリケーションの設定]** をクリックします。
3. **[アプリケーション設定]** セクションまでスクロールして、通知ハブの *DefaultFullSharedAccessSignature* 値の名前付きエントリを追加します。
4. 出力バインドのアプリケーション設定文字列名を参照します。 上の例で使用した **MyHubConnectionString** と同様です。

## <a name="apns-native-notifications-with-c-queue-triggers"></a>C# キュー トリガーを使用した APNS ネイティブ通知
次の例では、[Microsoft Azure Notification Hubs ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)に定義されたタイプを使用してネイティブの APNS 通知を送信する方法を示します。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>C# キュー トリガーを使用した GCM ネイティブ通知
次の例では、[Microsoft Azure Notification Hubs ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)に定義されたタイプを使用してネイティブの GCM 通知を送信する方法を示します。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>C# キュー トリガーを使用した WNS ネイティブ通知
次の例では、[Microsoft Azure Notification Hubs ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)に定義されたタイプを使用してネイティブの WNS トースト通知を送信する方法を示します。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Node.js タイマー トリガーのテンプレートの例
次の例では、`location` と `message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>F# タイマー トリガーのテンプレートの例
次の例では、`location` と `message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>out パラメーターを使用したテンプレートの例
次の例では、テンプレートに `message` プレース ホルダーを含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>非同期関数を使用するテンプレートの例
非同期コードを使用している場合は、out パラメーターを使用できません。 その場合は、`IAsyncCollector` を使用してテンプレート通知を返します。 次のコードでは、上記のコードの非同期の例を示します。 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>JSON を使用したテンプレートの例
次の例では、有効な JSON 文字列を使用して、テンプレートに `message` プレース ホルダーを含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>Notification Hubs ライブラリのタイプを使用したテンプレートの例
次の例では、[Microsoft Azure Notification Hubs ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)に定義されたタイプの使用方法を示します。 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Feb17_HO2-->


