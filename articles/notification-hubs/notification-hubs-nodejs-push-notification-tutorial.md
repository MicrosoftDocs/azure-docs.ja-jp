---
title: Azure Notification Hubs と Node.js でのプッシュ通知の送信
description: Notification Hubs を使用して Node.js アプリケーションからプッシュ通知を送信する方法について説明します。
keywords: push notification,push notifications,node.js push,ios push
services: notification-hubs
documentationcenter: nodejs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 129127a2a43cd9a86e0a1e1cf538358b62381257
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706226"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Azure Notification Hubs と Node.js でのプッシュ通知の送信

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>概要

> [!IMPORTANT]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントをお持ちでない場合は、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)を使用してわずか数分で無料試用アカウントを作成できます。

このガイドでは、Azure Notification Hubs を使用して [Node.js](https://nodejs.org) アプリケーションから直接プッシュ通知を送信する方法を示します。

ここで扱うシナリオでは、次のプラットフォーム上のアプリケーションにプッシュ通知を送信します。

- Android
- iOS
- ユニバーサル Windows プラットフォーム
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs によって、モバイル デバイスにプッシュ通知を送信するための、使いやすくスケーラブルなマルチプラットフォーム インフラストラクチャが提供されます。 サービス インフラストラクチャの詳細については、「 [Azure Notification Hubs](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) 」のページを参照してください。

## <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

このチュートリアルの最初の手順では、新しい空の Node.js アプリケーションを作成します。 Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ][nodejswebsite], [Node.js Cloud Service][Node.js Cloud Service] (Windows PowerShell の使用)、または [WebMatrix を使用した Web サイト][webmatrix]に関する各ページをご覧ください。

## <a name="configure-your-application-to-use-notification-hubs"></a>アプリケーションを構成して Notification Hubs を使用する

