---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: de961aa36d690cf03e42707758bc70e5495f692e
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448758"
---
### <a name="validate-package-name-and-permissions"></a>パッケージ名とアクセス許可を検証する

1. **PushDemo.Android** で、 **[Project Options] (プロジェクト オプション)** を開き、 **[ビルド]** セクションから **[Android アプリケーション]** を開きます。

1. **パッケージ名**が、[Firebase Console](https://console.firebase.google.com) の **PushDemo** プロジェクトで使用した値と一致していることを確認します。 **パッケージ名**は、``com.<organization>.pushdemo`` という形式でした。

1. **[Minimum Android Version] (最小 Android バージョン)** を **[Android 8.0 (API level 26)] (Android 8.0 (API レベル 26))** に設定し、 **[Target Android Version] (対象の Android バージョン)** を最新の **API レベル**に設定します。

    > [!NOTE]
    > このチュートリアルでは、**API レベル 26 以上**が実行されているデバイスのみがサポートされていますが、より前のバージョンを実行しているデバイスをサポートするように拡張できます。

1. **[必要なアクセス許可]** で、 **[INTERNET]** と **[READ_PHONE_STATE]** のアクセス許可が有効になっていることを確認します。

1. **[OK]**

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Xamarin Google Play 開発者サービス ベース パッケージと Xamarin.Messaging パッケージを追加する

1. **PushDemo.Android** で、**Packages** フォルダーを **Control** + **クリック**し、 **[NuGet パッケージの管理...]** を選択します。

1. **Xamarin.GooglePlayServices.Base** (**Basement** ではありません) を探し、チェック ボックスがオンになっていることを確認します。

1. **Xamarin.Firebase.Messaging** を探し、チェック ボックスがオンになっていることを確認します。

1. **[パッケージの追加]** をクリックし、**ライセンス条項**への同意を求めるメッセージが表示されたら、 **[同意する]** をクリックします。

### <a name="add-the-google-services-json-file"></a>Google Services JSON ファイルを追加する

1. `PushDemo.Android` プロジェクトを **Control** + **クリック**し、 **[追加]** メニューから **[既存のファイル...]** を選択します。

1. [Firebase Console](https://console.firebase.google.com) で以前 **PushDemo** プロジェクトを設定したときにダウンロードした *google-services.json* ファイルを選択し、 **[開く]** をクリックします。

1. メッセージが表示されたら、**ファイルをディレクトリにコピーする**ように選択します。

1. `PushDemo.Android` プロジェクト内からの *google-services.json* を **Control** + **クリック**して、**GoogleServicesJson** が**ビルド アクション**として設定されていることを確認します。

### <a name="handle-push-notifications-for-android"></a>Android 用のプッシュ通知を処理する

1. `PushDemo.Android` プロジェクトを **Control** + **クリック**し、 **[追加]** メニューから **[新しいフォルダー]** を選択します。次に、 **[フォルダー名]** として「*Services*」を使用して **[追加]** をクリックします。

1. **Services** フォルダーを **Control** + **クリック**し、 **[追加]** メニューから **[新しいファイル...]** を選択します。

1. **[全般]**  >  **[空のクラス]** と選択し、 **[名前]** には「*DeviceInstallationService.cs*」と入力します。次に **[新規]** をクリックして以下の実装を追加します。

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for FCM");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > このクラスでは、通知ハブの登録ペイロードの一部として一意の ID ([Secure.AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9) を使用) が提供されます。

1. *PushNotificationFirebaseMessagingService.cs* という名前の別の**空のクラス**を **Services** フォルダーに追加してから、次の実装を追加します。

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. **MainActivity.cs** で、以下の名前空間がファイルの先頭に追加されていることを確認します。

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. **MainActivity.cs** で、**LaunchMode** を **SingleTop** に設定し、開かれたときに再度 **MainActivity** が作成されないようにします。

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. プライベート プロパティとそれらに対応するバッキング フィールドを追加して、**IPushNotificationActionService** および **IDeviceInstallationService** の実装への参照を格納します。

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. **Firebase** トークンを取得して格納する **IOnSuccessListener** インターフェイスを実装します。

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. **ProcessNotificationActions** という名前の新しいメソッドを追加します。このメソッドでは、指定された **Intent** に *action* という名前の追加の値があるかどうかを調べます。 その action は、**IPushDemoNotificationActionService** の実装を使用して条件付きでトリガーします。

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. **ProcessNotificationActions** メソッドを呼び出すように **OnNewIntent** メソッドをオーバーライドします。

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > **Activity** の **LaunchMode** は **SingleTop**に設定されているので、**Intent** は、**OnCreate** メソッドではなく **OnNewIntent** メソッドによって既存の **Activity** インスタンスに送信されます。そのため、**OnCreate** メソッドと **OnNewIntent** メソッドの両方で、到着する意図を処理する必要があります。

1. `base.OnCreate` の呼び出しの直後に `Bootstrap.Begin` を呼び出すように **OnCreate** メソッドを更新して、**IDeviceInstallationService** のプラットフォーム固有の実装を渡します。

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. 同じメソッドで、`Bootstrap.Begin` の呼び出しの直後に、条件に基づいて **FirebaseApp** インスタンスに対して **GetInstanceId** を呼び出して、**IOnSuccessListener** として **MainActivity** を追加します。

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. 引き続き **OnCreate** 内で、`LoadApplication` の呼び出し直後に **ProcessNotificationActions** を呼び出して、現在の **Intent** を渡します。

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> プッシュ通知を受信し続けるには、アプリを実行して停止するたびにデバッグ セッションからアプリを再登録する必要があります。
