---
title: Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について学習します。
services: notification-hubs
documentationcenter: ios
keywords: プッシュ通知,プッシュ通知,iOS プッシュ通知
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 520d01327b5809d453bb777165899770ea4c130b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885035"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>チュートリアル:Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信します。 [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) を使用してプッシュ通知を受信する空の iOS アプリを作成します。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * 証明書の署名要求ファイルを生成する
> * アプリをプッシュ通知用に要求する
> * アプリケーションのプロビジョニング プロファイルを作成する
> * iOS プッシュ通知向けに通知ハブを構成する
> * iOS アプリを通知ハブに接続する
> * テスト プッシュ通知を送信する
> * アプリが通知を受信することを確認する

このチュートリアルの完成したコードについては、 [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)を参照してください。 

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、数分で[無料の Azure アカウントを作成](https://azure.microsoft.com/free)できます。
* [Windows Azure Messaging Framework]
*  [Xcode]
* iOS 10 (またはこれ以降のバージョン) に対応したデバイス
* [Apple Developer Program](https://developer.apple.com/programs/) メンバーシップ
  
  > [!NOTE]
  > プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、iOS シミュレーターではなく物理 iOS デバイス (iPhone または iPad) で行う必要があります。
  
このチュートリアルを完了することは、iOS アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>iOS プッシュ通知向けに通知ハブを構成する

このセクションでは、前に作成した **.p12** プッシュ証明書を使用して、通知ハブを作成し、APNS での認証を構成します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-your-notification-hub-with-apns-information"></a>APNS 情報で通知ハブを構成する

1. **[Notification Services]** で、**[Apple (APNS)]** を選択します。
2. **[Certificate]** を選択します。
3. **ファイル アイコン**を選択します。
4. 前にエクスポートした **.p12** ファイルを選択します。
5. 正しい**パスワード**を指定します。
6. **[サンドボックス]** モードを選択します。 **[実稼働]** は、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。

    ![Azure Portal で APNS 証明書を構成する][7]

通知ハブと APNS の構成が完了したので、接続文字列にアプリケーションを登録し、プッシュ通知を送信できます。

## <a name="connect-your-ios-app-to-notification-hubs"></a>Notification Hubs に iOS アプリケーションを接続する

1. Xcode で、新しい iOS プロジェクトを作成し、 **[Single View Application]** テンプレートを選択します。

    ![Xcode - Single View Application][8]

2. 新しいプロジェクトのオプションを設定する際には、Apple Developer ポータルでバンドル ID を設定したときと同じ**製品名**と**組織識別子**を使用してください。

    ![Xcode - project options][11]

3. プロジェクト ナビゲーターでプロジェクト名をクリックし、**[全般]** タブをクリックし、**[署名]** を探します。 Apple Developer アカウントに適したチームを選択します。 XCode を選択すると、バンドル識別子に基づいて以前に作成したプロビジョニング プロファイルが自動的に表示されます。

    Xcode で作成した新しいプロビジョニング プロファイルが表示されない場合は、署名 ID のプロファイルを更新してみてください。 メニュー バーの **Xcode** をクリックし、**[Preference (ユーザー設定)]**、**[Account (アカウント)]** タブ、**[View Details (詳細の表示)]** ボタンの順にクリックします。次に、署名 ID をクリックし、右下隅にある更新ボタンをクリックします。

    ![Xcode - provisioning profile][9]

4. **[機能]** タブを選択し、プッシュ通知を有効にします。

    ![Xcode - プッシュ機能][12]

5. Azure Notification Hubs SDK モジュールを追加します。

   [Cocoapods](https://cocoapods.org) を使用して、またはバイナリをプロジェクトに手動で追加して、アプリに Azure Notification Hubs SDK を統合することができます。

   - Cocoapods による統合

     次の依存関係を `podfile` に追加して、アプリに Azure Notification Hubs SDK を組み込みます。

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     `pod install` を実行して新しく定義したポッドをインストールし、`.xcworkspace` を開きます。

     > [!NOTE]
     > ```[!] Unable to find a specification for `AzureNotificationHubs-iOS` ``` のようなエラーが `pod install` の実行中に表示される場合は、`pod repo update` を実行して Cocoapods リポジトリから最新のポッドを取得した後、`pod install` を実行してください。

   - Carthage による統合

     次の依存関係を `Cartfile` に追加して、アプリに Azure Notification Hubs SDK を組み込みます。

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     次に、依存関係を更新してビルドします。

     ```shell
     $ carthage update
     ```

     Carthage の使用について詳しくは、[Carthage GitHub リポジトリ](https://github.com/Carthage/Carthage)をご覧ください。

   - バイナリをプロジェクトにコピーすることによる統合

     1. zip ファイルとして提供されいるり [Azure Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs-ios/releases) フレームワークをダウンロードして、解凍します。

     2. Xcode でプロジェクトを右クリックして **[Add Files to (ファイルの追加先)]** オプションをクリックし、Xcode プロジェクトに **WindowsAzureMessaging.framework** フォルダーを追加します。 **[オプション]** を選択し、**[Copy items if needed]\(必要に応じてアイテムをコピーする\)** をオンにして **[追加]** をクリックします。

        ![Unzip Azure SDK][10]

6. `HubInfo.h`という名前の新しいヘッダー ファイルをプロジェクトに追加します。 このファイルは、通知ハブの定数を保持します。 次の定義を追加し、文字列リテラルのプレースホルダーを*ハブ名*とメモしておいた *DefaultListenSharedAccessSignature* に置き換えます。

    ```objc
    #ifndef HubInfo_h
    #define HubInfo_h

        #define HUBNAME @"<Enter the name of your hub>"
        #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"

    #endif /* HubInfo_h */
    ```

7. `AppDelegate.h` ファイルを開き、次の import ディレクティブを追加します。

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    #import "HubInfo.h"
    ```
8. `AppDelegate.m` ファイルで、iOS のバージョンに基づいて `didFinishLaunchingWithOptions` メソッド内に次のコードを追加します。 このコードにより、APNs にデバイス ハンドルが登録されます。

    ```objc
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
        UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

9. 同じファイルで、次のメソッドを追加します。

    ```objc
        - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                    notificationHubPath:HUBNAME];

        [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
            else {
                [self MessageBox:@"Registration Status" message:@"Registered"];
            }
        }];
        }

    -(void)MessageBox:(NSString *) title message:(NSString *)messageText
    {
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:messageText preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil];
        [alert addAction:okAction];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }
    ```

    このコードは、HubInfo.h に指定した接続情報を使用して通知ハブに接続します。 その後、通知ハブが通知を送信できるように、通知ハブにデバイス トークンを指定します。

10. 同じファイルで次のメソッドを追加し、アプリケーションがアクティブのときに通知を受信した場合に **UIAlert** が表示されるようにします。

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

11. エラーがないことを確認するために、デバイスでアプリをビルドして実行します。

## <a name="send-test-push-notifications"></a>テスト プッシュ通知を送信する

アプリの通知の受信をテストするには、[Azure Portal] の *[テスト送信]* オプションを使用します。 これは、デバイスにテスト プッシュ通知を送信します。

![Azure Portal - テスト送信][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>アプリがプッシュ通知を受信することを確認する

iOS でプッシュ通知をテストするには、物理 iOS デバイスにアプリをデプロイする必要があります。 iOS シミュレーターを使用して Apple のプッシュ通知を送信することはできません。

1. アプリケーションを実行して登録が成功したことを確認したら、 **[OK]** を押します。

    ![iOS App Push Notification Registration Test][33]

2. 次に、前のセクションで説明されているように、[Azure Portal] からテスト プッシュ通知を送信します。

3. 特定の通知ハブから通知を受信するように登録されているすべてのデバイスにプッシュ通知が送信されます。

    ![iOS App Push Notification Receive Test][35]

## <a name="next-steps"></a>次の手順

この簡単な例では、すべての登録済み iOS デバイスにプッシュ通知をブロードキャストしました。 特定の iOS デバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Windows Azure Messaging Framework]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
