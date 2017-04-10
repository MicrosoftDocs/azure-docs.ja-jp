---
title: "Xamarin.Forms アプリへのプッシュ通知の追加 | Microsoft Docs"
description: "Azure サービスを使用して Xamarin.Forms アプリにマルチプラットフォーム プッシュ通知を送信する方法について説明します。"
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: adrianha
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f1fe97c6b3e2d28b7e17d035bc7e3ecced8a0d0f
ms.lasthandoff: 03/21/2017


---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Xamarin.Forms アプリにプッシュ通知を追加する
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要
このチュートリアルでは、[Xamarin.Forms クイック スタート](app-service-mobile-xamarin-forms-get-started.md)で作成したすべてのプロジェクトにプッシュ通知を追加します。 つまり、レコードが挿入されるたびにすべてのクロスプラットフォーム クライアントに対してプッシュ通知が送信されるようにします。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージを追加する必要があります。 詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
iOS については、[Apple Developer Program メンバーシップ](https://developer.apple.com/programs/ios/)と物理 iOS デバイスが必要です。 [iOS シミュレーターでは、プッシュ通知はサポートされていません](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。

## <a name="configure-hub"></a>通知ハブを構成する
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>プッシュ通知を送信するようにサーバー プロジェクトを更新する
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Android プロジェクトを構成して実行する (省略可能)
このセクションを完了すると、Android 用の Xamarin.Forms Droid プロジェクトのプッシュ通知を有効にすることができます。

### <a name="enable-firebase-cloud-messaging-fcm"></a>Firebase Cloud Messaging (FCM) を有効にする
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>FCM を使用してプッシュ要求を送信するように Mobile Apps バックエンドを構成する
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Android プロジェクトにプッシュ通知を追加する
FCM を使用してバックエンドを構成すると、コンポーネントとコードをクライアントに追加して FCM に登録できます。 Mobile Apps バックエンドを通じて Azure Notification Hubs によるプッシュ通知に登録し、通知を受信することもできます。

1. **Droid** プロジェクトで **Components** フォルダーを右クリックし、**[コンポーネントをさらに取得する...]** をクリックします。 "**Google Cloud Messaging Client**" というコンポーネントを検索し、それをプロジェクトに追加します。 このコンポーネントは、Xamarin Android プロジェクトのプッシュ通知をサポートします。
2. MainActivity.cs プロジェクト ファイルを開き、次のステートメントをファイルの先頭に追加します。

        using Gcm.Client;
3. **LoadApplication** の呼び出し後、次のコードを **OnCreate** メソッドに追加します。

        try
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }
4. 次のように、新しい **CreateAndShowDialog** ヘルパーを追加します。

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. **MainActivity** クラスに次のコードを追加します。

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    これにより、現在の **MainActivity** インスタンスが公開され、メイン UI スレッドで実行できるようになります。
6. **OnCreate** メソッドの先頭にある変数 `instance` を次のように初期化します。

        // Set the current instance of MainActivity.
        instance = this;
7. `GcmService.cs` という名前の **Droid** プロジェクトに新しいクラス ファイルを追加します。次の **using** ステートメントはファイルの先頭に配置してください。

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;
8. ファイルの先頭の **using** ステートメントと **namespace** 宣言の間に、次のアクセス許可要求を追加します。

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. 名前空間に次のクラス定義を追加します。

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > **<PROJECT_NUMBER>** を、前にメモしたプロジェクト番号に置き換えます。    
   >
   >
10. 空の **GcmService** クラスを、新しいブロードキャスト レシーバーを使用する次のコードに置き換えます。

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. 次のコードを **GcmService** クラスに追加します。 これにより、**OnRegistered**イベント ハンドラーが上書きされ、**Register** メソッドが実装されます。

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

    このコードは、テンプレート登録で `messageParam` パラメーターを使用します。
12. **OnMessage**を実装する次のコードを追加します。

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    これにより、受信した通知が処理され、表示される通知マネージャーに送信されます。
13. また、**GcmServiceBase** では、**OnUnRegistered** および **OnError** ハンドラー メソッドを実装する必要があります。これらは、次のように実装できます。

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

これで、Android デバイスまたはエミュレーターで実行されているアプリでプッシュ通知をテストする準備が整いました。

### <a name="test-push-notifications-in-your-android-app"></a>Android アプリでプッシュ通知をテストする
最初の 2 つの手順は、エミュレーターでテストする場合にのみ必要です。

1. 次に示すように Android Virtual Device Manager で Google API がターゲットとして設定された仮想デバイスに対してデプロイまたはデバッグする必要があります。
2. **[アプリ]** > **[設定]** > **[アカウントの追加]** をクリックして Android デバイスに Google アカウントを追加します。 プロンプトに従って既存の Google アカウントをデバイスに追加するか、新たにアカウントを作成します。
3. Visual Studio または Xamarin Studio で、**Droid** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
4. **[実行]** をクリックしてプロジェクトをビルドし、Android デバイスまたはエミュレーターでアプリを起動します。
5. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。
6. 項目が追加されたときに、通知が受信されていることを確認します。

