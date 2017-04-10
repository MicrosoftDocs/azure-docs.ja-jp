---
title: "Azure Mobile Engagement iOS SDK の概要 | Microsoft Docs"
description: "Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: c8bb1161e874a3adda4a71ee889ca833db881e20
ms.openlocfilehash: cd70b0b5656bef08a8be1c1a67754b203cceb905


---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK for Azure Mobile Engagement
ここでは、iOS アプリで Azure Mobile Engagement を統合する方法の詳細を確認します。 まず試してみる場合は、「 [15 分間チュートリアル](mobile-engagement-ios-get-started.md)」をご覧ください

[SDK コンテンツ](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>統合手順
1. ここから開始: [iOS アプリで Mobile Engagement を統合する方法](mobile-engagement-ios-integrate-engagement.md)
2. 通知: [リーチ (通知) を iOS アプリに統合する方法](mobile-engagement-ios-integrate-engagement-reach.md)
3. タグ付けプランの実装: [iOS アプリで高度な Mobile Engagement のタグ付け API を使用する方法](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>リリース ノート
### <a name="401-12132016"></a>4.0.1 (12/13/2016)
* バックグラウンドでのログ配信が改善されました。

以前のバージョンについては、「 [完全リリース ノート](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>アップグレードの手順
既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK のいくつかのバージョンがない場合は、次の手順に従う必要があります。完全な「[アップグレード手順](mobile-engagement-ios-upgrade-procedure.md)」をご覧ください。

まず、新しいバージョンの SDK ごとに、EngagementSDK フォルダーと EngagementReach フォルダーを置き換える (削除し、xcode で再インポートする) 必要があります。

### <a name="from-300-to-400"></a>3.0.0 から 4.0.0 に移行
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

#### <a name="usernotifications-framework"></a>UserNotifications フレームワーク
`UserNotifications` フレームワークをビルド フェーズに追加する必要があります。

プロジェクト エクスプローラーでプロジェクト ペインを開き、適切なターゲットを選択します。 次に、**[Build phases (ビルド フェーズ)]** タブを開き、**[Link Binary With Libraries (バイナリをライブラリとリンク)]** メニューで `UserNotifications.framework` フレームワークを追加し、リンクを `Optional` として設定します。

#### <a name="application-push-capability"></a>アプリケーションのプッシュ機能
XCode 8 はアプリケーションのプッシュ機能をリセットする場合があります。選択したターゲットの `capability` タブで、この機能を再確認してください。

#### <a name="add-the-new-ios-10-notification-registration-code"></a>新しい iOS 10 通知登録コードの追加
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

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>UNUserNotificationCenter デリゲートの競合を解決する

*アプリケーションも、いずれかのサードパーティ ライブラリも `UNUserNotificationCenterDelegate` を実装していない場合は、この部分をスキップできます。*

`UNUserNotificationCenter` デリゲートは、iOS 10 以降で実行されているデバイスで Engagement 通知のライフ サイクルを監視するために SDK によって使用されます。 SDK には、`UNUserNotificationCenterDelegate` プロトコルの独自の実装が存在しますが、アプリケーションごとに存在できる `UNUserNotificationCenter` デリゲートは&1; つだけです。 `UNUserNotificationCenter` オブジェクトに追加されたその他のデリゲートは、Engagement のものと競合します。 SDK によって自分またはサード パーティのデリゲートが検出された場合、SDK によって独自の実装は使用されず、競合を解決する機会が提供されます。 競合を解決するには、Engagement ロジックを独自のデリゲートに追加する必要があります。

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




<!--HONumber=Dec16_HO2-->


