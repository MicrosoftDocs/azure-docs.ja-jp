---
title: "Swift で iOS の Azure Mobile Engagement を開始する | Microsoft Docs"
description: "iOS アプリ の分析やプッシュ通知で Azure Mobile Engagement を使用する方法を説明します。"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
ms.openlocfilehash: 1011b9823333e79a52cd2d187df4f8d063b1f799
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Swift で IOS アプリ の Azure  Mobile Engagement を開始する
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、iOS アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Apple プッシュ通知システム (APNS) を使用して基本データを収集し、プッシュ通知を受信する空の iOS アプリケーションを作成します。

このチュートリアルには、次のものが必要です。

* XCode 8 (Mac アプリ ストアからインストールすることができます)
* [モバイル エンゲージメント iOS SDK]
* プッシュ通知証明書 (.p12)。Apple Dev Center で入手できます

> [!NOTE]
> このチュートリアルでは、Swift バージョン 3.0 を使用します。 
> 
> 

このチュートリアルを完了することは、iOS アプリケーションの他のすべての Mobile Engagement チュートリアルの前提条件です。

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started)」をご覧ください。
> 
> 

## <a id="setup-azme"></a>iOS アプリ用に Mobile Engagement を設定する
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します
このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。 統合に関する完全なドキュメントは、「 [Mobile Engagement iOS SDK 統合](mobile-engagement-ios-sdk-overview.md)

統合のデモンストレーションを行うために、XCode で基本的なアプリを作成します。

### <a name="create-a-new-ios-project"></a>新しい iOS プロジェクトを作成する
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>アプリを Mobile Engagement のバックエンドに接続する
1. [モバイル エンゲージメント iOS SDK]
2. コンピューター上のフォルダーに .tar.gz ファイルを抽出します
3. プロジェクトを右クリックし、[Add files to ...] を選択します
   
    ![][1]
4. SDK を抽出したフォルダーに移動し、 `EngagementSDK` フォルダーを選択して、[OK] を押します。
   
    ![][2]
5. `Build Phases` タブを開き、`Link Binary With Libraries` メニューで次に示すように、フレームワークを追加します。
   
    ![][3]
6. ブリッジ ヘッダーを作成して、[作成] > [新規] > [ファイル] > [iOS] > [ソース] > [ヘッダー ファイル] を選択して SDK's Objective C API を使用できるようにします。
   
    ![][4]
7. ブリッジ ヘッダー ファイルを編集して、Mobile Engagement Objective-C コードを Swift コードに公開し、次の imports を追加します。
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. [ビルドの設定] で、Swift コンパイラの Objective-C Bridging Header のビルド設定で Swift コンパイラ - コード生成にこのヘッダーへのパスがあることを確認します。 パスの例を次に示します。**$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (パスによる)**
   
   ![][6]
9. アプリの *[接続情報]* ページで Azure ポータルに戻り、[接続文字列] をコピーします。
   
   ![][5]
10. `didFinishLaunchingWithOptions` 認証で、接続文字列を貼り付けます。
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a id="monitor"></a>リアルタイム監視の有効化
データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

1. **ViewController.swift** ファイルを開き、**ViewController** の基本クラスを **EngagementViewController** に変更します。
   
    `class ViewController : EngagementViewController {`

## <a id="monitor"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする
Mobile Engagement により、ユーザーと通信を行い、キャンペーンのコンテキストに関するプッシュ通知とアプリ内メッセージングを届けることができます。 このモジュールは、 Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するためにアプリをセットアップします。

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>アプリがサイレント プッシュ通知を受信できるようにする
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>リーチ ライブラリをプロジェクトに追加する
1. プロジェクトを右クリックします
2. `Add file to ...` を選択します
3. SDK を抽出したフォルダーに移動します
4. `EngagementReach` フォルダーを選択します
5. [追加] をクリックします。
6. ブリッジ ヘッダー ファイルを編集して、Mobile Engagement Objective-C Reach ヘッダーを Swift コードに公開し、次の imports を追加します。
   
        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>アプリケーション デリゲートを変更する
1. `didFinishLaunchingWithOptions` 内にリーチ モジュールを作成し、それを既存の Engagement 初期化行に渡します。
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>アプリで APNS プッシュ通知を受信できるようにする
1. 次の行を `didFinishLaunchingWithOptions` メソッドに追加します。
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. 次のように、`didRegisterForRemoteNotificationsWithDeviceToken` メソッドを追加します。
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. 次のように、 `didReceiveRemoteNotification:fetchCompletionHandler:` メソッドを追加します。
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[モバイル エンゲージメント iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