Azure Notification Hubs を使用するには、Node.js [azure パッケージ](https://www.npmjs.com/package/azure)をダウンロードして使用する必要があります。このパッケージには、プッシュ通知 REST サービスと通信するためのヘルパー ライブラリの組み込みのセットが含まれています。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1. **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Linux) などのコマンド ライン インターフェイスを使用して、空のアプリケーションを作成したフォルダーに移動します。
2. コマンド ウィンドウで `npm install azure-sb` を実行します。
3. 手動で `ls` または `dir` コマンドを実行して、`node_modules` フォルダーが作成されたことを確認することもできます。
4. そのフォルダーで **azure** パッケージを探します。このパッケージには、Notification Hubs にアクセスするために必要なライブラリが含まれています。

> [!NOTE]
> NPM のインストールについては、公式 [NPM ブログ](https://blog.npmjs.org/post/85484771375/how-to-install-npm)を参照してください。

### <a name="import-the-module"></a>モジュールのインポート
テキスト エディターを使用して、アプリケーションの `server.js` ファイルの先頭に次の内容を追加します。

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Azure Notification Hub の接続を設定する

`NotificationHubService` オブジェクトを使用すると、通知ハブを操作できます。 次のコードは、`hubname` という名前の通知ハブ用の `NotificationHubService` オブジェクトを作成します。 `server.js` ファイルの先頭付近の、azure モジュールをインポートするステートメントの後に、このコードを追加します。

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

次の手順を行い、[Azure portal] から接続 `connectionstring` の値を取得します。

1. 左のナビゲーション ウィンドウで、 **[参照]** をクリックします。
2. **[Notification Hubs]** を選択し、サンプルとして使用するハブを見つけます。 新しい通知ハブの作成については、[Windows ストアの使用に関するチュートリアル](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)を参照することができます。
3. **[設定]** を選択します。
4. **[アクセス ポリシー]** をクリックします。 共有接続文字列とフル アクセス接続文字列の両方が表示されます。

![Azure Portal - Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> また、[Azure PowerShell](/powershell/azureps-cmdlets-docs) に用意されている **Get-AzureSbNamespace** コマンドレット、または [Azure コマンド ライン インターフェイス (Azure CLI)](../cli-install-nodejs.md) で **azure sb namespace show** コマンドを使用して、接続文字列を取得することもできます。

## <a name="general-architecture"></a>全般的なアーキテクチャ

`NotificationHubService` オブジェクトは、特定のデバイスやアプリケーションにプッシュ通知を送信するために、次のオブジェクト インスタンスを公開します。

- **Android** - `notificationHubService.gcm` で使用できる `GcmService` オブジェクトを使用します
- **iOS** - `notificationHubService.apns` でアクセスできる `ApnsService` オブジェクトを使用します
- **Windows Phone** - `notificationHubService.mpns` で使用できる `MpnsService` オブジェクトを使用します
- **ユニバーサル Windows プラットフォーム** - `notificationHubService.wns` で使用できる `WnsService` オブジェクトを使用します

### <a name="how-to-send-push-notifications-to-android-applications"></a>方法:Android アプリケーションにプッシュ通知を送信する

`GcmService` オブジェクトには、Android アプリケーションにプッシュ通知を送信するために使用できる `send` メソッドが用意されています。 `send` メソッドは、次のパラメーターを受け取ります。

- **Tags** - タグ識別子。 タグが指定されない場合、通知はすべてのクライアントに送信されます。
- **Payload** - メッセージの JSON または未加工の文字列ペイロード
- **Callback** - コールバック関数。

ペイロード形式の詳細については、[ペイロードに関するドキュメント](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload)を参照してください。

次のコードは、`NotificationHubService` によって公開されている `GcmService` インスタンスを使用して、登録されているすべてのクライアントにプッシュ通知を送信します。

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>方法:iOS アプリケーションにプッシュ通知を送信する

上で説明した Android アプリケーションと同様に、`ApnsService` オブジェクトには、iOS アプリケーションにプッシュ通知を送信するために使用できる `send` メソッドが用意されています。 `send` メソッドは、次のパラメーターを受け取ります。

- **Tags** - タグ識別子。 タグが指定されない場合、通知はすべてのクライアントに送信されます。
- **Payload** - メッセージの JSON または文字列ペイロード。
- **Callback** - コールバック関数。

ペイロード形式の詳細については、「 **Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)** 」ドキュメントの「 [Notification Payload (通知ペイロード)](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) 」のセクションを参照してください。

次のコードは、`NotificationHubService` によって公開されている `ApnsService` インスタンスを使用して、すべてのクライアントにアラート メッセージを送信します。

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>方法:Windows Phone アプリケーションにプッシュ通知を送信する

`MpnsService` オブジェクトには、Windows Phone アプリにプッシュ通知を送信するために使用できる `send` メソッドが用意されています。 `send` メソッドは、次のパラメーターを受け取ります。

- **Tags** - タグ識別子。 タグが指定されない場合、通知はすべてのクライアントに送信されます。
- **Payload** - メッセージの XML ペイロード。
- **TargetName** -  トースト通知の場合は `toast`。 `token` 。
- **NotificationClass** - 通知の優先度。 有効な値については、「 **Push notifications from a server (サーバーからのプッシュ通知)** 」の「 [HTTP Header Elements](https://msdn.microsoft.com/library/hh221551.aspx) (HTTP ヘッダー要素)」のセクションを参照してください。
- **Options** - 省略可能な要求ヘッダー。
- **Callback** - コールバック関数。

有効な `TargetName`、`NotificationClass`、ヘッダー オプションについては、[サーバーからのプッシュ通知](https://msdn.microsoft.com/library/hh221551.aspx)に関するページを参照してください。

次のサンプル コードは、`NotificationHubService` によって公開されている `MpnsService` インスタンスを使用して、トースト プッシュ通知を送信します。

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>方法:ユニバーサル Windows プラットフォーム (UWP) アプリケーションにプッシュ通知を送信する

`WnsService` オブジェクトには、ユニバーサル Windows プラットフォーム アプリにプッシュ通知を送信するために使用できる `send` メソッドが用意されています。  `send` メソッドは、次のパラメーターを受け取ります。

- **Tags** - タグ識別子。 タグが指定されない場合、通知は登録されているすべてのクライアントに送信されます。
- **Payload** - XML メッセージ ペイロード。
- **Type** - 通知の種類。
- **Options** - 省略可能な要求ヘッダー。
- **Callback** - コールバック関数。

有効なタイプと要求ヘッダーの一覧については、「 [プッシュ通知サービスの要求ヘッダーと応答ヘッダー](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx)」を参照してください。

次のコードは、`NotificationHubService` によって公開されている `WnsService` インスタンスを使用して、トースト プッシュ通知を UWP アプリに送信します。

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>次の手順

上記のサンプル スニペットを使用すると、さまざまなデバイスにプッシュ通知を提供するサービス インフラストラクチャを簡単に構築できます。 これで、node.js による Notification Hubs の使用の基本を学習できました。さらに次のリンクを使用して、これらの機能を拡張する方法の詳細を学習してください。

- MSDN リファレンス: [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx)
- 他のサンプルと実装の詳細については、GitHub の [Azure SDK for Node] リポジトリを参照してください。

[Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
