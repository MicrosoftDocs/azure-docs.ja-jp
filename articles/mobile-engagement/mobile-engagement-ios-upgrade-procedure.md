---
title: "Azure Mobile Engagement iOS SDK のアップグレード手順 | Microsoft Docs"
description: "Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: c4b5b8bc05365ddc63b0d7a6a3c63eaee31af957
ms.openlocfilehash: 37c7f133d079186f828d58cabce0d2a259efd085


---
# <a name="upgrade-procedures"></a>アップグレードの手順
既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

まず、新しいバージョンの SDK ごとに、EngagementSDK フォルダーと EngagementReach フォルダーを置き換える (削除し、xcode で再インポートする) 必要があります。

## <a name="from-300-to-400"></a>3.0.0 から 4.0.0 に移行
### <a name="xcode-8"></a>XCode 8
SDK のバージョン 4.0.0 以降では、XCode 8 が必須となります。

> [!NOTE]
> XCode 7 に依存している場合は、 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)を使用できます。 この以前のバージョンの Reach モジュールには、iOS 10 デバイスで実行したときに、システム通知が処理されないという既知のバグがあります。 これを修正するには、次のようにアプリケーション デリゲートで非推奨 API の `application:didReceiveRemoteNotification:` を実装する必要があります。
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **この回避策はお勧めできません。**この iOS API は推奨されていないため、(マイナー アップグレードも含め) iOS バージョンの今後のアップグレードでこの動作が変更される可能性があります。 できるだけ速やかに XCode 8 に切り替えてください。
> 
> 

### <a name="usernotifications-framework"></a>UserNotifications フレームワーク
`UserNotifications` フレームワークをビルド フェーズに追加する必要があります。

プロジェクト エクスプローラーでプロジェクト ペインを開き、適切なターゲットを選択します。 次に、**[Build phases (ビルド フェーズ)]** タブを開き、**[Link Binary With Libraries (バイナリをライブラリとリンク)]** メニューで `UserNotifications.framework` フレームワークを追加し、リンクを `Optional` として設定します。

### <a name="application-push-capability"></a>アプリケーションのプッシュ機能
XCode 8 はアプリケーションのプッシュ機能をリセットする場合があります。選択したターゲットの `capability` タブで、この機能を再確認してください。

### <a name="add-the-new-ios-10-notification-registration-code"></a>新しい iOS 10 通知登録コードの追加
アプリケーションを通知に登録するための古いコード スニペットは引き続き機能しますが、iOS 10 での実行時に非推奨 API を使用します。

`User Notification` フレームワークをインポートします。

        #import <UserNotifications/UserNotifications.h> 

アプリケーション デリゲートの `application:didFinishLaunchingWithOptions` メソッド内の

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

を以下で置き換えます。

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>UNUserNotificationCenter デリゲートの競合を解決する

*アプリケーションでも、いずれかのサードパーティ ライブラリでも `UNUserNotificationCenterDelegate` を実装しない場合は、この部分をスキップすることができます。*

`UNUserNotificationCenter` デリゲートは、iOS 10 以上で実行されているデバイスで Engagement 通知のライフ サイクルを監視するために SDK によって使用されます。 SDK には、`UNUserNotificationCenterDelegate` プロトコルの独自の実装が存在しますが、アプリケーションごとに存在可能な `UNUserNotificationCenter` デリゲートは&1; つだけです。 `UNUserNotificationCenter` に追加されたその他のデリゲートは、Engagement のものと競合します。 SDK によって自分またはそれ以外のサード パーティのデリゲートが検出された場合、独自の実装は使用されず、競合を解決する機会が提供されます。 競合を解決するには、Engagement ロジックを独自のデリゲートに追加する必要があります。

これを実現する方法は&2; つあります。

提案 1: 単に、デリゲート呼び出しを SDK に転送します。

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

提案 2: `AEUserNotificationHandler` クラスから継承します。

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> 通知が Engagement から送信されたものかどうかを確認するには、`userInfo` ディクショナリをエージェントの `isEngagementPushPayload:` クラス メソッドに渡します。

`UNUserNotificationCenter` オブジェクトのデリゲートがアプリケーション デリゲートの `application:willFinishLaunchingWithOptions:` または `application:didFinishLaunchingWithOptions:` メソッド内のデリゲートに設定されていることを確認します。
たとえば、上記の提案 1 を実装した場合は、次のようになります。

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code
  
        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="from-200-to-300"></a>2.0.0 から 3.0.0 に移行
iOS 4.X のサポートが終了。 このバージョンから、アプリケーションのデプロイ ターゲットは iOS 6 以降である必要があります。

アプリケーションでリーチを使用している場合は、リモート通知を受け取れるように、`remote-notification` 値を Info.plist ファイル内の `UIBackgroundModes` 配列に追加する必要があります。

アプリケーション デリゲートのメソッド `application:didReceiveRemoteNotification:` は、`application:didReceiveRemoteNotification:fetchCompletionHandler:` に置き換える必要があります。

AEPushDelegate.h インターフェイスは廃止されるため、すべての参照を削除する必要があります。 これには、 `[[EngagementAgent shared] setPushDelegate:self]` の削除や、アプリケーション デリゲートからのデリゲート メソッドの削除も含まれます。

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>1.16.0 から 2.0.0 に移行
Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。
以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 1.16 に移行し、次の手順を適用してください。

> [!IMPORTANT]
> Capptain と Mobile Engagement は、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。 アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。
> 
> 

### <a name="agent"></a>エージェント
メソッド `registerApp:` は、新しいメソッド `init:` で置き換えられました。 その結果、アプリケーション デリゲートは更新され、接続文字列を使用する必要があります。

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

SmartAd の追跡が SDK から削除されました。`AETrackModule` クラスのすべてのインスタンスを削除する必要があります。

### <a name="class-name-changes"></a>クラス名の変更
ブランド変更の一部として、いくつかの変更が必要なクラス名とファイル名があります。

「CP」で始まるクラスは、「AE」で始まる名前に変更されました。

例:

* `CPModule.h` は `AEModule.h` に変更されました。

「Capptain」で始まるクラスは、「Engagement」で始まる名前に変更されました。

次に例を示します。

* クラス `CapptainAgent` は `EngagementAgent` に変更されました。
* クラス `CapptainTableViewController` は `EngagementTableViewController` に変更されました。
* クラス `CapptainUtils` は `EngagementUtils` に変更されました。
* クラス `CapptainViewController` は `EngagementViewController` に変更されました。




<!--HONumber=Dec16_HO2-->


