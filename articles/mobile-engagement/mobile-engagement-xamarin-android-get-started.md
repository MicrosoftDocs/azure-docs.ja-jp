---
title: "Xamarin.Android 用 Azure Mobile Engagement の使用"
description: "Xamarin.Android アプリの分析やプッシュ通知で Azure Mobile Engagement を使用する方法について説明します。"
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: 
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 7b3d01b32c2d5a40448fc22861cd45f612238f2f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Xamarin.Android アプリ用 Azure Mobile Engagement の使用
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握する方法と、Xamarin.Android アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Mobile Engagement を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。 このシナリオでは、基本的なデータを収集し、Google Cloud Messaging (GCM) を使ってプッシュ通知を受信する空の Xamarin.Android アプリを作成します。

> [!NOTE]
> Azure Mobile Engagement サービスは 2018 年 3 月に停止予定であり、現在は既存のお客様のみご利用いただけます。 詳細については、「[Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)」を参照してください。

このチュートリアルには、次のものが必要です。

* [Xamarin Studio](http://xamarin.com/studio)。 Visual Studio with Xamarin を使用することもできますが、このチュートリアルでは Xamarin Studio を使用します。 インストールの手順については、「 [Visual Studio と Xamarin の設定とインストール](https://msdn.microsoft.com/library/mt613162.aspx)」を参照してください。
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started)」をご覧ください。
> 
> 

## <a id="setup-azme"></a>Android アプリ用のモバイル エンゲージメントの設定
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します
このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。 

統合のデモンストレーションを行うために、Xamarin Studio で基本的なアプリを作成します。

### <a name="create-a-new-xamarinandroid-project"></a>新しい Xamarin.Android プロジェクトを作成する
1. **Xamarin Studio** を起動して、**[File (ファイル)]** -> **[New (新規)]** -> **[Solution (ソリューション)]** の順に移動します。 
   
    ![][1]
2. **[Android App (Android アプリ)]** を選択し、選択されている言語が **[C#]** であることを確認してから、**[Next (次へ)]** をクリックします。
   
    ![][2]
3. **アプリ名**と**組織 ID** を入力します。 **[Google Play Services]** チェック ボックスがオンになっていることを確認し、**[Next (次へ)]** をクリックします。 
   
    ![][3]
4. 必要に応じて **[Project Name (プロジェクト名)]**、**[Solution Name (ソリューション名)]**、**[Location (位置)]** を変更し、**[Create (作成)]** をクリックします。
   
    ![][4]

Xamarin Studio で、Mobile Engagement の統合先のアプリが作成されます。 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>アプリを Mobile Engagement のバックエンドに接続する
1. [Solution (ソリューション)] ウィンドウの **Packages** フォルダーを右クリックし、**[Add Packages (パッケージの追加)]** を選択します。
   
    ![][5]
2. **Microsoft Azure Mobile Engagement Xamarin SDK** を探してソリューションに追加します。  
   
    ![][6]
3. **MainActivity.cs** を開き、次の using ステートメントを追加します。
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. `OnCreate` メソッドに、Mobile Engagement バックエンドとの接続を初期化する次のコードを追加します。 **ConnectionString**を必ず追加してください。 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>アクセス権限とサービス宣言を追加する
1. Properties フォルダーの下にある **Manifest.xml** ファイルを開きます。 [Source] \(ソース) タブを選択して、XML ソースを直接更新します。
2. 次のアクセス許可を、プロジェクトの Manifest.xml 内の `<application>` タグの直前または直後に追加します (Manifest.xml は **Properties** フォルダーの下にあります)。
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. 次の設定を `<application>`タグと`</application>` タグの間に追加して、エージェント サービスを宣言します。
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. 貼り付けたばかりのコードの label 内の `"<Your application name>"` をアプリケーション名に置き換えます。 この内容は **[設定]** メニューに表示されます。デバイスで実行されているサービスをユーザーはここで確認できます。 たとえば、このラベルに「サービス」という語句を追加できます。

### <a name="send-a-screen-to-mobile-engagement"></a>画面を Mobile Engagement に送信する
データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面を Mobile Engagement のバックエンドに送信する必要があります。 これを行うには、`MainActivity` が `Activity` ではなく `EngagementActivity` から継承するようにします。

    public class MainActivity : EngagementActivity

`EngagementActivity` からの継承ができない場合は、`.StartActivity` メソッドと `.EndActivity` メソッドを、それぞれ `OnResume` と `OnPause` に追加する必要があります。  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする
Mobile Engagement を導入すると、プッシュ通知とアプリ内メッセージングを利用して、ユーザーにキャンペーン情報を提供できます。 このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するようにアプリをセットアップします。

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png

