---
title: "Azure Mobile Apps を使用した Apache Cordova アプリへのプッシュ通知の追加 |Microsoft Docs"
description: "Azure Mobile Apps を使用して Apache Cordova アプリにプッシュ通知を送信する方法について説明します。"
services: app-service\mobile
documentationcenter: javascript
manager: adrianha
editor: 
author: ysxu
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 47063276d7bb6bb3b3aac0cca4290dfbea5488f7
ms.openlocfilehash: 99b23de962f7ba338fcf3f9b2e96d58c3dcbe7bc
ms.lasthandoff: 02/16/2017


---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Apache Cordova アプリへのプッシュ通知の追加
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要
このチュートリアルでは、[Apache Cordova クイック スタート] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージを追加する必要があります。 詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作][1]」を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Visual Studio 2015 内で開発し、Google Android エミュレーター、Android デバイス、Windows デバイス、iOS デバイスで実行される Apache Cordova アプリケーションについて説明します。

このチュートリアルを完了するには、次のものが必要です。

* [Visual Studio Community 2015][2] 以降がインストールされている PC
* [Visual Studio Tools for Apache Cordova][4]
* [アクティブな Azure アカウント][3]
* 完成した [Apache Cordova クイック スタート][5] プロジェクト
* (Android) 確認済みの電子メール アドレスがある [Google アカウント][6]
* (iOS) [Apple Developer Program メンバーシップ][7]と iOS デバイス (iOS シミュレーターはプッシュ通知をサポートしていません)
* (Windows) [Windows ストア開発者アカウント][8]と Windows 10 デバイス

## <a name="configure-hub"></a>通知ハブを構成する
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[このセクションの手順を紹介するビデオを見る][9]

## <a name="update-the-server-project"></a>サーバー プロジェクトを更新する
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Cordova アプリを変更する
Cordova プッシュ プラグインとプラットフォーム固有のプッシュ サービスをインストールすることで、Apache Cordova アプリ プロジェクトがプッシュ通知を処理する準備ができていることを確認します。

#### <a name="update-the-cordova-version-in-your-project"></a>プロジェクトで Cordova のバージョンを更新する
プロジェクトで v6.1.1 より前のバージョンの Apache Cordova を使用している場合は、クライアント プロジェクトを更新します。 プロジェクトの更新方法:

* `config.xml` を右クリックして、構成デザイナーを開きます。
* [プラットフォーム] タブを選択します。
* **[Cordova CLI]** ボックスで 6.1.1 を選択します。
* **[ビルド]**、**[ソリューションのビルド]** の順に選択してプロジェクトを更新します。

#### <a name="install-the-push-plugin"></a>プッシュ プラグインをインストールする
Apache Cordova アプリケーションでは、デバイスやネットワークの機能をネイティブで処理しません。  これらの機能は、[npm][10] または GitHub で公開されているプラグインによって提供されます。  ネットワーク プッシュ通知の処理には、 `phonegap-plugin-push` プラグインを使用します。

次のいずれかの方法でプッシュ プラグインをインストールできます。

**コマンド プロンプトから:**

次のコマンドを実行します。

    cordova plugin add phonegap-plugin-push

**Visual Studio 内から:**

1. ソリューション エクスプローラーで `config.xml` ファイルを開き、**[プラグイン]** > **[カスタム]** をクリックします。次に、**[Git]** をインストール元として選択し、「`https://github.com/phonegap/phonegap-plugin-push`」をソースとして入力します。

   ![][img1]

2. インストール元の横にある矢印をクリックします。
3. Google デベロッパー コンソール プロジェクト用のプロジェクト ID (数値) を既に所有している場合は、**SENDER_ID** のボックスに追加できます。 それ以外の場合は、777777 などのプレースホルダーの値を入力します。  Android を対象としている場合は、後からこの値を config.xml で更新することができます。
4. **[追加]**をクリックします。

これでプッシュ プラグインがインストールされました。

#### <a name="install-the-device-plugin"></a>デバイス プラグインをインストールする
プッシュ プラグインのインストールに使用したものと同じ手順に従います。  コア プラグインのリストから、デバイス プラグインを追加します (**[プラグイン]** > **[コア]** をクリックして検索します)。 このプラグインはプラットフォーム名の取得に必要です。

#### <a name="register-your-device-on-application-start-up"></a>アプリケーションのスタートアップ時にデバイスを登録する
最初に、最小限の Android コードを追加します。 その後、iOS または Windows 10 で実行できるようにアプリを変更します。

1. ログイン プロセスのコールバック時、つまり **onDeviceReady** メソッドの一番下に **registerForPushNotifications** の呼び出しを追加します。

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    この例は、認証が成功した後の **registerForPushNotifications** の呼び出しを示しています。  `registerForPushNotifications()` は必要とされる頻度で呼び出すことができます。

