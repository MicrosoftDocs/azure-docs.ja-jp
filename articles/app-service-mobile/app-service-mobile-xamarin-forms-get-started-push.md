---
title: Xamarin.Forms アプリへのプッシュ通知の追加 | Microsoft Docs
description: Azure サービスを使用して Xamarin.Forms アプリにマルチプラットフォーム プッシュ通知を送信する方法について説明します。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b7e2ff63211ec5891a48a585e4f69e18116cdeb3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446562"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Xamarin.Forms アプリにプッシュ通知を追加する

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーとかかわることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-push) を今すぐチェックしてください。
>

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

1. **Droid** プロジェクトで、 **[参照] > [NuGet パッケージの管理...]** の順に右クリックします。
1. [NuGet パッケージ マネージャー] ウィンドウで **[Xamarin.Firebase.Messaging]** パッケージを検索して、プロジェクトに追加します。
1. **Droid** プロジェクトのプロジェクト プロパティで、Android バージョン 7.0 以降を使用してコンパイルするようにアプリを設定します。
1. Firebase コンソールからダウンロードした **google-services.json** ファイルを **Droid** プロジェクトのルートに追加して、ビルド アクションを **GoogleServicesJson** に設定します。 詳細については、[Google Services JSON ファイルの追加](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File)に関する記事をご覧ください。

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging の登録

1. **AndroidManifest.xml** ファイルを開き、次の `<receiver>` 要素を `<application>` 要素に挿入します。

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Firebase インスタンス ID サービスの実装

1. `FirebaseRegistrationService` という名前の **Droid** プロジェクトに新しいクラスを追加します。次の `using` ステートメントはファイルの先頭に配置してください。

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. 空の `FirebaseRegistrationService` クラスを次のコードに置き換えます。

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    `FirebaseRegistrationService` クラスは、FCM にアクセスするアプリケーションを認証するセキュリティ トークンを生成する役割を担います `OnTokenRefresh` メソッドは、アプリケーションが FCM から登録トークンを受信するときに呼び出されます。 メソッドは、FCM によって非同期で更新された `FirebaseInstanceId.Instance.Token` プロパティからトークンを受信します。 トークンはアプリケーションがインストールまたはアンインストールされたとき、ユーザーがアプリケーション データを削除したとき、アプリケーションがインスタンス ID を消去したとき、またはトークンのセキュリティが侵害されたときにだけ更新されるため、`OnTokenRefresh` メソッドが呼び出される頻度は高くありません。 さらに、FCM インスタンス ID サービスでは、アプリケーションによって定期的にトークンが更新されること (通常は 6 か月ごと) を必要とします。

    また、`OnTokenRefresh` メソッドは `SendRegistrationTokenToAzureNotificationHub` メソッドを呼び出します。このメソッドは、ユーザーの登録トークンを Azure Notification Hub と関連付けるために使用されます。

#### <a name="registering-with-the-azure-notification-hub"></a>Azure Notification Hub による登録

1. `AzureNotificationHubService` という名前の **Droid** プロジェクトに新しいクラスを追加します。次の `using` ステートメントはファイルの先頭に配置してください。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. 空の `AzureNotificationHubService` クラスを次のコードに置き換えます。

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    `RegisterAsync` メソッドは単純な通知メッセージ テンプレートを JSON として作成し、Firebase 登録トークンを使用して通知ハブからテンプレート通知を受け取るように登録を行います。 これにより、Azure Notification Hub から送信された通知はすべて、登録トークンによって提示されたデバイスを対象とします。

#### <a name="displaying-the-contents-of-a-push-notification"></a>プッシュ通知の内容の表示

1. `FirebaseNotificationService` という名前の **Droid** プロジェクトに新しいクラスを追加します。次の `using` ステートメントはファイルの先頭に配置してください。

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. 空の `FirebaseNotificationService` クラスを次のコードに置き換えます。

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    アプリケーションが FCM から通知を受け取るときに呼び出される `OnMessageReceived` メソッドは、メッセージのコンテンツを抽出し、`SendNotification` メソッドを呼び出します。 このメソッドは、通知領域に表示された通知を使用して、メッセージのコンテンツを、アプリケーションの実行中に起動されたローカル通知に変換します。

これで、Android デバイスまたはエミュレーターで実行されているアプリでプッシュ通知をテストする準備が整いました。

### <a name="test-push-notifications-in-your-android-app"></a>Android アプリでプッシュ通知をテストする

最初の 2 つの手順は、エミュレーターでテストする場合にのみ必要です。

1. 必ず Google Play 開発者サービスで構成されたデバイスまたはエミュレーターに対してデプロイまたはデバッグします。 これを確かめるには、**Play** アプリがデバイスまたはエミュレーターにインストールされていることを確認します。
2. **[アプリ]**  >  **[設定]**  >  **[アカウントの追加]** をクリックして Android デバイスに Google アカウントを追加します。 プロンプトに従って既存の Google アカウントをデバイスに追加するか、新たにアカウントを作成します。
3. Visual Studio または Xamarin Studio で、**Droid** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** をクリックします。
4. **[実行]** をクリックしてプロジェクトをビルドし、Android デバイスまたはエミュレーターでアプリを起動します。
5. アプリケーションで、タスクを入力し、プラス ( **+** ) アイコンをクリックします。
6. 項目が追加されたときに、通知が受信されていることを確認します。

