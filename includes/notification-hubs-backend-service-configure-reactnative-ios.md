---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060441"
---
### <a name="configure-required-ios-packages"></a>必要な iOS パッケージを構成する

パッケージは、アプリをビルドするときに[自動的にリンク](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)されます。 必要なのは、ネイティブ ポッドをインストールすることだけです。

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Info.plist と Entitlements.plist を構成する

1. "PushDemo/ios" フォルダーにアクセスし、"PushDemo.xcworkspace" ワークスペースを開いて、一番上のプロジェクト "PushDemo" を選択し、[Signing & Capabilities]\(署名と機能\) タブを選択します。

1. バンドル識別子を更新し、プロビジョニング プロファイルで使用される値と一致させます。

1. "+" ボタンを使用して、2 つの新機能を追加します。

    - バックグラウンド モード機能。[リモート通知] をオンにします。
    - プッシュ通知機能

### <a name="handle-push-notifications-for-ios"></a>iOS 用のプッシュ通知を処理する

1. "AppDelegate.h" を開き、次のインポートを追加します。

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. `UNUserNotificationCenterDelegate` を追加して、"AppDelegate" でサポートされるプロトコルの一覧を更新します。

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. "AppDelegate.m" を開いて、必要なすべての iOS コールバックを構成します。

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
