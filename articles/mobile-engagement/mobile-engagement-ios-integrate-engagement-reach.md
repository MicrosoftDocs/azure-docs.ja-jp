---
title: "Azure Mobile Engagement iOS SDK Reach の統合 | Microsoft Docs"
description: "Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: c8bb1161e874a3adda4a71ee889ca833db881e20
ms.openlocfilehash: 7e24bbc1832c6a85181c943e4e1c705785358527


---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>iOS でエンゲージメント リーチを統合する方法
このガイドに従う前に、「 [Engagement を iOS に統合する方法](mobile-engagement-ios-integrate-engagement.md) 」に記載されている統合手順に従う必要があります。

このドキュメントでは XCode 8 が必要です。 XCode 7 に依存している場合は、 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)を使用できます。 この以前のバージョンには、iOS 10 デバイスで実行したときに、システム通知が処理されないという既知のバグがあります。 これを修正するには、次のようにアプリケーション デリゲートで非推奨 API の `application:didReceiveRemoteNotification:` を実装する必要があります。

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **この回避策はお勧めできません。**この iOS API は推奨されていないため、(マイナー アップグレードも含め) iOS バージョンの今後のアップグレードでこの動作が変更される可能性があります。 できるだけ速やかに XCode 8 に切り替えてください。
> 
> 

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>アプリがサイレント プッシュ通知を受信できるようにする
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>統合手順
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>iOS プロジェクトにエンゲージメント リーチ SDK を埋め込む
* Xcode プロジェクトにリーチ SDK を追加します。 Xcode で、**[プロジェクト] \> [プロジェクトに追加]** の順に進み、`EngagementReach` フォルダーを選択します。

### <a name="modify-your-application-delegate"></a>アプリケーション デリゲートを変更する
* 実装ファイルの上部で、Engagement Reach モジュールをインポートします。
  
      [...]
      #import "AEReachModule.h"
* メソッド `applicationDidFinishLaunching:` または `application:didFinishLaunchingWithOptions:` 内で、reach モジュールを作成し、これを既存の Engagement 初期化行に渡します。
  
      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]
  
        return YES;
      }
* 通知アイコンの **'icon.png'** 文字列とイメージ名を変更します。
* リーチ キャンペーンで、オプションの *Update badge value* を使用する場合、またはネイティブ プッシュの \</SaaS/Reach API/Campaign format/Native Push\> キャンペーンを使用する場合は、Reach モジュールにバッジ アイコン自体を管理させる必要があります (アプリケーションが起動またはフォアグラウンドで実行されるたびに、アプリケーション バッジは自動的にクリアされ、Engagement が格納した値もリセットされます)。 これは、Reach モジュールの初期化後に、次の行を追加することによって行われます。
  
      [reach setAutoBadgeEnabled:YES];
* Reach データ プッシュを処理する場合は、アプリケーション デリゲートに `AEReachDataPushDelegate` プロトコルを準拠させる必要があります。 Reach モジュールの初期化後に、次の行を追加します。
  
      [reach setDataPushDelegate:self];
* これにより、メソッド `onDataPushStringReceived:` と `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` をアプリケーション デリゲートに実装できます。
  
      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }
  
      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>カテゴリ
カテゴリ パラメーターは、データ プッシュ キャンペーンを作成するときに指定でき、データ プッシュをフィルター処理するために使用できます。 これは、さまざまな種類の `Base64` データをプッシュする場合や、データの解析前に型を識別する場合に役立ちます。

**アプリケーションは、リーチ コンテンツを受信して表示する準備ができました。**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>お知らせとポーリングを常に受信する方法
エンゲージメントは、Apple Push Notification Service を使用して、いつでもエンドユーザーにリーチ通知を送信できます。

この機能を有効にするには、Apple プッシュ通知用のアプリケーションを準備し、アプリケーション デリゲートを変更する必要があります。

### <a name="prepare-your-application-for-apple-push-notifications"></a>Apple プッシュ通知用アプリケーションを準備する
[Apple プッシュ通知用アプリケーションを準備する方法](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>必要なクライアント コードを追加する
*この時点で、アプリケーションにはエンゲージメント フロントエンドで登録済みの Apple プッシュ証明書が必要になります。*

まだ登録していない場合は、プッシュ通知を受信するアプリケーションを登録する必要があります。

* `User Notification` フレームワークをインポートします。
  
        #import <UserNotifications/UserNotifications.h>
* アプリケーションの起動時に、次の行を追加します (通常は `application:didFinishLaunchingWithOptions:`)。
  
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

次に、Apple サーバーによって返されたデバイス トークンをエンゲージメントに提供する必要があります。 これは、アプリケーション デリゲートの `application:didRegisterForRemoteNotificationsWithDeviceToken:` という名前のメソッドで行われます。

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

最後に、アプリケーションがリモート通知を受信したときに、エンゲージメント SDK を通知する必要があります。 これを行うには、アプリケーション デリゲートのメソッド `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` を呼び出します。

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!NOTE]
> 上記のメソッドは、iOS 7 で導入されました。 iOS&7; 以前を対象としている場合は、必ず、アプリケーション デリゲートに `application:didReceiveRemoteNotification:` メソッドを実装し、`handler` 引数の代わりに nil を渡して EngagementAgent の `applicationDidReceiveRemoteNotification` を呼び出してください。
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> 既定では、エンゲージメント リーチが completionHandler を制御します。 コード内の `handler` ブロックに手動で応答する場合は、`handler` 引数の代わりに nil を渡し、完了ブロックを自分で制御できます。 使用可能な値の一覧については、 `UIBackgroundFetchResult` 型に関するページを参照してください、。
> 
> 