## <a name="configure-and-run-the-ios-project-optional"></a>iOS プロジェクトを構成して実行する (省略可能)
このセクションでは、iOS デバイス用の Xamarin iOS プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>APNS 用に通知ハブを構成する
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

次に、Xamarin Studio または Visual Studio で iOS のプロジェクト設定を構成します。

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>iOS アプリへのプッシュ通知の追加
1. **iOS** プロジェクトで AppDelegate.cs を開き、次のステートメントをコード ファイルの先頭に追加します。

        using Newtonsoft.Json.Linq;
2. **AppDelegate** クラスで、通知に登録するために **RegisteredForRemoteNotifications** イベントのオーバーライドを追加します。

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. **AppDelegate** に次の **DidReceiveRemoteNotification** イベント ハンドラーのオーバーライドも追加します。

        public override void DidReceiveRemoteNotification(UIApplication application,
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    このメソッドは、アプリの実行中に受信した通知を処理します。
4. **AppDelegate** クラスの **FinishedLaunching** メソッドに次のコードを追加します。

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    これによってリモート通知のサポートが有効になり、プッシュ登録の要求が行われます。

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

#### <a name="test-push-notifications-in-your-ios-app"></a>iOS アプリでプッシュ通知をテストする
1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
2. Visual Studio で **[実行]** または **F5** キーを押してプロジェクトをビルドし、iOS デバイスでアプリを起動します。 **[OK]** をクリックして、プッシュ通知を受け入れます。

   > [!NOTE]
   > アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。 これが必要であるのは、初めてアプリケーションを実行するときだけです。
   >
   >
3. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。
4. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

## <a name="configure-and-run-windows-projects-optional"></a>Windows プロジェクトを構成して実行する (省略可能)
このセクションでは、Windows デバイス用の Xamarin.Forms WinApp プロジェクトと Xamarin.Forms WinPhone81 プロジェクトを実行します。 次の手順では、ユニバーサル Windows プラットフォーム (UWP) プロジェクトもサポートされています。 Windows デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Windows Notification Service (WNS) によるプッシュ通知に Windows アプリを登録する
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS 用に通知ハブを構成する
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Windows アプリにプッシュ通知を追加する
1. Visual Studio で、Windows プロジェクトの **App.xaml.cs** を開き、次のステートメントを追加します。

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    `<your_TodoItemManager_portable_class_namespace>` を、`TodoItemManager` クラスが含まれているポータブル プロジェクトの名前空間に置き換えます。
2. App.xaml.cs で、次の **InitNotificationsAsync** メソッドを追加します。

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS =
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    このメソッドによって、プッシュ通知チャネルが取得され、対象の通知ハブからテンプレート通知を受け取るためのテンプレートが登録されます。 *messageParam* をサポートするテンプレート通知がこのクライアントに配信されるようになります。
3. App.xaml.cs で、`async` 修飾子を追加して、**OnLaunched** イベント ハンドラー メソッドの定義を更新します。 次に、メソッドの末尾に次のコード行を追加します。

        await InitNotificationsAsync();

    これにより、アプリの起動時に毎回プッシュ通知登録が作成または更新されるようになります。 これを行うことは、WNS プッシュ チャネルが常にアクティブであることを保証するために重要です。  
4. Visual Studio のソリューション エクスプローラーで、**Package.appxmanifest** ファイルを開き、**[通知]** で **[トースト対応]** を **[はい]** に設定します。
5. アプリをビルドし、エラーがないことを確認します。 これで、クライアント アプリが、Mobile Apps バックエンドから送信されるテンプレート通知に登録されました。 ソリューションのすべての Windows プロジェクトについて、このセクションを繰り返します。

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows アプリでプッシュ通知をテストする
1. Visual Studio で、Windows プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
2. **[実行]** ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。
3. アプリで新しい todoitem の名前を入力し、プラス (**+**) アイコンをクリックして追加します。
4. 項目が追加されたときに、通知が受信されていることを確認します。

## <a name="next-steps"></a>次のステップ
プッシュ通知についてさらに学ぶことができます。

* [プッシュ通知の問題の診断](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  通知が破棄されたり、デバイスに届かなかったりするのにはさまざまな原因があります。 このトピックでは、プッシュ通知のエラーの根本原因を分析、解明する方法について説明しています。

次のチュートリアルのいずれかに進むこともできます。

* [アプリへの認証の追加 ](app-service-mobile-xamarin-forms-get-started-users.md)  
  ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Mobile Apps バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