## <a name="configure-and-run-the-ios-project-optional"></a>iOS プロジェクトを構成して実行する (省略可能)

このセクションでは、iOS デバイス用の Xamarin iOS プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>APNS 用に通知ハブを構成する

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

次に、Xamarin Studio または Visual Studio で iOS のプロジェクト設定を構成します。

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>iOS アプリへのプッシュ通知の追加

1. **iOS** プロジェクトで AppDelegate.cs を開き、次のステートメントをコード ファイルの先頭に追加します。

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. **AppDelegate** クラスで、通知に登録するために **RegisteredForRemoteNotifications** イベントのオーバーライドを追加します。

    ```csharp
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
    ```

3. **AppDelegate** に次の **DidReceiveRemoteNotification** イベント ハンドラーのオーバーライドも追加します。

    ```csharp
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
    ```

    このメソッドは、アプリの実行中に受信した通知を処理します。

4. **AppDelegate** クラスの **FinishedLaunching** メソッドに次のコードを追加します。

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    これによってリモート通知のサポートが有効になり、プッシュ登録の要求が行われます。

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

#### <a name="test-push-notifications-in-your-ios-app"></a>iOS アプリでプッシュ通知をテストする

1. iOS プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** をクリックします。
2. Visual Studio で **[実行]** または **F5** キーを押してプロジェクトをビルドし、iOS デバイスでアプリを起動します。 **[OK]** をクリックして、プッシュ通知を受け入れます。

   > [!NOTE]
   > アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。 これが必要であるのは、初めてアプリケーションを実行するときだけです。

3. アプリケーションで、タスクを入力し、プラス ( **+** ) アイコンをクリックします。
4. 通知が受信されたことを確認し、 **[OK]** をクリックして通知を破棄します。

## <a name="configure-and-run-windows-projects-optional"></a>Windows プロジェクトを構成して実行する (省略可能)

このセクションでは、Windows デバイス用の Xamarin.Forms WinApp プロジェクトと Xamarin.Forms WinPhone81 プロジェクトを実行します。 次の手順では、ユニバーサル Windows プラットフォーム (UWP) プロジェクトもサポートされています。 Windows デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Windows Notification Service (WNS) によるプッシュ通知に Windows アプリを登録する

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS 用に通知ハブを構成する

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Windows アプリにプッシュ通知を追加する

1. Visual Studio で、Windows プロジェクトの **App.xaml.cs** を開き、次のステートメントを追加します。

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    `<your_TodoItemManager_portable_class_namespace>` を、`TodoItemManager` クラスが含まれているポータブル プロジェクトの名前空間に置き換えます。

2. App.xaml.cs で、次の **InitNotificationsAsync** メソッドを追加します。

    ```csharp
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
    ```

    このメソッドによって、プッシュ通知チャネルが取得され、対象の通知ハブからテンプレート通知を受け取るためのテンプレートが登録されます。 *messageParam* をサポートするテンプレート通知がこのクライアントに配信されるようになります。

3. App.xaml.cs で、`async` 修飾子を追加して、**OnLaunched** イベント ハンドラー メソッドの定義を更新します。 次に、メソッドの末尾に次のコード行を追加します。

    ```csharp
    await InitNotificationsAsync();
    ```

    これにより、アプリの起動時に毎回プッシュ通知登録が作成または更新されるようになります。 これを行うことは、WNS プッシュ チャネルが常にアクティブであることを保証するために重要です。  

4. Visual Studio のソリューション エクスプローラーで、**Package.appxmanifest** ファイルを開き、 **[通知]** で **[トースト対応]** を **[はい]** に設定します。
5. アプリをビルドし、エラーがないことを確認します。 これで、クライアント アプリが、Mobile Apps バックエンドから送信されるテンプレート通知に登録されました。 ソリューションのすべての Windows プロジェクトについて、このセクションを繰り返します。

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows アプリでプッシュ通知をテストする

1. Visual Studio で、Windows プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** をクリックします。
2. **[実行]** ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。
3. アプリで新しい todoitem の名前を入力し、プラス ( **+** ) アイコンをクリックして追加します。
4. 項目が追加されたときに、通知が受信されていることを確認します。

## <a name="next-steps"></a>次の手順

プッシュ通知についてさらに学ぶことができます。

* [Azure Mobile Apps からプッシュ通知を送信する](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase クラウド メッセージング](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Firebase クラウド メッセージングによるリモート通知](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [プッシュ通知の問題の診断](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  通知が破棄されたり、デバイスに届かなかったりするのにはさまざまな原因があります。 このトピックでは、プッシュ通知のエラーの根本原因を分析、解明する方法について説明しています。

次のチュートリアルのいずれかに進むこともできます。

* [アプリへの認証の追加](app-service-mobile-xamarin-forms-get-started-users.md)  
  ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Mobile Apps バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
