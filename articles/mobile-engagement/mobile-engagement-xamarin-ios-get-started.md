---
title: "Xamarin.iOS 用 Azure Mobile Engagement の使用"
description: "Xamarin.iOS アプリの分析やプッシュ通知で Azure Mobile Engagement を使用する方法を説明します。"
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0448209e-fff6-47bd-985c-2cf074bac12f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9938c3e994acf31244825b1afb347f8c9f90ebe3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Xamarin.iOS アプリ用 Azure Mobile Engagement の使用
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、Xamarin.iOS アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、基本的なデータを収集し、Apple Push Notification System (APNS) を使ってプッシュ通知を受信する空の Xamarin.iOS アプリケーションを作成します。

> [!NOTE]
> Azure Mobile Engagement サービスは 2018 年 3 月に停止予定であり、現在は既存のお客様のみご利用いただけます。 詳細については、「[Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)」を参照してください。

このチュートリアルには、次のものが必要です。

* [Xamarin Studio](http://xamarin.com/studio)。 Visual Studio with Xamarin を使用することもできますが、このチュートリアルでは Xamarin Studio を使用します。 インストールの手順については、「 [Visual Studio と Xamarin の設定とインストール](https://msdn.microsoft.com/library/mt613162.aspx)」を参照してください。 
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started)」をご覧ください。
> 
> 

## <a id="setup-azme"></a>iOS アプリ用に Mobile Engagement を設定する
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します
このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。

統合のデモンストレーションを行うために、Xamarin で基本的なアプリを作成します。

### <a name="create-a-new-xamarinios-project"></a>新しい Xamarin.iOS プロジェクトを作成する
1. Xamarin Studio を起動します。 **[File (ファイル)]** -> **[New (新規)]** -> **[Solution (ソリューション)]** の順に移動します。 
   
    ![][1]
2. **[Single View App (単一ビュー アプリ)]** を選択し、選択されている言語が **[C#]** であることを確認してから、**[Next (次へ)]** をクリックします。
   
    ![][2]
3. **[App Name (アプリ名)]** と **[Organization Identifier (組織 ID)]** を入力し、**[Next (次へ)]** をクリックします。 
   
    ![][3]
   
   > [!IMPORTANT]
   > iOS アプリのデプロイに最終的に使用する発行プロファイルでは、ここに表示されたバンドル識別子と完全に一致するアプリ ID を必ず使用してください。 
   > 
   > 
4. 必要に応じて **[Project Name (プロジェクト名)]**、**[Solution Name (ソリューション名)]**、**[Location (位置)]** を変更し、**[Create (作成)]** をクリックします。
   
    ![][4]

Xamarin Studio で、Mobile Engagement の統合先のデモ アプリが作成されます。 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>アプリを Mobile Engagement のバックエンドに接続する
1. [Solution (ソリューション)] ウィンドウの **Packages** フォルダーを右クリックし、**[Add Packages (パッケージの追加)]** を選択します。
   
    ![][5]
2. **Microsoft Azure Mobile Engagement Xamarin SDK** を探してソリューションに追加します。  
   
    ![][6]
3. **AppDelegate.cs** を開き、次の using ステートメントを追加します。
   
        using Microsoft.Azure.Engagement.Xamarin;
4. **FinishedLaunching** メソッドに、Mobile Engagement バックエンドとの接続を初期化する次のコードを追加します。 **ConnectionString**を必ず追加してください。 また、次のコードでは、Mobile Engagement SDK によって追加されるダミーの **NotificationIcon** も使用されています。これは、置き換えることができます。 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a id="monitor"></a>リアルタイム監視の有効化
データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面を Mobile Engagement のバックエンドに送信する必要があります。

1. **ViewController.cs** を開き、次の using ステートメントを追加します。
   
        using Microsoft.Azure.Engagement.Xamarin;
2. `ViewController` の継承元となるクラスを `UIViewController` から `EngagementViewController` に置き換えます。 

## <a id="monitor"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする
Mobile Engagement により、ユーザーと通信を行い、キャンペーンに関するプッシュ通知とアプリ内メッセージングを届けることができます。 このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するようにアプリをセットアップします。

### <a name="modify-your-application-delegate"></a>アプリケーション デリゲートを変更する
1. **AppDelegate.cs** を開き、次の using ステートメントを追加します。
   
        using System; 
2. ここで、`FinishedLaunching` メソッド内の `EngagementAgent.init(...)` の後に、プッシュ メッセージを登録する次のコードを追加します。
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. 最後に、次のメソッドを更新するか、追加します。
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. ソリューションの **Info.plist** ファイルで、**バンドル識別子**が、Apple Dev Center のプロビジョニング プロファイルにある**アプリ ID** と一致していることを確認します。 
   
    ![][7]
5. その **Info.plist** ファイルで、**[Enable Background Modes (バックグラウンド モードを有効にする)]** と **[Remote Notifications (リモート通知)]** のチェック ボックスがオンになっていることを確認します。 
   
     ![][8]
6. この発行プロファイルに関連付けたデバイスでアプリを実行します。 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
