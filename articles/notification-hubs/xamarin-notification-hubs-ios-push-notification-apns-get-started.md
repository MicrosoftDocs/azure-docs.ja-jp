---
title: "Xamarin アプリ用通知ハブを使用した iOS Push Notifications | Microsoft Docs"
description: "このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.iOS アプリケーションにプッシュ通知を送信する方法について学習します。"
services: notification-hubs
keywords: "ios push notifications、プッシュ メッセージ、プッシュ通知、プッシュ メッセージ"
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 53f9d5b37e6f754540e70b534555750dd54895d0


---
# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>Xamarin アプリ用通知ハブを使用した iOS Push Notifications
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
> [!IMPORTANT]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)を参照してください。
> 
> 

このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。
[Apple Push Notification サービス (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)を使用してプッシュ通知を受信する空の Xamarin.iOS アプリケーションを作成します。 完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。 完成したコードは、[NotificationHubs アプリ][GitHub] サンプルで参照できます。

このチュートリアルでは、Notification Hubs を使用した簡単なプッシュ メッセージ ブロードキャスト シナリオのデモンストレーションを行います。

## <a name="prerequisites"></a>前提条件
このチュートリアルには、次のものが必要です。

* [Xcode 6.0][Install Xcode]
* iOS 7.0 以降のバージョンに対応するデバイス
* iOS Developer Program メンバーシップ
* [Xamarin Studio]
  
  > [!NOTE]
  > iOS Push Notifications の構成要件に従って、サンプル アプリケーションのデプロイとテストは、シミュレーターではなく、物理的な iOS デバイス (iPhone または iPad) で行う必要があります。
  > 
  > 

このチュートリアルを完了することは、Xamarin.iOS アプリの他のすべての Notification Hubs チュートリアルの前提条件です。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub"></a>通知ハブを構成する
このセクションでは、作成した **.p12** プッシュ証明書を使用して、新しい通知ハブを作成し、APNS での認証を構成する方法について説明します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">

<li>

<p>APNS 接続を構成するため、Azure Portal で [通知ハブ] 設定を開き、<b>[Notification Services]</b> をクリックし、リストの <b>[Apple (APNS)]</b> の項目をクリックします。 次に <b>[証明書のアップロード]</b> をクリックし、エクスポートした <b>.p12</b> 証明書を選択し、証明書のパスワードを入力します。</p>

<p>開発環境でプッシュ メッセージを送信するので、<b>[サンドボックス]</b> モードをオンにします。 <b>[実稼働]</b> 設定は、ストアから既にアプリを購入しているユーザーにプッシュ通知を送信する場合にのみ使用します。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)

これで、通知ハブが APNS と連動するように構成されました。接続文字列にアプリケーションを登録し、プッシュ通知を送信できます。

## <a name="connect-your-app-to-the-notification-hub"></a>通知ハブにアプリケーションを接続する
#### <a name="create-a-new-project"></a>新しいプロジェクトを作成する
1. Xamarin Studio で、新しい iOS プロジェクトを作成し、**[Unified API (統合 API)]** > **[Single View Application (単一ビュー アプリケーション)]** テンプレートを選択します。
   
       ![Xamarin Studio - Select Application Type][31]
2. Azure Messaging コンポーネントへの参照を追加します。 [Solution (ソリューション)] ビューで、プロジェクトの **[Components (コンポーネント)]** フォルダーを右クリックし、**[Get More Components (他のコンポーネントを取得する)]** を選択します。 **Azure Messaging** コンポーネントを検索し、そのコンポーネントをプロジェクトに追加します。
3. **AppDelegate.cs**で、次の using ステートメントを追加します。
   
        using WindowsAzure.Messaging;
4. **SBNotificationHub**のインスタンスを宣言します。
   
        private SBNotificationHub Hub { get; set; }
5. 次の変数を使用して **Constants.cs** クラスを作成します。
   
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
6. **AppDelegate.cs** で、**FinishedLaunching()** を更新して次の内容に合わせます。
   
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
7. **AppDelegate.cs** で **RegisteredForRemoteNotifications()** メソッドをオーバーライドします。
   
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
8. **AppDelegate.cs** で **ReceivedRemoteNotification()** メソッドをオーバーライドします。
   
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
9. **AppDelegate.cs** で次の **ProcessNotification()** メソッドを作成します。
   
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
   
   > [!NOTE]
   > ネットワーク接続がないなどの状況に対処するために、 **FailedToRegisterForRemoteNotifications()** をオーバーライドすることを選択できます。 この方法は、ユーザーが (飛行機内などで) オフライン モードで起動した場合に備えて、アプリ独自のプッシュ メッセージ シナリオを処理する場合に特に重要です。
   > 
   > 
10. デバイスでアプリケーションを実行します。

## <a name="sending-push-notifications"></a>プッシュ通知を送信する
[Azure Portal] の **[トラブルシューティング]** ツールセットの **[テスト送信]** で通知を送信して、アプリでプッシュ通知をテスト受信することができます。[トラブルシューティング] は、次の画面のように通知ハブのページにあります。

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

