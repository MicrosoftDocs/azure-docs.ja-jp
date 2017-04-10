---
title: "Cordova/Phonegap 用 Azure Mobile Engagement の使用"
description: "Cordova/Phonegap アプリで Azure Mobile Engagement の分析機能やプッシュ通知を使用する方法について説明します。"
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Cordova/Phonegap 用 Azure Mobile Engagement の使用
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用して、アプリの使用状況を把握する方法と、Cordova を使用して開発されたモバイル アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。

このチュートリアルでは、Mac を使用して空の Cordova アプリを作成してから、Mobile Engagement SDK と統合します。 このアプリは、基本的な分析データを収集し、iOS では Apple Push Notification System (APNS)、Android では Google Cloud Messaging (GCM) を使用してプッシュ通知を受信します。 このアプリを、テスト用に iOS デバイスまたは Android デバイスにデプロイします。 

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started)をご覧ください。
> 
> 

このチュートリアルには、次のものが必要です。

* XCode (iOS へのデプロイ用)。Mac App Store からインストールできます。
* [Android SDK およびエミュレーター](http://developer.android.com/sdk/installing/index.html) (Android へのデプロイ用)
* APNS のプッシュ通知証明書 (.p12)。Apple Dev Center から入手できます。
* GCM 用の GCM プロジェクト番号。Google デベロッパー コンソールから入手できます。
* [Mobile Engagement 用の Cordoca プラグイン](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Cordova プラグインのソース コードと ReadMe ファイルは、[GitHub](https://github.com/Azure/azure-mobile-engagement-cordova) にあります。
> 
> 

## <a id="setup-azme"></a>Cordova アプリ用に Mobile Engagement を設定する
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続する
このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。 

統合のデモンストレーションを行うために、Cordova で基本的なアプリを作成します。

### <a name="create-a-new-cordova-project"></a>新しい Cordova プロジェクトを作成します。
1. Mac コンピューターで *[Terminal]* ウィンドウを起動して次を入力し、既定のテンプレートから新しい Cordova プロジェクトを作成する次のコードを入力します。 iOS アプリのデプロイに最終的に使用する発行プロファイルが、App ID として「com.mycompany.myapp」を使用していることを確認します。 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. 次を実行して **iOS** のプロジェクトを構成し、その構成を iOS シミュレーターで実行します。
   
        $ cordova platform add ios 
        $ cordova run ios
3. 次のコマンドを実行して **Android** のプロジェクトを構成し、その構成を Android エミュレーターで実行します。 Android SDK エミュレーターで、ターゲットが Google API (Google Inc.) に、CPU/ABI が Google API ARM に設定されていることを確認してください。  
   
        $ cordova platform add android
        $ cordova run android
4. Cordova Console プラグインを追加します。 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>アプリを Mobile Engagement のバックエンドに接続する
1. Azure Mobile Engagement Cordova プラグインに変数値を設定しながら、このプラグインをインストールします。
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

"*Android Reach アイコン*": 拡張子も描画可能なプレフィックスも付いていないリソース名を指定する必要があります (例: mynotificationicon)。また、アイコン ファイルは Android プロジェクトにコピーする必要があります (platforms/android/res/drawable)。

"*iOS Reach アイコン*": 拡張子付きのリソース名を指定する必要があります (例: mynotificationicon.png)。また、アイコン ファイルは ([フィールドの追加] メニューを使用して) XCode と共に iOS プロジェクトに追加する必要があります。

## <a id="monitor"></a>リアルタイム監視の有効化
1. Cordova プロジェクトで、 **www/js/index.js** を編集して、 *deviceReady* イベントが受信された場合に新しいアクティビティを宣言する Mobile Engagement への呼び出しを追加します。
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. アプリケーションを実行します。
   
   * **iOS の場合**
     
       [ `Terminal` ] ウィンドウで次を実行して、新しいシミュレーター インスタンスでアプリを起動します。
     
           cordova run ios
   * **Android の場合**
     
       [ `Terminal` ] ウィンドウで次を実行して、新しいエミュレーター インスタンスでアプリを起動します。
     
           cordova run android
3. コンソールのログでは、次のことを確認できます。
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>リアルタイム監視を使用してアプリを接続する
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする
Mobile Engagement を導入すると、キャンペーンとの関連でプッシュ通知とアプリ内メッセージングを利用し、ユーザーとのやり取りを行うことができます。 このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するためにアプリをセットアップします。

### <a name="configure-push-credentials-for-mobile-engagement"></a>Mobile Engagement のプッシュの資格情報を設定する
Mobile Engagement がユーザーに代わりプッシュ通知を送信できるようにするには、Mobile Engagement に Apple iOS 証明書または GCM サーバーの API キーへのアクセスを許可する必要があります。 

1. Mobile Engagement ポータルに移動します。 このプロジェクト用に使用しているアプリを対象にしていることを確認し、下部にある **[エンゲージ]** ボタンをクリックします。
   
    ![][1]
2. Engagement ポータルの設定ページが表示されます。 ここで、 **[ネイティブ プッシュ通知]** セクションをクリックします。
   
    ![][2]
3. iOS の証明書/GCM サーバーの API キーの設定
   
    **(iOS)**
   
    a. .p12 を選択してアップロードし、パスワードを入力します。
   
    ![][3]
   
    **(Android)**
   
    a. [GCM 設定] セクションの **[API キー]** の前にある編集アイコンをクリックし、表示されたポップアップの中に GCM サーバー キーを貼り付け、**[OK]** をクリックします。 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Cordova アプリでのプッシュ通知の有効化
**www/js/index.js** を編集して、プッシュ通知の要求とハンドラーの宣言を行う Mobile Engagement への呼び出しを追加します。

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>アプリの実行
**(iOS)**

1. iOS では実在するデバイスへのプッシュ通知だけが許可されているため、XCode を使用してアプリをビルドし、デバイスにデプロイして、プッシュ通知をテストします。 Cordova プロジェクトを作成した場所の **...\platforms\ios** に移動します。 XCode でネイティブ .xcodeproj ファイルを開きます。 
2. 先ほど Mobile Engagement ポータルにアップロードした証明書と、Cordova アプリの作成中に指定した App Id を含むプロビジョニング プロファイルが設定されたアカウントを使用して、Cordova アプリをビルドして iOS デバイスにデプロイします。 XCode で **Resources\*-info.plist** ファイルの *Bundle ID* をチェックアウトして、照合することができます。 
3. デバイスに、アプリが通知を送信する許可を要求したことを示す標準の iOS ポップアップが表示されます。 送信を許可します。 

**(Android)**

GCM 通知は Android エミュレーターでサポートされているため、エミュレーターを使用するだけで Android アプリを実行することができます。 

    cordova run android

## <a id="send"></a>アプリへ通知を送信する
デバイスで実行されているアプリにプッシュ通知を送る簡単なプッシュ通知キャンペーンを作成してみましょう。

1. Mobile Engagement ポータルで **[リーチ]** タブに移動します。
2. **[新しいお知らせ]** をクリックして、プッシュ キャンペーンを作成します。
   
    ![][6]
3. 情報を入力してキャンペーン **(Android)**
   
   * キャンペーンの **名前** を指定します。 
   * **[配信タイプ]** として *[システム通知]*、*[シンプル]* を選択します。
   * **[配信時刻]** として *[任意の時刻]*
   * プッシュ通知の 1 行目になる **[タイトル]** を指定します。
   * **[メッセージ]** に、通知のメッセージ本文となるテキストを入力します。 
     
     ![][11]
4. 情報を入力してキャンペーン **(iOS)**
   
   * キャンペーンの **名前** を指定します。 
   * **[配信時刻]** として *[アプリ外のみ]*
   * プッシュ通知の 1 行目になる **[タイトル]** を指定します。
   * **[メッセージ]** に、通知のメッセージ本文となるテキストを入力します。 
     
     ![][12]
5. スクロール ダウンし、コンテンツ セクションで  **通知のみ**
   
    ![][8]
6. (オプション) アクション URL を指定することもできます。 この URL では、プラグインの **AZME\_REDIRECT\_URL** 変数の構成時に指定した URL スキームを必ず使用します (例: *myapp://test*)。  
7. 最も基本的なキャンペーンの設定が完了しました。 もう一度下にスクロールし、 **[作成]** ボタンをクリックしてキャンペーンを保存します。
8. 最後に、キャンペーンを **アクティブ化** します。
   
    ![][10]
9. このキャンペーンでは、デバイスまたはエミュレーターにプッシュ通知が表示されるはずです。 

## <a id="next-steps"></a>次のステップ
[Cordova Mobile Engagement SDK で使用できるすべてのメソッドの概要](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png


