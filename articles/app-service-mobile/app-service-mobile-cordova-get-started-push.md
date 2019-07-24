---
title: Azure App Service の Mobile Apps 機能を使用した Apache Cordova アプリへのプッシュ通知の追加 | Microsoft Docs
description: Mobile Apps を使用して Apache Cordova アプリにプッシュ通知を送信する方法について説明します。
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 40a7552ffd0bfcab173d2e35c52313a94ec3d0bb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960330"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Apache Cordova アプリへのプッシュ通知の追加

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要

このチュートリアルでは、[[Apache Cordova クイック スタート]][5] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにデバイスにプッシュ通知が送信されるようにします。

ダウンロードされたクイック スタート サーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージが必要です。 詳細については、[Mobile Apps 用の .NET バックエンド サーバー SDK の操作][1]に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Visual Studio 2015 で開発された Apache Cordova アプリケーションがあることを前提にしています。 このデバイスは、Google Android Emulator、Android デバイス、Windows デバイス、または iOS デバイスで実行する必要があります。

このチュートリアルを完了するには、次のものが必要です。

* [Visual Studio Community 2015][2] 以降がインストールされた PC
* [Visual Studio Tools for Apache Cordova][4]
* [アクティブな Azure アカウント][3]
* 完了した [[Apache Cordova クイック スタート]][5] プロジェクト
* (Android) 確認済みのメール アドレスがある [Google アカウント][6]
* (iOS) [Apple Developer Program メンバーシップ][7]と iOS デバイス (iOS シミュレーターはプッシュ通知をサポートしていません)
* (Windows) A [Microsoft Store Developer Account][8]　およびと Windows 10 デバイス

## <a name="configure-hub"></a>通知ハブを構成する

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[このセクションの手順を示すビデオを見る][9]。

## <a name="update-the-server-project"></a>サーバー プロジェクトを更新する

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Cordova アプリを変更する

Apache Cordova アプリ プロジェクトが確実にプッシュ通知を処理する準備ができるように、Cordova プッシュ プラグインに加え、プラットフォーム固有のいずれかのプッシュ サービスをインストールします。

#### <a name="update-the-cordova-version-in-your-project"></a>プロジェクトで Cordova のバージョンを更新する

プロジェクトがバージョン 6.1.1 より前の Apache Cordova を使用している場合は、クライアント プロジェクトを更新します。 プロジェクトを更新するには、次の手順を実行します。

* 構成デザイナーを開くには、`config.xml` を右クリックします。
* **[プラットフォーム]** タブを選択します。
* **[Cordova CLI]** テキスト ボックスで、**[6.1.1]** を選択します。 
* プロジェクトを更新するには、**[構築]**、**[Build Solution] (ソリューションの構築)** の順に選択します。

#### <a name="install-the-push-plugin"></a>プッシュ プラグインをインストールする

Apache Cordova アプリケーションでは、デバイスやネットワークの機能をネイティブで処理しません。  これらの機能は、[npm][10] または GitHub で公開されているプラグインによって提供されます。 `phonegap-plugin-push` プラグインは、ネットワーク プッシュ通知を処理します。

プッシュ プラグインは、次のいずれかの方法でインストールできます。

**コマンド プロンプトから:**

次のコマンドを実行します。

    cordova plugin add phonegap-plugin-push

**Visual Studio 内から:**

1. ソリューション エクスプローラーで、`config.xml` ファイルを開きます。 次に、**[プラグイン]** > **[カスタム]** を選択します。 その後、インストール ソースとして **[Git]** を選択します。

2. ソースとして `https://github.com/phonegap/phonegap-plugin-push` を入力します。

    ![ソリューション エクスプローラーで config.xml ファイルを開きます。][img1]

3. インストール ソースの横にある矢印を選択します。

