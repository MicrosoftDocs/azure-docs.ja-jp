---
title: "Windows Phone Silverlight アプリの Azure Mobile Engagement の概要"
description: "Windows Phone Silverlight アプリで Azure Mobile Engagement の分析機能やプッシュ通知を使用する方法について説明します。"
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: aa34692f-87f7-47c6-a20c-a1972750bc25
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 3f1f8d74eb2f562991b351cae87c372897644cf8
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Windows Phone Silverlight アプリの Azure Mobile Engagement の概要
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、Windows Phone Silverlight アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、モバイル エンゲージメントを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。 チュートリアル内で空白の Windows Phone Silverlight アプリを作成し、このアプリで基本的なデータを収集して、Microsoft Push Notification Service (MPNS) を使用してプッシュ通知を受信します。

> [!NOTE]
> Windows Phone 8.1 (Silverlight 以外) を対象としている場合は、 [Windows Universal チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を参照してください。
> 
> 

このチュートリアルには、次のものが必要です。

* Visual Studio 2013
* [MicrosoftAzure.MobileEngagement] Nuget パッケージ

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started)」をご覧ください。
> 
> 

## <a name="a-idsetup-azmeasetup-mobile-engagement-for-your-windows-phone-app"></a><a id="setup-azme"></a>Windows Phone アプリ用に Mobile Engagement を設定する
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnecting-appaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します
このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。 完全な統合のドキュメントについては、「 [Mobile Engagement Windows Phone SDK 統合](mobile-engagement-windows-phone-sdk-overview.md)

統合のデモンストレーションを行うために、Visual Studio で基本的なアプリを作成します。

### <a name="create-a-new-windows-phone-silverlight-project"></a>新しい Windows Phone Silverlight プロジェクトを作成する
次の手順では、以前のバージョンの Visual Studio と類似した手順で、Visual Studio 2015 を使用します。 

1. Visual Studio を起動し、**ホーム**画面で **[新しいプロジェクト]** を選択します。
2. ポップアップで、**[Windows 8]** -> **[Windows Phone]** -> **[空のアプリ (Windows Phone Silverlight)]** の順に選択します。 アプリの**名前**と**ソリューション名**を入力し、**[OK]** をクリックします。
   
    ![][1]
3. ターゲットとして **Windows Phone 8.0** または **Windows Phone 8.1** を選択できます。

これで、Azure Mobile Engagement SDK を統合する新しい Windows Phone Silverlight アプリが作成されました。

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>アプリを Mobile Engagement のバックエンドに接続します
1. [MicrosoftAzure.MobileEngagement] Nuget パッケージをプロジェクトにインストールします。
2. `WMAppManifest.xml` ([プロパティ] フォルダーの下) を開き、`<Capabilities />` タグで次が宣言されていることを確認します (宣言されていない場合は追加します)。
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. 先ほどコピーした Mobile Engagement アプリの接続文字列を `Resources\EngagementConfiguration.xml` ファイルの `<connectionString>` タグと `</connectionString>` タグの間に貼り付けます。
   
    ![][3]
4. `App.xaml.cs` ファイルで次の操作を行います。
   
    a. `using` ステートメントを追加します。
   
            using Microsoft.Azure.Engagement;
   
    b. `Application_Launching` メソッドで SDK を初期化します。
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. `Application_Activated` に次を挿入します。
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a name="a-idmonitoraenable-real-time-monitoring"></a><a id="monitor"></a>リアルタイム監視を有効にする
データを送信してユーザーがアクティブであることを確認するには、少なくとも&1; つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

1. MainPage.xaml.cs に、`using` ステートメントを追加します。
   
        using Microsoft.Azure.Engagement;
2. **PhoneApplicationPage** の前にある **MainPage** の基本クラスを **EngagementPage** に置き換えます。
   
        class MainPage : EngagementPage 
3. `MainPage.xml` ファイルで、次の操作を行います。
   
    a. 次の内容を名前空間宣言に追加します。
   
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    b. XML タグ名の `phone:PhoneApplicationPage` を `engagement:EngagementPage` に置き換えます。

## <a name="a-idmonitoraconnect-app-with-real-time-monitoring"></a><a id="monitor"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegrate-pushaenable-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする
Mobile Engagement を導入すると、キャンペーンとの関連でプッシュ通知とアプリ内メッセージングを利用し、ユーザーに情報を提供できます。 このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するようにアプリをセットアップします。

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>アプリが WNS プッシュ通知を受信できるようにする
`WMAppManifest.xml` ファイルに新しい Capabilities を追加します。

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>REACH SDK を初期化する
1. `App.xaml.cs` で、エージェントの初期化の直後に、**Application_Launching** 関数で `EngagementReach.Instance.Init();` を呼び出します。
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. `App.xaml.cs` で、エージェントの初期化の直後に、**Application_Activated** 関数で `EngagementReach.Instance.OnActivated(e);` を呼び出します。
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

設定が完了しました。 それでは、この基本的な統合が正しく設定されているかどうかを検証してみましょう。

## <a name="a-idsendasend-a-notification-to-your-app"></a><a id="send"></a>アプリへ通知を送信する
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

アプリが開いている場合、通知はアプリ内通知としてデバイスに表示されます。アプリが開いていない場合は、次のようにトースト通知として表示されます。 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

