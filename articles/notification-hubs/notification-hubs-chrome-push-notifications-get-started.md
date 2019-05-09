---
title: Azure Notification Hubs を使用して Chrome アプリにプッシュ通知を送信する | Microsoft Docs
description: Azure Notification Hubs を使用して Chrome アプリケーションにプッシュ通知を送信する方法について説明します。
services: notification-hubs
keywords: モバイル プッシュ通知,プッシュ通知,プッシュ通知,Chrome プッシュ通知
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 03374f63345bd6c9e4f2b603443a1448493e1cdc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894616"
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>チュートリアル:Azure Notification Hubs を使用して Chrome アプリにプッシュ通知を送信する

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

このチュートリアルでは、通知ハブの作成と、[Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/) を使用したサンプルの Google Chrome アプリへのプッシュ通知の送信について説明します。 Chrome アプリは Google Chrome ブラウザーのコンテキストで実行され、通知ハブに登録されます。

> [!NOTE]
> Chrome アプリケーションのプッシュ通知は汎用的なブラウザー内通知ではなく、Chrome ブラウザーの拡張モデルに固有の機能となっています (「 [Chrome Apps Overview (Chrome アプリケーションの概要)] 」を参照)。 Chrome アプリケーションは、デスクトップ ブラウザーだけでなく、Apache Cordova を通じてモバイル (Android および iOS) でも動作します。 詳細については、[モバイルでの Chrome アプリケーションの実行]に関するページを参照してください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * [Google Cloud Messaging を有効にする](#register)
> * [通知ハブを構成する](#configure-hub)
> * [通知ハブに Chrome アプリケーションを接続する](#connect-app)
> * [Chrome アプリケーションにプッシュ通知を送信する](#send)

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a id="register"></a>Google Cloud Messaging を有効にする

1. [Google Cloud Console](https://console.cloud.google.com/cloud-resource-manager) の Web サイトに移動し、Google アカウントの資格情報でサインインします。
2. ツールバーの **[プロジェクトの作成]** を選択します。

    ![[プロジェクトの作成] ボタン](media/notification-hubs-chrome-get-started/create-project-button.png)
3. **[Project Name (プロジェクト名)]** に適切なプロジェクト名を指定して、**[Create (作成)]** ボタンをクリックします。
4. ツールバーの通知アイコン (ベル) を選択してから、**[プロジェクトの作成]** メッセージを選択します。

    ![[プロジェクトの作成] 通知](media/notification-hubs-chrome-get-started/project-creation-notification.png)
5. 作成したプロジェクトの **[Projects]\(プロジェクト\)** ページにある **[Project Number]\(プロジェクト番号\)** をメモしておきます。 Chrome アプリケーションでこのプロジェクト番号を **GCM 送信者 ID** として使用して GCM に登録します。

    ![Google Cloud Console - Project Number](media/notification-hubs-chrome-get-started/gcm-project-number.png)
6. ダッシュボードで、**[Go to APIs overview] (API の概要に移動)** を選択します。

    ![[Go to API overviews] (API の概要に移動) ボタン](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
7. [API & Services] (API とサービス) ページで、**[Enable APIs and Services] (API とサービスの有効化)** を選択します。

    ![API とサービスの有効化](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
8. 一覧内で **Cloud Messaging** のキーワードを検索します。 フィルター処理された一覧で **[Google Cloud Messaging]** を選択します。

    ![Google Cloud Messaging API](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
9. **[Google Cloud Messaging]** ページで、**[有効化]** を選択します。

    ![GCM の有効化](media/notification-hubs-chrome-get-started/enable-gcm.png)
10. **[API & Services] (API とサービス)** ページで、**[資格情報]** タブに切り替えます。**[Create credentials]\(資格情報の作成)** を選択してから、**[API キー]** を選択します。

    ![[Create API key] (API キーの作成) ボタン](media/notification-hubs-chrome-get-started/create-api-key-button.png)
11. **[API key created] (作成された API キー)** ダイアログ ボックスで、コピー ボタンを選択して、そのキーをクリップボードにコピーします。 それを別の場所に保存します。 この値は、次のセクションで通知ハブを構成して、GCM にプッシュ通知を送信できるようにするために使用します。

    ![API ページ](media/notification-hubs-chrome-get-started/api-created-page.png)
12. **[API キー]** 一覧で API キーを選択します。 [API キー] ページで、**[IP addresses (web servers, cron jobs, etc.)] (IP アドレス (Web サーバー、cron ジョブなど))** を選択し、IP アドレスに **0.0.0.0/0** を入力して [保存] をクリックします。

    ![IP アドレスの有効化](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>通知ハブを作成する

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. **[通知設定]** カテゴリで **[Google (GCM)]** を選択し、GCM プロジェクトの **[API キー]** を入力して **[保存]** をクリックします。

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs - Google (GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>通知ハブに Chrome アプリケーションを接続する

これで、通知ハブが GCM と連動するように構成されました。接続文字列を使用して、プッシュ通知の受信と送信の両方にアプリを登録できます。

### <a name="create-a-new-chrome-app"></a>新しい Chrome アプリケーションの作成

以下のサンプルは、[Chrome アプリケーションの GCM サンプル] に基づいており、Chrome アプリケーションの推奨の作成方法を使用します。 このセクションでは、Azure Notification Hubs に固有の手順を重点的に説明しています。

> [!NOTE]
> この Chrome アプリケーションのソースを [Chrome アプリケーションの Notification Hub サンプル]のページからダウンロードすることをお勧めします。

Chrome アプリケーションは JavaScript を使用して作成されます。推奨される任意のワード エディターを使用して作成を行うことができます。 次の図は、Chrome アプリの外観を示しています。

![Google Chrome App][15]

1. フォルダーを作成して `ChromePushApp` という名前を付けます。 名前は任意です。ただし他の名前を使用した場合は、必要なコード セグメント内のパスもそれに合わせて変更してください。
2. 2 つ目の手順で作成したフォルダーに [crypto-js ライブラリ] をダウンロードします。 このライブラリ フォルダーには 2 つのサブフォルダー (`components` と `rollups`) が含まれています。
3. `manifest.json` ファイルを作成します。 すべての Chrome アプリケーションは、マニフェスト ファイルを持ちます。マニフェスト ファイルは、アプリケーションのメタデータのほか、特に重要な情報として、ユーザーがアプリケーションをインストールする際にそのアプリケーションに与えられるすべてのアクセス許可を含んでいます。

    ```json
    {
        "name": "NH-GCM Notifications",
        "description": "Chrome platform app.",
        "manifest_version": 2,
        "version": "0.1",
        "app": {
        "background": {
            "scripts": ["background.js"]
        }
        },
        "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
        "icons": { "128": "gcm_128.png" }
    }
    ```

    `permissions` 要素に注目してください。この Chrome アプリケーションが GCM からプッシュ通知を受信できる、ということを指定しています。 このサンプル アプリでは、元の GCM サンプルから再利用されるソースにあるアイコン ファイル (`gcm_128.png`) を使用します。 [アイコンの基準](https://developer.chrome.com/apps/manifest/icons)さえ満たせば、どのような画像でも代わりに使用することができます。
4. 次のコードを含む `background.js` という名前のファイルを作成します。

    ```javascript
    // Returns a new notification ID used in the notification.
    function getNotificationId() {
        var id = Math.floor(Math.random() * 9007199254740992) + 1;
        return id.toString();
    }

    function messageReceived(message) {
        // A message is an object with a data property that
        // consists of key-value pairs.

        // Concatenate all key-value pairs to form a display string.
        var messageString = "";
        for (var key in message.data) {
        if (messageString != "")
            messageString += ", "
        messageString += key + ":" + message.data[key];
        }
        console.log("Message received: " + messageString);

        // Pop up a notification to show the GCM message.
        chrome.notifications.create(getNotificationId(), {
        title: 'GCM Message',
        iconUrl: 'gcm_128.png',
        type: 'basic',
        message: messageString
        }, function() {});
    }

    var registerWindowCreated = false;

    function firstTimeRegistration() {
        chrome.storage.local.get("registered", function(result) {

        registerWindowCreated = true;
        chrome.app.window.create(
            "register.html",
            {  width: 520,
                height: 500,
                frame: 'chrome'
            },
            function(appWin) {}
        );
        });
    }

    // Set up a listener for GCM message event.
    chrome.gcm.onMessage.addListener(messageReceived);

    // Set up listeners to trigger the first-time registration.
    chrome.runtime.onInstalled.addListener(firstTimeRegistration);
    chrome.runtime.onStartup.addListener(firstTimeRegistration);
    ```

    これは、Chrome アプリ ウィンドウの HTML (`register.html`) をポップアップ表示するファイルであり、受信したプッシュ通知を処理するためのハンドラー `messageReceived` も定義しています。
5. `register.html` というファイルを作成します。Chrome アプリケーションの UI が、このファイルによって定義されます。

   > [!NOTE]
   > このサンプルでは **CryptoJS v3.1.2**を使用します。 別のバージョンのライブラリをダウンロードした場合は、 `src` パスのバージョンを忘れずに変更してください。

    ```html
    <html>
        <head>
            <title>GCM Registration</title>
            <script src="register.js"></script>
            <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
            <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
        <body>
            Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
            <button id="registerWithGCM">Register with GCM</button>
            <br/>
            <br/>
            <br/>

            Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
            Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

            <br/>
            <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
            <br/>
            <br/>

            <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
        </body>
    </html>
    ```
6. この手順のコードを含む `register.js` というファイルを作成します。 このファイルは、 `register.html`の基盤となるスクリプトを指定します。 Chrome アプリケーションではインライン実行が許可されないため、UI 用の基盤となるスクリプトを別途作成する必要があります。

    ```javascript
    var registrationId = "";
    var hubName        = "", connectionString = "";
    var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

    window.onload = function() {
        document.getElementById("registerWithGCM").onclick = registerWithGCM;  
        document.getElementById("registerWithNH").onclick = registerWithNH;
        updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
    }

    function updateLog(status) {
        currentStatus = document.getElementById("console").innerHTML;
        if (currentStatus != "") {
        currentStatus = currentStatus + "\n\n";
        }

        document.getElementById("console").innerHTML = currentStatus  + status;
    }

    function registerWithGCM() {
        var senderId = document.getElementById("senderId").value.trim();
        chrome.gcm.register([senderId], registerCallback);

        // Prevent register button from being clicked again before the registration finishes.
        document.getElementById("registerWithGCM").disabled = true;
    }

    function registerCallback(regId) {
        registrationId = regId;
        document.getElementById("registerWithGCM").disabled = false;

        if (chrome.runtime.lastError) {
        // When the registration fails, handle the error and retry the
        // registration later.
        updateLog("Registration failed: " + chrome.runtime.lastError.message);
        return;
        }

        updateLog("Registration with GCM succeeded.");
        document.getElementById("registerWithNH").disabled = false;

        // Mark that the first-time registration is done.
        chrome.storage.local.set({registered: true});
    }

    function registerWithNH() {
        hubName = document.getElementById("hubName").value.trim();
        connectionString = document.getElementById("connectionString").value.trim();
        splitConnectionString();
        generateSaSToken();
        sendNHRegistrationRequest();
    }

    // From https://msdn.microsoft.com/library/dn495627.aspx
    function splitConnectionString()
    {
        var parts = connectionString.split(';');
        if (parts.length != 3)
        throw "Error parsing connection string";

        parts.forEach(function(part) {
        if (part.indexOf('Endpoint') == 0) {
        endpoint = 'https' + part.substring(11);
        } else if (part.indexOf('SharedAccessKeyName') == 0) {
        sasKeyName = part.substring(20);
        } else if (part.indexOf('SharedAccessKey') == 0) {
        sasKeyValue = part.substring(16);
        }
        });

        originalUri = endpoint + hubName;
    }

    function generateSaSToken()
    {
        targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
        var expiresInMins = 10; // 10 minute expiration

        // Set expiration in seconds.
        var expireOnDate = new Date();
        expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
        var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
        .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
        .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
        .getUTCSeconds()) / 1000;
        var tosign = targetUri + '\n' + expires;

        // Using CryptoJS.
        var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
        var base64signature = signature.toString(CryptoJS.enc.Base64);
        var base64UriEncoded = encodeURIComponent(base64signature);

        // Construct authorization string.
        sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
    }

    function sendNHRegistrationRequest()
    {
        var registrationPayload =
        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
        "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
            "<content type=\"application/xml\">" +
                "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                    "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                "</GcmRegistrationDescription>" +
            "</content>" +
        "</entry>";

        // Update the payload with the registration ID obtained earlier.
        registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

        var url = originalUri + "/registrations/?api-version=2014-09";
        var client = new XMLHttpRequest();

        client.onload = function () {
        if (client.readyState == 4) {
            if (client.status == 200) {
            updateLog("Notification Hub Registration successful!");
            updateLog(client.responseText);
            } else {
            updateLog("Notification Hub Registration did not succeed!");
            updateLog("HTTP Status: " + client.status + " : " + client.statusText);
            updateLog("HTTP Response: " + "\n" + client.responseText);
            }
        }
        };

        client.onerror = function () {
            updateLog("ERROR - Notification Hub Registration did not succeed!");
        }

        client.open("POST", url, true);
        client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
        client.setRequestHeader("Authorization", sasToken);
        client.setRequestHeader("x-ms-version", "2014-09");

        try {
            client.send(registrationPayload);
        }
        catch(err) {
            updateLog(err.message);
        }
    }
    ```

    このスクリプトの主要なパラメーターは次のとおりです。

   * `window.onload` は、UI の 2 つのボタンのボタン クリック イベントを定義します。 1 つ目のボタン クリック イベント ハンドラーは GCM への登録を行い、もう 1 つは、GCM への登録後に返される登録 ID を使用して Azure Notification Hubs への登録を行います。
   * `updateLog` は、コードで情報を記録するための関数です。
   * `registerWithGCM` は、現在の Chrome アプリ インスタンスを登録するために、GCM に対して `chrome.gcm.register` を呼び出す最初のボタン クリック ハンドラーです。
   * `registerCallback` は、GCM 登録の呼び出しから戻った時点で呼び出されるコールバック関数です。
   * `registerWithNH` は 2 番目のボタン クリック ハンドラーで、Notification Hubs への登録を行います。 また、ユーザーが指定した `hubName` と `connectionString` を取得して、Notification Hubs 登録の REST API 呼び出しを作成します。
   * `splitConnectionString` と `generateSaSToken` は、すべての REST API 呼び出しで使用する必要のある SaS トークン作成プロセスの JavaScript の実装です。 詳細については、「 [一般的な概念](https://msdn.microsoft.com/library/dn495627.aspx)」を参照してください。
   * `sendNHRegistrationRequest` は、Azure Notification Hubs に対して HTTP REST 呼び出しを実行する関数です。
   * `registrationPayload` は、登録の XML ペイロードを定義します。 詳細については、「 [登録の作成]」をご覧ください。 そこでは、GCM から受信された値を使用して登録 ID を更新します。
   * `client` は、HTTP POST 要求に使用する `XMLHttpRequest` のインスタンスです。 `sasToken` を使用して `Authorization` ヘッダーを更新します。 この呼び出しが正常に完了すると、Chrome アプリケーション インスタンスが Azure Notification Hubs に登録されます。

     このプロジェクトの全体的なフォルダー構造は、![Google Chrome アプリ - フォルダー構造][21]のような構造になります。

### <a name="set-up-and-test-your-chrome-app"></a>Chrome アプリケーションのセットアップとテスト

1. Chrome ブラウザーを開きます。 Chrome の **[拡張機能]** を開いて **[デベロッパー モード]** を有効にします。

    ![Google Chrome - Enable Developer Mode][16]
2. **[Load unpacked extension]** をクリックして、ファイルを作成したフォルダーに移動します。 また必要に応じて、**Chrome Apps & Extensions Developer Tool** を使用することもできます。 このツールはそれ自体が Chrome アプリケーションであり (Chrome Web Store からインストールされます)、Chrome アプリケーション開発用の高度なデバッグ機能を提供します。

    ![Google Chrome - Load Unpacked Extension][17]
3. Chrome アプリケーションの作成時にエラーが発生しなかった場合は、作成した Chrome アプリケーションが表示されます。

    ![Google Chrome - Chrome App Display][18]
4. 前の手順で **Google Cloud Console** から取得した**プロジェクト番号**を送信者 ID として入力し、**[Register with GCM]** をクリックします。 **GCM 登録が成功しました**

    ![Google Chrome - Chrome App Customization][19]
5. **[Notification Hub Name]** に通知ハブの名前を入力し、前の手順でポータルから取得した **DefaultListenSharedAccessSignature** を入力して、**[Register with Azure Notification Hub]** をクリックします。 **Notification Hub の登録に成功しました** というメッセージ、および Azure Notification Hubs の登録 ID を含む登録応答の詳細が表示されます。

    ![Google Chrome - Specify Notification Hub Details][20]  

## <a name="send"></a>Chrome アプリケーションに通知を送信する

テストを行うために、.NET コンソール アプリケーションを使用して Chrome プッシュ通知を送信します。

> [!NOTE]
> プッシュ通知は、パブリック [REST インターフェイス](https://msdn.microsoft.com/library/windowsazure/dn223264.aspx)を介してどのバックエンドからでも Notification Hubs を使用して送信できます。 クロスプラットフォームの詳しい例については、Microsoft の[ドキュメント ポータル](https://azure.microsoft.com/documentation/services/notification-hubs/)を参照してください。

1. Visual Studio で、**[ファイル]** メニューから **[新規作成]**、**[プロジェクト]** の順に選択します。 **[Visual C#]** で、**[Windows]**、**[コンソール アプリケーション]** の順にクリックし、**[OK]** をクリックします。  この手順で、新しいコンソール アプリケーション プロジェクトが作成されます。
2. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。 一番下のウィンドウにパッケージ マネージャー コンソールが表示されます。
3. コンソール ウィンドウで、次のコマンドを実行します。

    ```shell
    Install-Package Microsoft.Azure.NotificationHubs
    ```

   Azure Service Bus SDK への参照が、[WindowsAzure.ServiceBus NuGet パッケージと共に自動的にプロジェクトに追加](https://nuget.org/packages/WindowsAzure.ServiceBus/)されます。
4. `Program.cs` を開いて、次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```
5. `Program` クラスで、次のメソッドを追加します。

    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
        await hub.SendGcmNativeNotificationAsync(message);
    }
    ```

    `<hub name>` プレースホルダーは、[ポータル](https://portal.azure.com)の [Notification Hubs] ページに表示される通知ハブの名前に置き換えるようにしてください。 また、接続文字列プレースホルダーを、「通知ハブを構成する」で取得した `DefaultFullSharedAccessSignature` という接続文字列に置き換えます。

    > [!NOTE]
    > **リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。 **リッスン** アクセスの接続文字列では、プッシュ通知を送信するためのアクセス許可が付与されません。

6. `Main` メソッドに次の呼び出しを追加します。

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

7. Chrome が実行中であることを確認し、コンソール アプリケーションを実行します。
8. 次の通知のポップアップがデスクトップに表示されます。

    ![Google Chrome - Notification][13]
9. Chrome の実行中に、(Windows の) タスク バーの Chrome 通知ウィンドウを使用してすべての通知を確認することもできます。

    ![Google Chrome - Notifications List](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> Chrome アプリケーションをブラウザーで実行したり開いたりする必要はありません (ただし、Chrome ブラウザー自体は実行しておく必要があります)。 また、Chrome の通知ウィンドウにはすべての通知がまとめて表示されます。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、バックエンドに登録されているすべてのクライアントにブロードキャスト通知を送信しました。 特定のデバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome アプリケーションの Notification Hub サンプル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[Chrome Apps Overview (Chrome アプリケーションの概要)]: https://developer.chrome.com/apps/about_apps
[Chrome アプリケーションの GCM サンプル]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[モバイルでの Chrome アプリケーションの実行]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[登録の作成]: https://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js ライブラリ]: https://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
