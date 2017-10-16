---
title: "Azure Functions における通知ハブのバインド | Microsoft Docs"
description: "Azure Functions で Azure Notification Hub のバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-notification-hub-output-binding"></a>Azure Functions における通知ハブの出力バインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Notification Hub のバインドを構成したりコーディングしたりする方法について説明します。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

関数を使用して、構成された Azure Notification Hub で数行のコードを使ってプッシュ通知を送信できます。 ただし、Azure Notification Hub は、使用するプラットフォーム通知サービス (PNS) 用に構成する必要があります。 Azure Notification Hub の構成方法や、通知を受け取るために登録するクライアント アプリケーションの開発方法の詳細については、 [Notification Hubs の使用](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) に関するページを参照して、上部の目的のクライアント プラットフォームをクリックしてください。

送信できる通知は、ネイティブ通知またはテンプレート通知です。 ネイティブ通知は、出力バインドの `platform` プロパティで構成された特定の通知プラットフォームを対象としています。 テンプレート通知は、複数のプラットフォームを対象として使用できます。   

## <a name="notification-hub-output-binding-properties"></a>Notification Hub 出力バインドのプロパティ
function.json ファイルは、次のプロパティを提供します。


|プロパティ  |Description  |
|---------|---------|
|**name** | 通知ハブ メッセージの関数コードで使用される変数名。 |
|**type** | `notificationHub` に設定する必要があります。 |
|**tagExpression** | タグ式。これにより、タグ式に一致する通知を受信するように登録した一連のデバイスに通知を配信するように指定できます。  詳細については、「[ルーティングとタグ式](../notification-hubs/notification-hubs-tags-segment-push-message.md)」を参照してください。 |
|**hubName** | Azure Portal 内の通知ハブ リソースの名前。 |
|**接続** | この接続文字列は、使用している通知ハブの *DefaultFullSharedAccessSignature* 値に設定された**アプリケーション設定**の接続文字列である必要があります。 |
|**direction** | `out` に設定する必要があります。 | 
|**platform** | platform プロパティは、通知の対象となる通知プラットフォームを示します。 既定では、プラットフォームのプロパティが出力バインドから省略されている場合、テンプレート通知は、Azure 通知ハブで構成されている任意のプラットフォームを対象として使用できます。 Azure Notification Hub でテンプレートを使用してクロスプラットフォームの通知を送信する一般的な方法の詳細については、「[Templates (テンプレート)](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)」を参照してください。 設定されている場合、_platform_ には、次のいずれかの値を指定する必要があります。 <ul><li><code>apns</code>&mdash;Apple Push Notification Service。 APNS 向けに Notification Hub を構成する方法と、クライアント アプリで通知を受信する方法の詳細については、「[Azure Notification Hubs から iOS へのプッシュ通知の送信](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)」を参照してください。</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging)。 ADM 向けに Notification Hub を構成する方法と、Kindle アプリで通知を受信する方法の詳細については、「[Notification Hubs の使用 (Kindle アプリ)](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)」を参照してください。</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/)。 GCM の新しいバージョンである Firebase Cloud Messaging もサポートされます。 詳細については、「[Azure Notification Hubs から Android へのプッシュ通知の送信](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)」を参照してください。</li><li><code>wns</code>&mdash;Windows プラットフォーム向けの [Windows Push Notification Services](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。 WNS では Windows Phone 8.1 以降もサポートされます。 詳細については、「[Notification Hubs の使用 (Windows ユニバーサル プラットフォーム アプリ)](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)」を参照してください。</li><li><code>mpns</code>&mdash;[Microsoft Push Notification Service](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx)。 このプラットフォームでは、Windows Phone 8 およびそれ以前の Windows Phone プラットフォームがサポートされます。 詳細については、「[Windows Phone での Azure Notification Hubs を使用したプッシュ通知の送信](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)」を参照してください。</li></ul> |

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
Notification Hub 出力バインドを使用するには、ハブの接続文字列を設定する必要があります。 既存の通知ハブを選択するか、関数の "*統合*" タブから新しいハブを作成できます。 接続文字列を手動で構成することもできます。 

既存の通知ハブに対する接続文字列を構成するには:

1. [Azure Portal](https://portal.azure.com) で通知ハブに移動し、**[アクセスポリシー]** を選択して、**[DefaultFullSharedAccessSignature]** ポリシーの横にあるコピー ボタンを選択します。 これにより、*DefaultFullSharedAccessSignature* ポリシーの接続文字列が通知ハブにコピーされます。 この接続文字列により、通知メッセージを送信するための関数アクセス権限が付与されます。 
    ![通知ハブの接続文字列をコピーする](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Azure Portal の関数アプリに移動し、**[アプリケーション設定]** を選択し、`MyHubConnectionString` などのキーを追加します。次に、通知ハブ用にコピーされた *DefaultFullSharedAccessSignature* を値として貼り付けて、**[保存]** をクリックします。

これで、出力バインドの通知ハブ接続を定義する、この名前付きアプリケーション設定を使用できます。

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

