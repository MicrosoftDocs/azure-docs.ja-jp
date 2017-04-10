---
title: "Windows ユニバーサル アプリの Azure Mobile Engagement の概要"
description: "Windows ユニバーサル アプリで Azure Mobile Engagement の分析機能やプッシュ通知を使用する方法について説明します。"
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 939d6adc548d5d6ef66909bcf52f11a4106c3be9
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Windows ユニバーサル アプリの Azure Mobile Engagement の概要
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、アプリの使用状況を理解して、プッシュ通知を Windows ユニバーサル アプリケーションのセグメント化されたユーザーに送信するために、Azure モバイル エンゲージメントの使用方法を説明します。
このチュートリアルでは、モバイル エンゲージメントを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。 基本的なアプリ使用状況データを収集し、Windows 通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ユニバーサル アプリを作成します。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Windows ユニバーサル アプリの Mobile Engagement の設定
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnecting-appaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します
このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。 完全な統合のドキュメントについては、「 [Mobile Engagement Windows ユニバーサル SDK の統合](mobile-engagement-windows-store-sdk-overview.md)」を参照してください。

統合の実例を示すために、Visual Studio で基本的なアプリを作成します。

### <a name="create-a-windows-universal-app-project"></a>Windows ユニバーサル アプリ プロジェクトを作成する
次の手順では、以前のバージョンの Visual Studio と類似した手順で、Visual Studio 2015 を使用します。

1. Visual Studio を起動し、**ホーム**画面で **[新しいプロジェクト]** を選択します。
2. ポップアップ ウィンドウで **[Windows]** -> **[ユニバーサル]** -> **[空のアプリ (ユニバーサル Windows)]** の順に選択します。 アプリの**名前**と**ソリューション名**を入力し、**[OK]** をクリックします。

    ![][1]

これで、後で Azure Mobile Engagement SDK を統合する Windows ユニバーサル アプリ プロジェクトが作成されました。

### <a name="connect-your-app-to-mobile-engagement-backend"></a>アプリを Mobile Engagement のバックエンドに接続する
1. プロジェクトに [MicrosoftAzure.MobileEngagement] Nuget パッケージをインストールします。 Windows と Windows Phone の両方のプラットフォームを対象としている場合、両方のプロジェクトにこれを行う必要があります。 Windows 8.x および Windows Phone 8.1 の場合は、同じ Nuget パッケージによって各プロジェクトに適切なプラットフォーム固有のバイナリが配置されます。
2. **Package.appxmanifest** を開き、そこに次の機能を追加します。

        Internet (Client)

    ![][2]
3. 先にコピーした Mobile Engagement アプリの接続文字列を `Resources\EngagementConfiguration.xml` ファイルの `<connectionString>` タグと `</connectionString>` タグの間にコピーします。

    ![][3]

    > [!TIP]
    > アプリの対象プラットフォームを Windows と Windows Phone の両方にする場合、2 つの Mobile Engagement アプリケーションを作成する必要があります (サポートされているプラットフォームごとに&1; つずつ)。 2 つのアプリがあれば、対象ユーザーを適切に分け、各プラットフォームに合わせた通知を送信できます。

    > [!IMPORTANT]
    > NuGet は、Windows 10 UWP アプリケーションで SDK のリソースを自動的にコピーしません。 NuGet パッケージのインストール時に表示される手順 (readme.txt) に従って、手動で行う必要があります。  

1. `App.xaml.cs` ファイルで次の操作を行います。

    a. `using` ステートメントを追加します。

            using Microsoft.Azure.Engagement;

    b. Engagement を初期化するメソッドを追加します。

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. **OnLaunched** メソッドで SDK を初期化します。

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. **OnActivated** メソッドに次を挿入します。このメソッドがない場合は追加します。

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a name="a-idmonitoraenable-real-time-monitoring"></a><a id="monitor"></a>リアルタイム監視を有効にする
データを送信してユーザーがアクティブであることを確認するには、少なくとも&1; つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

1. **MainPage.xaml.cs** に、次の `using` ステートメントを追加します。

    using Microsoft.Azure.Engagement.Overlay;
