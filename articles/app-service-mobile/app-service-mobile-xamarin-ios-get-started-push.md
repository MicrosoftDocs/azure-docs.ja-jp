---
title: "Azure App Service を使用して Xamarin iOS アプリにプッシュ通知を追加する"
description: "Azure App Service を使用して Xamarin iOS アプリにプッシュ通知を送信する方法について説明します"
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: adrianha
editor: 
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 185a32376dd1c40fd480e16d3ac7607ac798afae
ms.lasthandoff: 11/17/2016


---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Xamarin iOS アプリへのプッシュ通知の追加
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要
このチュートリアルでは、[Xamarin.iOS クイック スタート](app-service-mobile-xamarin-ios-get-started.md) プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージを追加する必要があります。 詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
* [Xamarin.iOS のクイックスタート](app-service-mobile-xamarin-ios-get-started.md) に関するチュートリアルを完了していること。
* 物理的な iOS デバイス iOS シミュレーターでは、プッシュ通知はサポートされていません。

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Apple の開発者ポータルにプッシュ通知のアプリを登録する
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>プッシュ通知を送信するようにモバイル アプリを構成する
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>プッシュ通知を送信するようにサーバー プロジェクトを更新する
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Xamarin.iOS プロジェクトを構成する
[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>アプリケーションにプッシュ通知を追加する
1. **QSTodoService** で次のプロパティを追加して、**AppDelegate** でモバイル クライアントを取得できるようにします。
   
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }
2. 次の `using` ステートメントを **AppDelegate.cs** ファイルの先頭に追加します。
   
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
3. **AppDelegate** で、**FinishedLaunching** イベントをオーバーライドします。
   
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());
   
            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
   
            return true;
        }
4. 同じファイルで **RegisteredForRemoteNotifications** イベントをオーバーライドします。 このコードでは、サーバーでサポートされているすべてのプラットフォームに送信される単純なテンプレート通知を登録します。
   
    Notification Hubs を使用するテンプレートの詳細については、「 [テンプレート](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)」を参照してください。

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


1. 次に、 **DidReceivedRemoteNotification** イベントをオーバーライドします。
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;
   
            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();
   
            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

## <a name="test"></a>アプリケーションでプッシュ通知をテストする
1. **[実行]** を押してプロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。
   
   > [!NOTE]
   > アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。 これが必要であるのは、初めてアプリケーションを実行するときだけです。
   > 
   > 
2. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。
3. 通知が受信されたことを確認し、 **[OK]** をクリックして通知を破棄します。
4. 手順 2. を繰り返してすぐにアプリケーションを閉じたら、通知が表示されることを確認します。

これで、このチュートリアルは終了です。

<!-- Images. -->

<!-- URLs. -->




