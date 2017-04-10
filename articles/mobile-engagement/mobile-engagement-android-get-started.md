---
title: "Android アプリの Azure Mobile Engagement の使用"
description: "Azure Mobile Engagement を使用して、Android アプリの分析やプッシュ通知を送信する方法について説明します。"
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 830eb6627cae71f358b9790791b1d86f7c82c566
ms.openlocfilehash: dc255a930bf71e6ef6d964bc5e3472a38ce4e467


---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Android アプリ用 Azure Mobile Engagement の使用
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、Android アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、モバイル エンゲージメントを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。 このシナリオでは、基本的なデータを収集する空の Android アプリを作成し、Google Cloud Messaging (GCM) を使ってプッシュ通知を受信します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、Android Studio 統合開発環境と最新の Android プラットフォームを含む [Android Developer Tools](https://developer.android.com/sdk/index.html)が必要です。

また、 [Mobile Engagement Android SDK](https://aka.ms/vq9mfn)も必要です。

> [!IMPORTANT]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要になります。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started)」をご覧ください。
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Android アプリ用の Mobile Engagement の設定
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>アプリを Mobile Engagement のバックエンドに接続する
このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。 統合の実例を示すために、Android Studio で基本的なアプリを作成します。

統合に関する完全なドキュメントについては、 [Mobile Engagement Android SDK の統合](mobile-engagement-android-sdk-overview.md)に関するページを参照してください。

### <a name="create-an-android-project"></a>Android プロジェクトの作成
1. **Android Studio** を開始し、ポップアップ画面で **[Start a new Android Studio project (新しい Android Studio プロジェクトを開始する)]** を選択します。

    ![][1]
2. アプリ名と会社のドメインを入力します。 後で必要になるため、入力内容をメモします。 **[次へ]**をクリックします。

    ![][2]
3. ターゲット フォーム ファクターと API レベルを選び、 **[Next]**をクリックします。

   > [!NOTE]
   > Mobile Engagement では、最小で API レベル 10 (Android 2.3.3) が必要です。
   >
   >

    ![][3]
4. ここでこのアプリの唯一の画面である **[Blank Activity (空のアクティビティ)]** を選択し、**[Next (次へ)]** をクリックします。

    ![][4]
5. 最後に、既定はそのままにして **[Finish]**をクリックします。

    ![][5]

Mobile Engagement を統合するデモ アプリが Android Studio によって作成されます。

### <a name="include-the-sdk-library-in-your-project"></a>SDK ライブラリをプロジェクトに含める
1. [Mobile Engagement Android SDK](https://aka.ms/vq9mfn)をダウンロードします。
2. アーカイブ ファイルをコンピューターのフォルダーに抽出します。
3. SDK の現在のバージョン用の .jar ライブラリを探し、クリップボードにコピーします。

      ![][6]
4. **[Project]** セクション (1) に移動し、[libs] フォルダー (2) に .jar を貼り付けます。

      ![][7]
5. ライブラリを読み込むには、プロジェクトを同期します。

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>接続文字列を使用してアプリを Mobile Engagement のバックエンドに接続します。
1. 次のコード行をアクティビティ作成の中にコピーします (アプリケーションの 1 か所でのみ実行する必要があります。通常はメイン アクティビティの中で実行します)。 このサンプル アプリの MainActivity を [src]、[main]、[java] フォルダーから開き、次を追加します。

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. Alt キーを押しながら Enter を押すか、次の import ステートメントを追加して、参照を解決します。

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. アプリの **[接続情報]** ページで Azure クラシック ポータルに戻り、**[接続文字列]** をコピーします。

      ![][9]
4. それを `setConnectionString` パラメーターにコピーし、次のコードに示されている文字列全体を置き換えます。

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>アクセス権限とサービス宣言を追加する
1. 次のアクセス権限をプロジェクトの Manifest.xml の `<application>` タグの直前または直後に追加します。

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. エージェント サービスを宣言するには、次のコードを `<application>` タグと `</application>` タグの間に追加します。

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. 貼り付けたコードで、ラベルの `"<Your application name>"` を置き換えます。これは、デバイスで実行中のサービスを確認できる **[設定]** メニューに表示されます。 たとえば、このラベルに「サービス」という語句を追加できます。

### <a name="send-a-screen-to-mobile-engagement"></a>画面を Mobile Engagement に送信する
データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

**MainActivity.java** に移動して、**MainActivity** の基底クラスを **EngagementActivity** に置き換えます。

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> 基底クラスが *Activity*ではない場合、別のクラスからの継承方法について、 [高度な Android レポート](mobile-engagement-android-advanced-reporting.md) に関するページを参照してください。
>
>

この簡単なサンプル シナリオでは、次の行をコメント アウトします。

    // setSupportActionBar(toolbar);

アプリで `ActionBar` を維持する場合は、 [高度な Android のレポート](mobile-engagement-android-advanced-reporting.md)に関するページを参照してください。

## <a name="connect-app-with-real-time-monitoring"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>プッシュ通知とアプリ内メッセージングを有効にする
キャンペーンの際に Mobile Engagement を利用すると、プッシュ通知とアプリ内メッセージングを利用して、ユーザーにキャンペーン情報を提供できます。 このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するようにアプリをセットアップします。

### <a name="copy-sdk-resources-in-your-project"></a>SDK リソースをプロジェクトにコピーする
1. SDK ダウンロード コンテンツに戻り、 **res** フォルダーをコピーします。

    ![][10]
2. Android Studio に戻り、プロジェクト ファイルの **main** ディレクトリを選択して貼り付け、リソースをプロジェクトに追加します。

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>次のステップ
[Android SDK](mobile-engagement-android-sdk-overview.md) に関するページに移動し、SDK の統合について詳細を確認します。

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png



<!--HONumber=Nov16_HO2-->