2. **MainPage** の基本クラスを **Page** から **EngagementPageOverlay** に変更します。

        class MainPage : EngagementPageOverlay
3. `MainPage.xaml` ファイルで次の操作を行います。

    a. 次の内容を名前空間宣言に追加します。

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. XML タグ名の **Page** を **engagement:EngagementPageOverlay** に置き換えます。

> [!IMPORTANT]
> ページが `OnNavigatedTo` メソッドをオーバーライドする場合は、必ず `base.OnNavigatedTo(e)` を呼び出します。 そうしないと、アクティビティが報告されません (`EngagementPage` は、`OnNavigatedTo` メソッド内で `StartActivity` を呼び出します)。 これは既定のテンプレートに `OnNavigatedTo` メソッドがある Windows Phone プロジェクトで特に重要です。
>
> **Windows 10 ユニバーサル アプリ**の場合は、上記の方法ではなく、「[Windows ユニバーサル アプリ Engagement SDK を使用した詳細なレポート](mobile-engagement-windows-store-advanced-reporting.md)」の「推奨される方法: Page クラスをオーバーロードします」で推奨されている方法を使用してください。

## <a name="a-idmonitoraconnect-app-with-real-time-monitoring"></a><a id="monitor"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegrate-pushaenable-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする
Mobile Engagement を導入すると、キャンペーンとの関連でプッシュ通知とアプリ内メッセージングを利用し、ユーザーに情報を提供できます。 このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するようにアプリをセットアップします。

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>アプリが WNS プッシュ通知を受信できるようにする
1. `Package.appxmanifest` ファイルの **[アプリケーション]** タブの **[通知]** で **[トースト対応:]** に **[はい]** を設定します。

    ![][5]

### <a name="initialize-the-reach-sdk"></a>REACH SDK を初期化する
`App.xaml.cs` で、エージェント初期化の直後に **InitEngagement** 関数で **EngagementReach.Instance.Init(e);** を呼び出します。

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

トーストを送信する準備ができました。 それでは、この基本的な統合が正しく設定されているかどうかを検証してみましょう。

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>通知を送信するために Mobile Engagement へのアクセスを許可する
1. Web ブラウザーで [Windows ストア デベロッパー センター]を開き、ログインします。必要に応じてアカウントを作成してください。
2. 右上隅の **[ダッシュボード]** をクリックし、左側のパネル メニューで **[新しいアプリの作成]** をクリックします。

    ![][9]
3. アプリの名前を予約して、アプリを作成します。

    ![][10]
4. アプリが作成されたら、左側のメニューから **[サービス]、[プッシュ通知]** の順に進みます。

    ![][11]
5. [プッシュ通知] セクションで、 **Live サービス サイト** のリンクをクリックします。

    ![][12]
6. プッシュ通知用資格情報セクションに移動します。 **[アプリケーション設定]** セクションが表示されていることを確認し、**[パッケージ SID]** と **[クライアント シークレット]** をコピーします。

    ![][13]
7. Mobile Engagement ポータルの **[設定]** に移動し、左側の **[ネイティブ プッシュ]** セクションをクリックします。 次に、**[編集]** ボタンをクリックし、次に示すように、**[パッケージ セキュリティ ID (SID)]** と **[シークレット キー]** に入力します。

    ![][6]
8. 最後に、アプリ ストアに作成されたこのアプリに Visual Studio アプリケーションが関連付けます。 Visual Studio で **[アプリケーションをストアと関連付ける]** をクリックします。

    ![][7]

## <a name="a-idsendasend-a-notification-to-your-app"></a><a id="send"></a>アプリへ通知を送信する
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

アプリが実行中の場合は、アプリ内通知が表示されます。 アプリが閉じている場合は、トースト通知が表示されます。
トースト通知ではなくアプリ内通知が表示され、Visual Studio のデバッグ モードでアプリを実行している場合は、ツール バーの **[ライフサイクル イベント]、[中断]** の順に選択し、アプリが中断していることを確認します。 Visual Studio でアプリケーションをデバッグしているときに [ホーム] ボタンをクリックしただけではアプリケーションは中断されず、アプリ内通知は表示されても、トースト通知は表示されないことがあります。  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows ストア デベロッパー センター]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png

