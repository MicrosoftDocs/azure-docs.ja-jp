---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156328"
---
### <a name="configure-the-runner-target-and-infoplist"></a>ランナーのターゲットと Info.plist を構成する

1. **Visual Studio Code** で、**Ctrl** キーを押しながら **ios** フォルダーを**クリック**し、 **[Xcode で開く]** を選択します。

1. **Xcode** で**ランナー** (フォルダーではなく、上部の **xcodeproj**) をクリックし、**ランナー**のターゲットを選択して、 **[Signing & Capabilities]\(署名と機能\)** を選択します。 **すべて**のビルド構成を選択した状態で、 **[Team]\(チーム\)** の開発者アカウントを選択します。 [Automatically manage signing]\(署名を自動管理する\) がオプションがオンになっていて、署名証明書とプロビジョニング プロファイルが自動的に選択されていることを確認します。

    > [!NOTE]
    > 新しいプロビジョニング プロファイルの値が表示されない場合は、 **[Xcode]**  >  **[Preferences]\(環境設定\)**  >  **[アカウント]** を選択して、[Signing Identity]\(署名 ID\) のプロファイルを更新してみてから、 **[Download Manual Profiles]\(手動プロファイルのダウンロード\)** ボタンを選択し、プロファイルをダウンロードします。

1. **[+ Capability]\(+ 機能\)** をクリックし、**プッシュ通知**を検索します。 **[Push Notifications]\(プッシュ通知\)** を**ダブルクリック**して、この機能を追加します。

1. **Info.plist** を開き、**Minimum system version** を **13.0** に設定します。

    > [!NOTE]
    > このチュートリアルでは、**iOS 13.0 以降**が実行されているデバイスのみがサポートされていますが、より前のバージョンを実行しているデバイスをサポートするように拡張できます。

1. **Runner.entitlements** を開き、**APS Environment** 設定が **development** になっていることを確認します。

### <a name="handle-push-notifications-for-ios"></a>iOS 用のプッシュ通知を処理する

1. **Ctrl** キーを押しながら **Runner** フォルダー (Runner プロジェクト内) を**クリック**し、 **[New Group]\(新しいグループ\)** を選択します。名前は **Services** にします。

1. **Ctrl** キーを押しながら **Services** フォルダーを**クリック**し、 **[New File]\(新しいファイル\)** を選択します。次に、 **[Swift File]\(Swift ファイル\)** を選択し、 **[Next]\(次へ\)** をクリックします。 名前として **DeviceInstallationService** を指定し、 **[Create]\(作成\)** をクリックします。

1. 次のコードを使用して、**DeviceInstallationService.swift** を実装します。

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > このクラスは、`com.<your_organization>.pushdemo/deviceinstallation` チャンネルのプラットフォーム固有の対応物を実装します。 これは、**DeviceInstallationService.dart** 内のアプリの Flutter 部分で定義されています。 この場合、呼び出しは共通コードからネイティブ ホストに対して行われます。 **<your_organization>** が使用されている箇所は、必ず自分の組織に置き換えてください。
    >
    > このクラスでは、通知ハブの登録ペイロードの一部として一意の ID ([UIDevice.identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) 値を使用) が提供されます。

1. *NotificationRegistrationService* という名前のもう 1 つの **Swift ファイル**を **Services** フォルダーに追加し、次のコードを追加します。

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > このクラスは、`com.<your_organization>.pushdemo/notificationregistration` チャンネルのプラットフォーム固有の対応物を実装します。 これは、**NotificationRegistrationService.dart** 内のアプリの Flutter 部分で定義されています。 この場合、呼び出しはネイティブ ホストから共通コードに対して行われます。 ここでも、 **<your_organization>** が使用されている箇所は、自分の組織に置き換えるように注意してください。

1. *NotificationActionService* という名前のもう 1 つの **Swift ファイル**を **Services** フォルダーに追加し、次のコードを追加します。

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > このクラスは、`com.<your_organization>.pushdemo/notificationaction` チャンネルのプラットフォーム固有の対応物を実装します。 それは **NotificationActionService. dart** 内のアプリの Flutter 部分で定義されました。 この場合、呼び出しは双方向で行うことができます。 **<your_organization>** が使用されている箇所は、必ず自分の組織に置き換えてください。

1. **AppDelegate. swift** で、前に作成したサービスへの参照を格納する変数を追加します。

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. 通知データを処理するために、**processNotificationActions** という名前の関数を追加します。 アクションがアプリの起動中に処理されている場合は、そのアクションを条件付きでトリガーするか、後で使用するために格納します。

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. **didRegisterForRemoteNotificationsWithDeviceToken** 関数をオーバーライドし、**DeviceInstallationService** の **token** の値を設定します。 次に、**NotificationRegistrationService** の **refreshRegistration** を呼び出します。

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. **didReceiveRemoteNotification** 関数をオーバーライドし、**userInfo** 引数を **processNotificationActions** 関数に渡します。

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. **didFailToRegisterForRemoteNotificationsWithError** 関数をオーバーライドして、エラーをログに記録します。

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > これは実際にはプレースホルダーです。 運用環境のシナリオの場合は、適切なログ記録とエラー処理を実装する必要があります。

1. **didFinishLaunchingWithOptions** で、**deviceInstallationService**、**notificationRegistrationService**、および **notificationActionService** 変数をインスタンス化します。

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. 同じ関数で、条件に基づいて承認を要求し、リモート通知に登録します。

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. **launchOptions** に **remoteNotification** キーが含まれている場合は、**didFinishLaunchingWithOptions** 関数の最後で **processNotificationActions** を呼び出します。 結果の **userInfo** オブジェクトを渡し、*launchAction* 引数には *true* を使用します。 *true* 値は、アプリの起動時にアクションが処理されていることを示します。

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