通常、プッシュ通知は、互換性のあるライブラリを使用し、Mobile Services などのバックエンド サービスや ASP.NET を経由して送信されます。 実際のシナリオでライブラリを利用できない場合、REST API を使用してプッシュ メッセージを直接送信できます。 

このチュートリアルでは、バックエンド サービスではなく、コンソール アプリケーションの通知ハブに .NET SDK を使用して通知を送信することで例を単純にして、クライアント アプリのテスト手順のみを説明します。 ASP.NET バックエンドから通知を送信するには、次のステップとして「 [Notification Hubs を使用したユーザーへのプッシュ通知](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) 」を参照することをお勧めします。 ただし、通知の送信には、次の方法も使用できます。

* **REST インターフェイス**: [REST インターフェイス](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)を使用すると、任意のバックエンド プラットフォームのプッシュ通知をサポートできます。
* **Microsoft Azure Notification Hubs .NET SDK**: Nuget Package Manager for Visual Studio で、 [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)を実行します。
* **Node.js** : [Node.js から Notification Hubs を使用する方法](notification-hubs-nodejs-push-notification-tutorial.md)。

**Mobile Apps**: Notification Hubs に統合されている Azure App Service Mobile Apps のバックエンドから通知を送信する方法の例については、[モバイル アプリにプッシュ通知を追加](../app-service-mobile/app-service-mobile-ios-get-started-push.md)する方法を説明したページを参照してください。

* **Java/PHP**: REST API を使用したプッシュ通知の送信方法の例については、「Java/PHP から Notification Hubs を使用する方法」([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)) を参照してください。

#### <a name="optional-send-push-notifications-from-a-net-console-app"></a>(省略可能) .NET コンソール アプリケーションからプッシュ通知を送信する
このセクションでは、シンプルな .NET コンソール アプリケーションを使用してプッシュ通知を送信します。 この例の目的に合わせて、Visual Studio が既にインストールされている Windows 開発環境に切り替えます。

1. Visual Studio で、Visual C# の新しいコンソール アプリケーションを作成します。
   
       ![Visual Studio - Create a new console application][213]
2. Visual Studio で、**[ツール]**、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。
   
    Visual Studio ワークスペースの下部にドッキングされた状態でパッケージ マネージャー コンソールが表示されます。
3. パッケージ マネージャー コンソール ウィンドウで **[既定のプロジェクト]** に新しいコンソール アプリケーション プロジェクトを設定した後、そのコンソール ウィンドウから次のコマンドを実行します。
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    これにより <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet パッケージ</a>を利用して Azure Notification Hubs SDK に参照が追加されます。
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
4. `Program.cs` ファイルを開き、次の `using` ステートメントを追加して、メイン クラス内で Azure のクラスと関数を使用できるようにします。
   
        using Microsoft.Azure.NotificationHubs;
5. `Program` クラスに、次のメソッドを追加します (**接続文字列**と**ハブ名**は必ず書き換えてください)。
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }
6. `Main` メソッド内に次の行を追加します。
   
         SendNotificationAsync();
         Console.ReadLine();
7. F5 キーを押してアプリケーションを実行します。 数秒後に、プッシュ通知がデバイスに表示されます。 Wi-Fi と携帯電話データ ネットワークのいずれを使用している場合でも、デバイスのインターネット接続がアクティブである必要があります。

Apple の「 [Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]」に、使用できるすべてのペイロードが記載されています。

#### <a name="optional-send-notifications-from-a-mobile-service"></a>(省略可能) モバイル サービスから通知を送信する
このセクションでは、モバイル サービスを使用してノード スクリプトからプッシュ通知を送信します。

モバイル サービスを使用して通知を送信するには、「 [Mobile Services の使用]」に従った後、次の手順を実行します。

1. [Azure クラシック ポータル]にサインインし、モバイル サービスを選択します。
2. 上部にある **[Scheduler]** タブを選択します。
   
       ![Azure Classic Portal - Scheduler][215]
3. スケジュールされた新しいジョブを作成して名前を挿入し、 **[要求時]**をクリックします。
   
       ![Azure Classic Portal - Create new job][216]
4. ジョブが作成されたら、ジョブ名をクリックします。 上部のバーにある **[スクリプト]** タブをクリックします。
5. スケジューラ関数内に次のスクリプトを挿入します。 必ず、プレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えてください。 **[保存]**をクリックします。
   
        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                  "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );
6. 下部のバーにある **[一度だけ実行する]** をクリックします。 デバイスでアラートを受信します。

## <a name="next-steps"></a>次のステップ
この簡単な例では、すべての iOS デバイスにプッシュ通知をブロードキャストします。 特定のユーザーをターゲットとするには、「 [Notification Hubs を使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。 対象グループごとにユーザーを区分する場合は、「 [Notification Hubs を使用したニュース速報の送信]」を参照してください。 Notification Hubs の使用方法の詳細については、「[Notification Hubs の概要]」と [iOS 向けの Notification Hubs の使用方法]に関する記事を参照してください。

<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Mobile Services の使用]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS 向けの Notification Hubs の使用方法]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS プロビジョニング ポータル]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Notification Hubs を使用したユーザーへのプッシュ通知]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs を使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Azure Portal]: https://portal.azure.com



<!--HONumber=Nov16_HO2-->