2. 新しい **registerForPushNotifications** メソッドを次のように追加します。

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }
3. (Android) 前述のコードで、`Your_Project_ID` を、 [Google デベロッパー コンソール][18]から入手したアプリのプロジェクト ID (数値) に置き換えます。

## <a name="optional-configure-and-run-the-app-on-android"></a>(省略可能) Android でアプリを構成して実行する
このセクションを完了すると、Android 用のプッシュ通知を有効にすることができます。

#### <a name="enable-gcm"></a>Firebase Cloud Messaging を有効にする
初期状態で Google Android プラットフォームをターゲットとしているため、Firebase Cloud Messaging を有効にする必要があります。

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>FCM を使用してプッシュ要求を送信するようにモバイル アプリ バックエンドを構成する
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Android 用に Cordova アプリを構成する
Cordova アプリで config.xml を開き、`Your_Project_ID` を [Google デベロッパー コンソール][18]から入手したアプリのプロジェクト ID (数値) に置き換えます。

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

index.js を開き、コードを更新して、プロジェクト ID (数値) を使用できるようにします。

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>USB デバッグ用に Android デバイスを構成する
アプリケーションを Android デバイスにデプロイする前に、USB デバッグを有効にする必要があります。  Android フォンで次の手順を実行します。

1. **[設定]** > **[端末情報]** を選択して移動した後、デベロッパー モードが有効になるまで (約 7 回) **[ビルド番号]** をタップします。
2. **[設定]** > **[開発者向けオプション]** を選択して戻り、**[USB デバッグ]** を有効にしてから、Android フォンを USB ケーブルで開発用 PC に接続します。

このチュートリアルのテストでは、Android 6.0 (Marshmallow) を実行している Google Nexus 5X を使用しました。  ただし、その手法は最新のどの Android リリースでも共通です。

#### <a name="install-google-play-services"></a>Google Play Services をインストールする
プッシュ プラグインでは、プッシュ通知に Android Google Play Services を利用します。

1. Visual Studio で **[ツール]** > **[Android]** > **[Android SDK マネージャー]** をクリックします。**Extras** フォルダーを展開し、次の SDK のチェック ボックスをオンにして、それらが確実にインストールされるようにします。

   * Android 2.3 以上
   * Google Repository リビジョン 27 以上
   * Google Play Services 9.0.2 以上

2. **[Install Packages (パッケージのインストール)]** をクリックして、インストールが完了するのを待ちます。

最新の必要なライブラリは、[phonegap-plugin-push のインストール ドキュメント][19]に記載されています。

#### <a name="test-push-notifications-in-the-app-on-android"></a>Android 上のアプリでプッシュ通知をテストする
ここで、アプリを実行して TodoItem テーブルにアイテムを挿入することにより、プッシュ通知をテストできます。 同じバックエンドを使用している限り、同じデバイスまたは 2 番目のデバイスからテストを実行できます。 次のいずれかの方法により、Android プラットフォームで Cordova アプリをテストします。

* **物理デバイス上:** Android デバイスを USB ケーブルで開発用コンピューターに接続します。  **Google Android エミュレーター**の代わりに、**[デバイス]** を選択します。 Visual Studio によってデバイスにアプリケーションがデプロイされ、アプリケーションが実行されます。  デバイスでアプリケーションを操作できるようになります。

  開発環境を強化しましょう。  [Mobizen][20] などの画面共有アプリケーションは、Android アプリケーションの開発に役立ちます。  Mobizen では、Android の画面を PC の Web ブラウザーで表示できます。

* **Android エミュレーター上:** エミュレーターで実行するときに必要な追加の構成手順があります。

    Android Virtual Device (AVD) Manager で示すように、Google API がターゲットとして設定された仮想デバイスに対してデプロイする必要があります。

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    より高速な x86 エミュレーターを使用する場合は、[HAXM ドライバーをインストール][11]した後、これを使用するようにエミュレーターを構成します。

    **[アプリ]** > **[設定]** > **[アカウントの追加]** をクリックして Android デバイスに Google アカウントを追加した後、画面の指示に従います。

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    これまでと同様に ToDoList アプリを実行し、新しい ToDo 項目を挿入します。 今回は、通知領域に通知アイコンが表示されます。 通知ドロアーを開いて、通知の全文を表示できます。

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(省略可能) iOS で構成して実行する
このセクションでは、iOS デバイス上で Cordova プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションをスキップできます。

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>iOS リモート ビルド エージェントを Mac またはクラウド サービスにインストールして実行する
Visual Studio を使用して iOS で Cordova アプリを実行する前に、[iOS セットアップ ガイド][12] の手順に従ってリモート ビルド エージェントのインストールと実行を行います。

iOS 用アプリのビルドが可能であることを確認します。 Visual Studio で iOS 用にビルドするには、セットアップ ガイドの手順の実施が必要です。 Mac を所持していない場合は、MacInCloud などのサービスでリモート ビルド エージェントを使用すれば、iOS 用にビルドが可能です。 詳細については、[クラウドでの iOS アプリの実行][21]に関するページを参照してください。