4. Google デベロッパー コンソール プロジェクト用のプロジェクト ID (数値) を既に所有している場合は、**SENDER_ID** のボックスに追加できます。 それ以外の場合は、777777 などのプレースホルダー値を入力します。 Android を対象にしている場合は、後で config.xml ファイル内のこの値を更新できます。

    >[!NOTE]
    >バージョン 2.0.0 の時点では、送信者 ID を構成するには、プロジェクトのルート フォルダーに google-services.json をインストールする必要があります。 詳細については、[インストール ドキュメント](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)を参照してください。

5. **[追加]** を選択します。

これでプッシュ プラグインがインストールされました。

#### <a name="install-the-device-plugin"></a>デバイス プラグインをインストールする

プッシュ プラグインのインストールに使用したものと同じ手順に従います。 コア プラグインの一覧からデバイス プラグインを追加します。 (これを見つけるには、**[プラグイン]** > **[コア]** を選択します。)このプラグインはプラットフォーム名の取得に必要です。

#### <a name="register-your-device-when-the-application-starts"></a>アプリケーションの起動時にデバイスを登録する 

最初に、最小限の Android コードを追加します。 後で、iOS または Windows 10 で実行されるようにアプリを変更できます。

1. サインイン プロセスのコールバック中に **registerForPushNotifications** への呼び出しを追加します。 あるいは、これを **onDeviceReady** メソッドの一番下に追加できます。

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    この例は、認証が成功した後の **registerForPushNotifications** の呼び出しを示しています。 `registerForPushNotifications()` は必要とされる頻度で呼び出すことができます。

