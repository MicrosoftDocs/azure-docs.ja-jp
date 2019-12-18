---
title: Azure Functions における Notification Hubs のバインド
description: Azure Functions で Azure Notification Hub のバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 211f8c8a203b81a4df6a8e9515b403f99cec572a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926329"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Azure Functions における Notification Hubs の出力バインド

この記事では、Azure Functions で [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) のバインドを使用してプッシュ通知を送信する方法について説明します。 Azure Functions は、Notification Hubs の出力バインドをサポートします。

Microsoft Azure Notification Hubs は、使用するプラットフォーム通知サービス (PNS) 用に構成する必要があります。 Notification Hubs からのプッシュ通知をクライアント アプリで取得するには、[Notification Hubs の使用](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)に関する記事を参照し、ページの上部にあるドロップダウン リストでターゲット クライアント プラットフォームを選択します。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google では [Google Cloud Messaging (GCM) は非推奨となり、Firebase Cloud Messaging (FCM) が推奨されます](https://developers.google.com/cloud-messaging/faq)。 この出力バインドは、FCM をサポートしていません。 FCM を使用して通知を送信するには、[Firebase API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) を関数に直接使用するか、[テンプレート通知](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)を使用します。

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Notification Hubs バインディングは [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet パッケージ、バージョン 1.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub リポジトリにあります。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>パッケージ - Functions 2.x 以降

このバインディングは、Functions 2.x 以降では使用できません。

## <a name="example---template"></a>例 - テンプレート

送信できる通知は、ネイティブ通知または[テンプレート通知](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)です。 ネイティブ通知は、出力バインドの `platform` プロパティで構成された特定のクライアント プラットフォームを対象としています。 テンプレート通知は、複数のプラットフォームを対象として使用できます。   

言語固有の例をご覧ください。

* [C# スクリプト - out パラメーター](#c-script-template-example---out-parameter)
* [C# スクリプト - 非同期](#c-script-template-example---asynchronous)
* [C# スクリプト - JSON](#c-script-template-example---json)
* [C# スクリプト - ライブラリのタイプ](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# スクリプト テンプレートの例 - out パラメーター

次の例では、テンプレートに `message` プレースホルダーを含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

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

### <a name="c-script-template-example---asynchronous"></a>C# スクリプト テンプレートの例 - 非同期

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

### <a name="c-script-template-example---json"></a>C# スクリプト テンプレートの例 - JSON

次の例では、有効な JSON 文字列を使用して、テンプレートに `message` プレースホルダーを含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# スクリプト テンプレートの例 - ライブラリのタイプ

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

### <a name="f-template-example"></a>F# テンプレートの例

次の例では、`location` と `message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>JavaScript テンプレートの例

次の例では、`location` と `message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
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

## <a name="example---apns-native"></a>例 - APNS ネイティブ

この C# スクリプトの例では、ネイティブ APNS 通知を送信する方法を示します。 

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

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>例 - WNS ネイティブ

この C# スクリプトの例では、[Microsoft Azure Notification Hubs ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)に定義されたタイプを使用してネイティブの WNS トースト通知を送信する方法を示します。 

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

## <a name="attributes"></a>属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) 属性を使用します。

この属性のコンストラクター パラメーターとプロパティについては、「[構成](#configuration)」セクションをご覧ください。

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `NotificationHub` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** |該当なし| `notificationHub` に設定する必要があります。 |
|**direction** |該当なし| `out` に設定する必要があります。 | 
|**name** |該当なし| 通知ハブ メッセージの関数コードで使用される変数名。 |
|**tagExpression** |**TagExpression** | タグ式。これにより、タグ式に一致する通知を受信するように登録した一連のデバイスに通知を配信するように指定できます。  詳細については、「[ルーティングとタグ式](../notification-hubs/notification-hubs-tags-segment-push-message.md)」を参照してください。 |
|**hubName** | **HubName** | Azure Portal 内の通知ハブ リソースの名前。 |
|**connection** | **ConnectionStringSetting** | Notification Hubs 接続文字列を含むアプリ設定の名前。  この接続文字列は、使用している通知ハブの *DefaultFullSharedAccessSignature* 値に設定される必要があります。 この記事で後述する「[接続文字列の設定](#connection-string-setup)」をご覧ください。|
|**platform** | **プラットフォーム** | platform プロパティは、通知の対象となるクライアント プラットフォームを示します。 既定では、プラットフォームのプロパティが出力バインドから省略されている場合、テンプレート通知は、Azure 通知ハブで構成されている任意のプラットフォームを対象として使用できます。 Azure Notification Hub でテンプレートを使用してクロスプラットフォームの通知を送信する一般的な方法の詳細については、「[Templates (テンプレート)](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)」を参照してください。 設定されている場合、**platform** には、次のいずれかの値を指定する必要があります。 <ul><li><code>apns</code>&mdash;Apple Push Notification Service。 APNS 向けに Notification Hub を構成する方法と、クライアント アプリで通知を受信する方法の詳細については、「[Azure Notification Hubs から iOS へのプッシュ通知の送信](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)」を参照してください。</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging)。 ADM 向けに Notification Hub を構成する方法と、Kindle アプリで通知を受信する方法の詳細については、「[Notification Hubs の使用 (Kindle アプリ)](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)」を参照してください。</li><li><code>wns</code>&mdash;Windows プラットフォーム向けの [Windows Push Notification Services](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview)。 WNS では Windows Phone 8.1 以降もサポートされます。 詳細については、「[Notification Hubs の使用 (Windows ユニバーサル プラットフォーム アプリ)](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)」を参照してください。</li><li><code>mpns</code>&mdash;[Microsoft Push Notification Service](/previous-versions/windows/apps/ff402558(v=vs.105))。 このプラットフォームでは、Windows Phone 8 およびそれ以前の Windows Phone プラットフォームがサポートされます。 詳細については、「[Windows Phone での Azure Notification Hubs を使用したプッシュ通知の送信](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)」を参照してください。</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>function.json ファイルの例

次に示すのは、*function.json* ファイルでの Notification Hubs バインディングの例です。

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>接続文字列の設定

通知ハブの出力バインドを使用するには、ハブの接続文字列を設定する必要があります。 既存の通知ハブを選択するか、Azure ポータルの *[統合]* タブから新しいハブを作成できます。 接続文字列を手動で構成することもできます。 

既存の通知ハブに対する接続文字列を構成するには:

1. [Azure Portal](https://portal.azure.com) で通知ハブに移動し、 **[アクセスポリシー]** を選択して、 **[DefaultFullSharedAccessSignature]** ポリシーの横にあるコピー ボタンを選択します。 これにより、*DefaultFullSharedAccessSignature* ポリシーの接続文字列が通知ハブにコピーされます。 この接続文字列を使用して、関数からハブに通知メッセージを送信できます。
    ![通知ハブの接続文字列をコピーする](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Azure Portal の関数アプリに移動し、 **[アプリケーション設定]** を選択し、**MyHubConnectionString** などのキーを追加します。次に、通知ハブ用にコピーされた *DefaultFullSharedAccessSignature* を値として貼り付けて、 **[保存]** をクリックします。

このアプリケーション設定の名前が、*function.json* または .NET 属性の出力バインディング接続設定で使用されます。 この記事で前述した「[構成](#configuration)」セクションをご覧ください。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| Notification Hub | [運用ガイド](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