### <a name="full-example"></a>完全な例
完全な統合の例を次に示します。

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>UNUserNotificationCenter デリゲートの競合を解決する

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

## <a name="how-to-customize-campaigns"></a>キャンペーンをカスタマイズする方法
### <a name="notifications"></a>通知
通知には、システム通知とアプリ内通知の&2; 種類があります。

システム通知は iOS によって処理され、カスタマイズすることはできません。

アプリ内通知は、現在のアプリケーション ウィンドウに動的に追加されているビューから成ります。 これは、通知オーバーレイと呼ばれます。 通知オーバーレイはアプリケーション内のビューを変更する必要がないため、迅速な統合に適しています。

#### <a name="layout"></a>レイアウト
アプリ内通知の外観を変更するには、既存のサブビューのタグの値と種類を保持している限り、必要に応じてファイル `AENotificationView.xib` を変更するだけです。

既定では、アプリ内通知は画面の下部に表示されます。 画面の上部に表示する場合は、指定された `AENotificationView.xib` を編集し、メイン ビューの `AutoSizing` プロパティを変更すると、そのスーパービューの上部に表示させることができます。

#### <a name="categories"></a>カテゴリ
指定されたレイアウトを変更する場合は、すべての通知の外観を変更します。 カテゴリを使用して、通知対象のさまざまな外観 (場合によっては動作) を定義できます。 リーチ キャンペーンを作成する場合にカテゴリを指定できます。 また、カテゴリを使用してお知らせとポーリングもカスタマイズできます。これについては、このドキュメントの後半で説明します。

通知のカテゴリ ハンドラーを登録するには、reach モジュールが初期化された時点で呼び出しを追加する必要があります。

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier` は、プロトコル `AENotifier` に準拠しているオブジェクトのインスタンスである必要があります。

プロトコル メソッドを自分で実装したり、既に多くの作業を行っている既存のクラス `AEDefaultNotifier` を再実装することも可能です。

たとえば、特定のカテゴリの通知ビューを再定義する場合は、次の例に従って操作します。

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

この簡単なカテゴリ例では、 `MyNotificationView.xib` という名前のファイルがメイン アプリケーション バンドルにあると想定しています。 このメソッドが対応する `.xib`を検出できない場合、通知は表示されず、エンゲージメントはコンソールにメッセージを出力します。

指定された nib ファイルは、次の規則を遵守する必要があります。

* 1 つのビューのみを含む。
* サブビューは、指定された `AENotificationView.xib`
* サブビューは、指定された `AENotificationView.xib` という名前の nib ファイル内のものと同じタグを持つ。

> [!TIP]
> 指定された `AENotificationView.xib` という名前の nib ファイルをコピーし、そこから作業を開始します。 この nib ファイル内のビューは、クラス `AENotificationView`に関連付けられていることに注意してください。 このクラスは、コンテキストに応じてサブビューを移動し、サイズ変更するようにメソッド `layoutSubViews` を再定義しました。 これを `UIView` や自分のカスタム ビュー クラスに置き換えることができます。
> 
> 

通知をさらに細かくカスタマイズする必要がある場合 (コードから直接ビューを読み込む場合など) は、`Protocol ReferencesDefaultNotifier` と `AENotifier` の指定されたソース コードやクラスのドキュメントをご確認ください。

複数のカテゴリに対して同じ通知を使用できます。

次のように、既定の通知を再定義もできます。

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>通知の処理
既定のカテゴリを使用する場合、いくつかのライフ サイクル メソッドは、`AEReachContent` オブジェクトを呼び出して統計情報を報告し、キャンペーンの状態を更新します。

* 通知がアプリケーションに表示されたときに、`handleNotification:` が `YES` を返すと、`displayNotification` メソッドが `AEReachModule` によって呼び出されます (統計情報を報告します)。
* 通知を閉じると、`exitNotification` メソッドが呼び出されて統計情報が報告され、次のキャンペーンを処理できるようになります。
* 通知をクリックすると、 `actionNotification` が呼び出されて統計情報が報告され、関連付けられたアクションが実行されます。

`AENotifier` の実装が既定の動作をバイパスする場合は、これらのライフ サイクル メソッドを自分で呼び出す必要があります。 次の例では、既定の動作がバイパスされるいくつかのケースを示しています。

* `AEDefaultNotifier` を拡張しない場合 (最初からカテゴリの処理を実装した場合など)。
* `prepareNotificationView:forContent:` をオーバーライドした場合 (少なくとも `onNotificationActioned` または `onNotificationExited` がいずれかの U.I コントロールにマップされていることをご確認ください)。

> [!WARNING]
> `handleNotification:` が例外をスローし、コンテンツが削除され、`drop` が呼び出された場合、これは統計情報で報告され、次のキャンペーンを処理できるようになります。
> 
> 

#### <a name="include-notification-as-part-of-an-existing-view"></a>既存のビューの一部に通知を含める
オーバーレイは迅速な統合に適していますが、不便な場合や望ましくない副作用をもたらす場合があります。

一部のビューのオーバーレイ システムに不満な場合は、これらのビューをカスタマイズできます。

既存のビューに通知のレイアウトを含めることができます。 これを行うには、次の&2; つの実装スタイルがあります。

1. インターフェイス ビルダーを使用して、通知ビューを追加します。
   
   *  *インターフェイス ビルダー*
   * 通知を表示する場所に 320 x 60 (または、iPad の場合は 768 x 60) の `UIView` を配置します。
   * このビューのタグ値を **36822491**
2. プログラムを使用して通知ビューを追加します。 ビューが初期化されたら、次のコードを追加します。
   
       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG` マクロは `AEDefaultNotifier.h` にあります。