2. 新しい **registerForPushNotifications** メソッドを次のように追加します。

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
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
    ```
3. (Android) 前述のコードで、`Your_Project_ID` を、[Google デベロッパー コンソール][18]から入手したアプリのプロジェクト ID (数値) に置き換えます。

## <a name="optional-configure-and-run-the-app-on-android"></a>(省略可能) Android でアプリを構成して実行する

このセクションを完了すると、Android 用のプッシュ通知を有効にすることができます。

#### <a name="enable-gcm"></a>Firebase Cloud Messaging を有効にする

最初は Google Android プラットフォームを対象にしているため、Firebase Cloud Messaging を有効にする必要があります。

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>FCM を使用してプッシュ要求を送信するようにモバイル アプリ バックエンドを構成する

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Android 用に Cordova アプリを構成する

Cordova アプリで、**config.xml** を開きます。 次に、`Your_Project_ID` を [Google デベロッパー コンソール ][18]にあるアプリの数値プロジェクト ID に置き換えます。

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

**index.js** を開きます。 次に、数値プロジェクト ID を使用するようにこのコードを更新します。

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>USB デバッグ用に Android デバイスを構成する

Android デバイスにアプリケーションをデプロイするには、USB デバッグを有効にしておく必要があります。 Android フォンで次の手順を実行します。

1. **[設定]** > **[端末情報]** に移動します。 その後、開発者モードが有効になるまで (約 7 回) **[ビルド番号]** をタップします。
2. **[設定]** > **[Developer Options] (開発者オプション)** に戻り、**[USB debugging] (USB デバッグ)** を有効にします。 次に、USB ケーブルを使用して Android フォンを開発 PC に接続します。

このチュートリアルのテストでは、Android 6.0 (Marshmallow) を実行している Google Nexus 5X を使用しました。 ただし、その手法は最新のどの Android リリースでも共通です。

#### <a name="install-google-play-services"></a>Google Play Services をインストールする

プッシュ プラグインでは、プッシュ通知に Android Google Play Services を利用します。

1. Visual Studio で、**[ツール]** > **[Android]** > **[Android SDK Manager]** を選択します。 次に、**Extras** フォルダーを展開します。 次の各 SDK が確実にインストールされるように、適切なボックスにチェックを入れます。

   * Android 2.3 以上
   * Google Repository リビジョン 27 以上
   * Google Play Services 9.0.2 以上

2. **[Install Packages] (パッケージのインストール)** を選択します。 その後、インストールの完了を待ちます。

最新の必要なライブラリは、[phonegap-plugin-push のインストール ドキュメント][19]に記載されています。

#### <a name="test-push-notifications-in-the-app-on-android"></a>Android 上のアプリでプッシュ通知をテストする

ここで、アプリを実行して TodoItem テーブルにアイテムを挿入することにより、プッシュ通知をテストできます。 同じバックエンドを使用している限り、同じデバイスまたは 2 番目のデバイスからテストできます。 次のいずれかの方法により、Android プラットフォームで Cordova アプリをテストします。

* *物理デバイス上:* Android デバイスを USB ケーブルで開発用コンピューターに接続します。  **Google Android エミュレーター**の代わりに、**[デバイス]** を選択します。 Visual Studio がデバイスにアプリケーションをデプロイし、そのアプリケーションを実行します。 デバイスでアプリケーションを操作できるようになります。

  [Mobizen][20] などの画面共有アプリケーションは、Android アプリケーションの開発に役立ちます。 Mobizen では、Android の画面を PC の Web ブラウザーで表示できます。

* *Android エミュレーター上:* エミュレーターを使用しているときに必要な追加の構成手順があります。

    Android Virtual Device (AVD) Manager で示すように、Google API がターゲットとして設定された仮想デバイスに対してデプロイする必要があります。

    ![Android Virtual Device Manager](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    より高速な x86 エミュレーターを使用する場合は、[HAXM ドライバーをインストールし][11]、それを使用するようにエミュレーターを構成します。

    **[アプリ]** > **[設定]** > **[アカウントの追加]** を選択することによって、Android デバイスに Google アカウントを追加します。 その後、プロンプトに従います。

    ![Android デバイスに Google アカウントを追加する](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    これまでと同様に ToDoList アプリを実行し、新しい ToDo 項目を挿入します。 今回は、通知領域に通知アイコンが表示されます。 通知ドロアーを開いて、通知の全文を表示できます。

    ![通知の表示](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(省略可能) iOS で構成して実行する

このセクションでは、iOS デバイス上で Cordova プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>iOS リモート ビルド エージェントを Mac またはクラウド サービスにインストールして実行する

Visual Studio を使用して iOS で Cordova アプリを実行するには、その前に [iOS セットアップ ガイド][12]の手順を実行してリモート ビルド エージェントをインストールおよび実行します。

iOS 用アプリのビルドが可能であることを確認します。 Visual Studio から iOS 用のアプリをビルドするには、セットアップ ガイドの手順が必要です。 Mac がない場合は、MacInCloud などのサービスでリモート ビルド エージェントを使用して iOS 用にビルドできます。 詳細については、[クラウドでの iOS アプリの実行][21]に関するページを参照してください。

> [!NOTE]
> iOS でプッシュ プラグインを使用するには Xcode 7 以上が必要です。

#### <a name="find-the-id-to-use-as-your-app-id"></a>アプリ ID として使用する ID を探す

アプリをプッシュ通知用に登録する前に、Cordova アプリで config.xml を開き、ウィジェット要素内の `id` 属性値を見つけ、それを後で使用するためにコピーします。 次の XML では、ID は `io.cordova.myapp7777777`です。

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

後で Apple の開発者ポータルでアプリ ID を作成するときに、この識別子を使用します  開発者ポータルで別のアプリ ID を作成した場合は、このチュートリアルの後半でいくつかの追加の手順が必要になります。 ウィジェット要素の ID は、開発者ポータルのアプリ ID と一致している必要があります。

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Apple の開発者ポータルにプッシュ通知のアプリを登録する

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[同様の手順を説明するビデオを見る](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>プッシュ通知を送信するように Azure を構成する

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>アプリ ID がCordova アプリと一致することを確認する

Apple 開発者アカウントで作成したアプリ ID が config.xml ファイル内のウィジェット要素の ID と既に一致している場合は、この手順を省略できます。 ただし、ID が一致しない場合は、以下の手順を実行します。

1. platforms フォルダーをプロジェクトから削除します。
2. plugins フォルダーをプロジェクトから削除します。
3. node_modules フォルダーをプロジェクトから削除します。
4. Apple 開発者アカウントで作成したアプリ ID を使用するには、config.xml ファイル内のウィジェット要素の ID 属性を更新します。
5. プロジェクトをリビルドします。

##### <a name="test-push-notifications-in-your-ios-app"></a>iOS アプリでプッシュ通知をテストする

1. Visual Studio で、デプロイ ターゲットとして **[iOS]** が選択されていることを確認します。 次に、**[デバイス]** を選択して、接続された iOS デバイスでプッシュ通知を実行します。

    iTunes で PC に接続された iOS デバイスでプッシュ通知を実行できます。 iOS シミュレーターでは、プッシュ通知はサポートされていません。

2. Visual Studio で **[実行]** ボタンまたは **F5** を選択して、プロジェクトをビルドし、iOS デバイスでアプリを起動します。 次に、**[OK]** を選択してプッシュ通知を受け入れます。

   > [!NOTE]
   > アプリは最初の実行中にプッシュ通知の確認を要求します。

3. アプリで、タスクを入力し、プラス **(+)** アイコンを選択します。
4. 通知が受信されたことを確認します。 次に、**[OK]** を選択して通知を無視します。

## <a name="optional-configure-and-run-on-windows"></a>(省略可能) Windows で構成して実行する

このセクションでは、Windows 10 デバイス上で Apache Cordova アプリ プロジェクトを実行する方法について説明します (Windows 10 では PhoneGap プッシュ プラグインがサポートされています)。 Windows デバイスを使用していない場合は、このセクションをスキップできます。

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>WNS を使用して Windows アプリをプッシュ通知に登録する

Visual Studio でストアのオプションを使用するには、ソリューション プラットフォームの一覧から Windows ターゲット (**[Windows-x64]** や **[Windows-x86]** など) を選択します。 (プッシュ通知に **[Windows-AnyCPU]** は避けてください。)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[同様の手順を説明するビデオを見る][13]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS 用に通知ハブを構成する

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Cordova アプリを構成して Windows プッシュ通知をサポートする

**config.xml** を右クリックして、構成デザイナーを開きます。 その後、**[ビュー デザイナー]** を選択します。 次に、**[Windows]** タブを選択してから、**[Windows Target Version] (Windows ターゲット バージョン)** の下の **[Windows 10]** を選択します。

既定の (デバッグ) ビルドでプッシュ通知をサポートするには、**build.json** ファイルを開きます。 次に、"release" 構成をデバッグ構成にコピーします。

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

更新の後、**build.json** ファイルには次のコードが含まれている必要があります。

```json
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
```

アプリをビルドし、エラーがないことを確認します。 これで、クライアント アプリが Mobile Apps バックエンドからの通知用に登録されました。 ソリューションのすべての Windows プロジェクトについて、このセクションを繰り返します。

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows アプリでプッシュ通知をテストする

Visual Studio で、**Windows-x64** や **Windows-x86** などの Windows プラットフォームがデプロイ ターゲットとして選択されていることを確認します。 Visual Studio をホストしている Windows 10 PC 上でアプリを実行するには、**[ローカル コンピューター]** を選択します。

1. **[実行]** ボタンを選択して、プロジェクトをビルドし、アプリを起動します。

2. アプリで、新しい todoitem の名前を入力し、プラス **(+)** アイコンを選択してそれを追加します。

項目が追加されたときに、通知が受信されていることを確認します。

## <a name="next-steps"></a>次のステップ

* プッシュ通知の詳細については、[Notification Hubs][17] に関する記事を参照してください。
* まだ実行していない場合は、チュートリアルを続行して Apache Cordova アプリに[認証を追加][14]してください。

次の SDK を使用する方法を学習してください。

* [Apache Cordova SDK][15]
* [ASP.NET サーバー SDK][1]
* [Node.js サーバー SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