> [!NOTE]
> iOS でプッシュ プラグインを使用するには XCode 7 以上が必要です。

#### <a name="find-the-id-to-use-as-your-app-id"></a>アプリ ID として使用する ID を探す
アプリをプッシュ通知に登録する前に、Cordova アプリで config.xml を開き、ウィジェット要素内の `id` 属性の値を特定して、後で使用できるようにコピーしておきます。 次の XML では、ID は `io.cordova.myapp7777777`です。

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

後で Apple の開発者ポータルでアプリ ID を作成するときに、この識別子を使用します  開発者ポータルで別のアプリ ID を作成した場合は、このチュートリアルの後半でいくつかの追加の手順が必要になります。 ウィジェット要素の ID は、開発者ポータルのアプリ ID と一致している必要があります。

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Apple の開発者ポータルにプッシュ通知のアプリを登録する
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[同様の手順を説明するビデオを見る](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>プッシュ通知を送信するように Azure を構成する
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>アプリ ID がCordova アプリと一致することを確認する
Apple の開発者アカウントで作成したアプリ ID が既に config.xml のウィジェット要素の ID と一致している場合、この手順は省略可能です。 ただし、ID が一致しない場合は、以下の手順を実行します。

1. platforms フォルダーをプロジェクトから削除します。
2. plugins フォルダーをプロジェクトから削除します。
3. node_modules フォルダーをプロジェクトから削除します。
4. config.xml のウィジェット要素の ID 属性を更新して、Apple の開発者アカウントで作成したアプリ ID を使用できるようにします。
5. プロジェクトをリビルドします。

##### <a name="test-push-notifications-in-your-ios-app"></a>iOS アプリでプッシュ通知をテストする
1. Visual Studio で、**iOS** がデプロイ ターゲットとして選択されていることを確認してから、**[デバイス]**
    を選択して、接続した iOS デバイス上で実行します。

    iTunes を使用中の PC に接続した iOS デバイス上で実行することができます。 iOS シミュレーターでは、プッシュ通知はサポートされていません。

2. Visual Studio で **[実行]** または **F5** キーを押してプロジェクトをビルドし、iOS デバイスでアプリを起動します。次に、**[OK]** をクリックして、プッシュ通知を受け入れます。

   > [!NOTE]
   > アプリは最初の実行中にプッシュ通知の確認を要求します。

3. アプリケーションで、タスクを入力し、プラス (+) アイコンをクリックします。
4. 通知が受信されたことを確認し、[OK] をクリックして通知を破棄します。

## <a name="optional-configure-and-run-on-windows"></a>(省略可能) Windows で構成して実行する
このセクションでは、Windows 10 デバイス上で Apache Cordova アプリ プロジェクトを実行します (Windows 10 では PhoneGap プッシュ プラグインがサポートされています)。 Windows デバイスを使用していない場合は、このセクションをスキップできます。

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>WNS を使用して Windows アプリをプッシュ通知に登録する
Visual Studio でストアのオプションを使用するには、ソリューション プラットフォームの一覧から **Windows x64** や **Windows x86** などの Windows ターゲットを選択します (プッシュ通知を行う場合、**Windows-AnyCPU** は選択しないでください)。

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[同様の手順を説明するビデオを見る][13]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS 用に通知ハブを構成する
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Cordova アプリを構成して Windows プッシュ通知をサポートする
構成デザイナーを開き (config.xml を右クリックして **[ビュー デザイナー]** を選択)、**[Windows]** タブを選択し、**[Windows 対象バージョン]** で **[Windows 10]** を選択します。

既定の (デバッグ) ビルドでプッシュ通知をサポートするには、build.json ファイルを開きます。 "release" 構成をデバッグ構成にコピーします。

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

更新の後は、build.json に次のコードが含まれている必要があります。

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

アプリをビルドし、エラーがないことを確認します。 これで、クライアント アプリが、モバイル アプリ バックエンドから送信される通知に登録されました。 ソリューションのすべての Windows プロジェクトについて、このセクションを繰り返します。

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows アプリでプッシュ通知をテストする
Visual Studio で、**Windows-x64** や **Windows-x86** などの Windows プラットフォームがデプロイ ターゲットとして選択されていることを確認します。 Visual Studio をホストしている Windows 10 PC でアプリを実行するには、 **[ローカル コンピューター]**を選択します。

[実行] ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。

アプリで新しい todoitem の名前を入力し、プラス (+) アイコンをクリックして追加します。

項目が追加されたときに、通知が受信されていることを確認します。

## <a name="next-steps"></a>次のステップ
* プッシュ通知の詳細については、[Notification Hubs][17] に関する記事を参照してください。
* まだ認証を追加していない場合は、チュートリアルの続きとして、Apache Cordova アプリに[認証を追加][14]してください。

SDK の使用方法を確認してください。

* [Apache Cordova SDK][15]
* [ASP.NET サーバー SDK][1]
* [Node.js サーバー SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/