> [!NOTE]
> 既定の通知は、通知のレイアウトがこのビューに含まれていて、オーバーレイが追加されていないことを自動的に検出します。
> 
> 

### <a name="announcements-and-polls"></a>お知らせとポーリング
#### <a name="layouts"></a>レイアウト
既存のサブビューのタグの値と種類を保持している限り、`AEDefaultAnnouncementView.xib` ファイルと `AEDefaultPollView.xib` ファイルを変更できます。

#### <a name="categories"></a>カテゴリ
##### <a name="alternate-layouts"></a>代替レイアウト
通知と同様に、キャンペーンのカテゴリを使用して、お知らせとポーリングの代替レイアウトを設定できます。

お知らせのカテゴリを作成するには、 **AEAnnouncementViewController** を拡張し、reach モジュールが初期化された時点でこれを登録する必要があります。

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> ユーザーがカテゴリ「my\_category」のお知らせの通知をクリックするたびに、登録されているビュー コントローラー (この場合は `MyCustomAnnouncementViewController`) が、メソッド `initWithAnnouncement:` を呼び出すことで初期化され、現在のアプリケーション ウィンドウにビューが追加されます。
> 
> 

`AEAnnouncementViewController` クラスの実装では、`announcement` プロパティを読み取り、サブビューを初期化する必要があります。 次の例について検討してみましょう。ここでは、2 つのラベルが `AEReachAnnouncement` クラスの `title` プロパティと `body` プロパティを使用して初期化されています。

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

自分でビューを読み込まずに、既定のお知らせビューのレイアウトを再利用する場合は、カスタム ビュー コントローラーを使用して指定されたクラス `AEDefaultAnnouncementViewController`を拡張できます。 この場合は、カスタム ビュー コントローラーで読み込めるように、nib ファイル `AEDefaultAnnouncementView.xib` を複製して名前を変更します (`CustomAnnouncementViewController` という名前のコントローラーの場合は、nib ファイル `CustomAnnouncementView.xib` を呼び出す必要があります)。

お知らせの既定のカテゴリを置き換えるには、 `kAEReachDefaultCategory`で定義されているカテゴリのカスタム ビュー コントローラーを登録します。

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

ポーリングは、同じようにカスタマイズできます。

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

今回は指定された `MyCustomPollViewController` が `AEPollViewController` を拡張する必要があります。 あるいは、既定のコント ローラー `AEDefaultPollViewController`から拡張できます。

> [!IMPORTANT]
> ビュー コントローラーが閉じる前に、`action` (カスタム ポーリング ビュー コントローラーの場合は `submitAnswers:`) メソッドまたは `exit` メソッドのいずれかを必ず呼び出してください。 これを行わないと、統計情報は送信されず (キャンペーンは分析されません)、さらに重要な次のキャンペーンが、アプリケーション プロセスが再起動するまで通知されないことになります。
> 
> 

##### <a name="implementation-example"></a>実装例
この実装では、カスタム アナウンス ビューが外部の xib ファイルから読み込まれます。

高度な通知カスタマイズと同様に、標準実装のソース コードをご確認ください。

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end



<!--HONumber=Dec16_HO2-->


