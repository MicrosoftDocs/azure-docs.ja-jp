---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 1dc491084f65bc90397b0897de6b6cfe4f2fd410
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448727"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Info.plist と Entitlements.plist を構成する

1. **Visual Studio** >  **[ユーザー設定...]**  >  **[発行]**  >  **[Apple Developer Accounts] (Apple Developer アカウント)** で、お使いの **Apple Developer アカウント**にサインインしていること、および適切な*証明書*と*プロビジョニング プロファイル*がダウンロードされていることを確認します。 これらのアセットは、前もっての手順の一部として作成しておく必要があります。

1. **PushDemo.iOS** で **Info.plist** を開き、**BundleIdentifier** が、[Apple Developer ポータル](https://developer.apple.com)の対応するプロビジョニング プロファイルで使用されていた値と一致していることを確認します。 **BundleIdentifier** は、``com.<organization>.PushDemo`` という形式でした。

1. 同じファイルで、**Minimum system version** を **13.0** に設定します。

    > [!NOTE]
    > このチュートリアルでは、**iOS 13.0 以降**が実行されているデバイスのみがサポートされていますが、より前のバージョンを実行しているデバイスをサポートするように拡張できます。

1. **PushDemo.iOS** の**プロジェクト オプション**を開きます (プロジェクトをダブルクリックします)。

1. **[Project Options] (プロジェクト オプション)** の **[ビルド] > [iOS Bundle Signing] (iOS バンドル署名)** で、 **[チーム]** の下にある開発者アカウントが選択されていることを確認します。 次に、[Automatically manage signing] (署名を自動管理する) が選択されていて、署名証明書とプロビジョニング プロファイルが自動的に選択されていることを確認します。

    > [!NOTE]
    > *署名証明書*と*プロビジョニング プロファイル*が自動的に選択されていない場合は、 **[Manual Provisioning] (手動プロビジョニング)** を選択してから **[Bundle Signing Options] (バンドル署名オプション)** をクリックします。 **[Signing Identity] (署名 ID)** には *[チーム]* が選択されていて、 **[デバッグ]** 構成と **[リリース]** 構成の両方で**プロビジョニング プロファイル**には *PushDemo* 固有のプロビジョニング プロファイルが選択されていることを確認します。そうすることで、両方の場合に **[プラットフォーム]** には **[iPhone]** が選択されるようにします。

1. **[PushDemo.iOS]** で **Entitlements.plist** を開き、 **[権利]** タブで表示したときに **[プッシュ通知を有効にする]** チェック ボックスがオンになっていることを確認します。次に、 **[ソース]** タブで表示したときに **[APS Environment] (APS 環境)** の設定が **[開発]** に設定されていることを確認します。

### <a name="handle-push-notifications-for-ios"></a>iOS 用のプッシュ通知を処理する

1. **[PushDemo.iOS]** プロジェクトを **Control** + **クリック**し、 **[追加]** メニューから **[新しいフォルダー]** を選択します。次に、 **[フォルダー名]** として「*Services*」を使用して **[追加]** をクリックします。

1. **Services** フォルダーを **Control** + **クリック**し、 **[追加]** メニューから **[新しいファイル...]** を選択します。

1. **[全般]**  >  **[空のクラス]** と選択し、 **[名前]** には「*DeviceInstallationService.cs*」と入力します。次に **[新規]** をクリックして以下の実装を追加します。

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > このクラスでは、一意の ID ([UIDevice.IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) 値を使用) と通知ハブの登録ペイロードが提供されます。

1. **PushDemo.iOS** プロジェクトに *Extensions* という名前の新しいフォルダーを追加してから、以下の実装を使用してそのフォルダーに *NSDataExtensions.cs* という名前の**空のクラス**を追加します。

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. **AppDelegate.cs** で、以下の名前空間がファイルの先頭に追加されていることを確認します。

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. プライベート プロパティとそれらに対応するバッキング フィールドを追加して、**IPushDemoNotificationActionService**、**INotificationRegistrationService**、および **IDeviceInstallationService** の実装への参照を格納します。

    ```csharp
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
    ```

1. ユーザー通知設定を登録してから、**APNS** を使用するリモート通知のための登録を行う **RegisterForRemoteNotifications** メソッドを追加します。

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. **CompleteRegistrationAsync** メソッドを追加して `IDeviceInstallationService.Token` プロパティ値を設定します。 最後に格納されてから更新されている場合は、登録を更新してデバイス トークンをキャッシュします。

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. **NSDictionary** 通知データを処理し、条件に基づいて **NotificationActionService.TriggerAction** を呼び出すための **ProcessNotificationActions** メソッドを追加しします。

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. **RegisteredForRemoteNotifications** メソッドをオーバーライドして、**deviceToken** 引数を **CompleteRegistrationAsync** メソッドに渡します。

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. **ReceivedRemoteNotification** メソッドをオーバーライドして、**userInfo** 引数を **ProcessNotificationActions** メソッドに渡します。

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. **FailedToRegisterForRemoteNotifications** メソッドをオーバーライドして、エラーをログに記録します。

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > これは実際にはプレースホルダーです。 運用環境のシナリオの場合は、適切なログ記録とエラー処理を実装する必要があります。

1. `Forms.Init` の呼び出しの直後に `Bootstrap.Begin` を呼び出すように **FinishedLaunching** メソッドを更新して、**IDeviceInstallationService** のプラットフォーム固有の実装を渡します。

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. 同じ方法で、条件に基づいて承認を要求し、`Bootstrap.Begin` の直後にリモート通知のための登録を行います。

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. まだ **FinishedLaunching** の中で、**options** 引数に **UIApplication.LaunchOptionsRemoteNotificationKey** が含まれている場合は、`LoadApplication` を呼び出した直後に **ProcessNotificationActions** を呼び出して、結果の **userInfo** オブジェクトを渡します。

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
