---
title: "Azure Notification Hubs から Chrome アプリへのプッシュ通知の送信 | Microsoft Docs"
description: "Azure Notification Hubs を使用して Chrome アプリケーションにプッシュ通知を送信する方法について説明します。"
services: notification-hubs
keywords: "モバイル プッシュ通知,プッシュ通知,プッシュ通知,Chrome プッシュ通知"
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 600b1b7e5f3987c9a0acc33b7049f7118442b931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Azure Notification Hubs から Chrome アプリケーションへのプッシュ通知の送信
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

このトピックでは、Azure Notification Hubs を使用して Chrome アプリケーションにプッシュ通知を送信し、Google Chrome ブラウザーのコンテキスト内で表示する方法を紹介します。 このチュートリアルでは、 [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/)を使用してプッシュ通知を受信する Chrome アプリケーションを作成します。 

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F)を参照してください。
> 
> 

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

* [Google Cloud Messaging を有効にする](#register)
* [通知ハブを構成する](#configure-hub)
* [通知ハブに Chrome アプリケーションを接続する](#connect-app)
* [Chrome アプリケーションにプッシュ通知を送信する](#send)
* [その他の機能](#next-steps)

> [!NOTE]
> Chrome アプリケーションのプッシュ通知は汎用的なブラウザー内通知ではなく、Chrome ブラウザーの拡張モデルに固有の機能となっています (「 [Chrome Apps Overview (Chrome アプリケーションの概要)] 」を参照)。 Chrome アプリケーションは、デスクトップ ブラウザーだけでなく、Apache Cordova を通じてモバイル (Android および iOS) でも動作します。 詳細については、 [モバイルでの Chrome アプリケーションの実行] に関するページを参照してください。
> 
> 

[Google Cloud Messaging for Chrome] は廃止されており、同じ GCM で Android デバイスと Chrome インスタンスの両方がサポートされるため、GCM と Azure Notification Hubs の構成は Android 用の構成と同じです。

## <a id="register"></a>Google Cloud Messaging を有効にする
1. [Google Cloud Console] Web サイトに移動し、Google アカウント資格情報でサインインして、 **[Create Project]** ボタンをクリックします。 **[Project Name (プロジェクト名)]** に適切なプロジェクト名を指定して、**[Create (作成)]** ボタンをクリックします。
   
       ![Google Cloud Console - Create Project][1]
2. 作成したプロジェクトの **[Projects (プロジェクト)]** ページにある **[Project Number (プロジェクト番号)]** をメモしておきます。 Chrome アプリケーションでこの番号を **GCM 送信者 ID** として使用して GCM に登録します。
   
       ![Google Cloud Console - Project Number][2]
3. 左側のウィンドウで、**[APIs & auth (API と認証)]** をクリックして下へスクロールし、トグルをクリックして **[Google Cloud Messaging for Android]** を有効にします。 **Google Cloud Messaging for Chrome**を有効にする必要はありません。
   
       ![Google Cloud Console - Server Key][3]
4. 左側のウィンドウで、**[Credentials (資格情報)]** > **[Create New Key (新しいキーの作成)]** > **[Server Key (サーバー キー)]** > **[Create (作成)]** の順にクリックします。
   
       ![Google Cloud Console - Credentials][4]
5. サーバーの **API キー**をメモしておきます。 次の手順では、Notification Hubs でこのキーを構成して、GCM にプッシュ通知を送信できるようにします。
   
       ![Google Cloud Console - API Key][5]

## <a id="configure-hub"></a>通知ハブを構成する
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6.   **[設定]** ブレードで、**[Notification Services]**、**[Google (GCM)]** の順に選択します。 API キーを入力して保存します。

&emsp;&emsp;![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

## <a id="connect-app"></a>通知ハブに Chrome アプリケーションを接続する
これで、通知ハブが GCM と連動するように構成されました。接続文字列を使用して、プッシュ通知の受信と送信の両方にアプリを登録できます。 LK

### <a name="create-a-new-chrome-app"></a>新しい Chrome アプリケーションの作成
以下のサンプルは、 [Chrome アプリケーションの GCM サンプル] に基づいており、Chrome アプリケーションの推奨の作成方法を使用します。 以降、Azure Notification Hubs に関連する手順を重点的に説明しています。 

> [!NOTE]
> この Chrome アプリケーションのソースを [Chrome アプリケーションの Notification Hub サンプル]のページからダウンロードすることをお勧めします。
> 
> 

Chrome アプリケーションは JavaScript を使用して作成されます。推奨される任意のワード エディターを使用して作成を行うことができます。 この Chrome アプリケーションの外観は次のようになります。

![Google Chrome App][15]

1. フォルダーを作成して `ChromePushApp` という名前を付けます。 もちろん名前は任意です。ただし他の名前を使用した場合は、必要なコード セグメント内のパスもそれに合わせて変更してください。
2. 2 つ目の手順で作成したフォルダーに [crypto-js ライブラリ] をダウンロードします。 このライブラリ フォルダーには 2 つのサブフォルダー (`components` と `rollups`) が含まれています。
3. `manifest.json` ファイルを作成します。 すべての Chrome アプリケーションは、マニフェスト ファイルを持ちます。マニフェスト ファイルは、アプリケーションのメタデータのほか、特に重要な情報として、ユーザーがアプリケーションをインストールする際にそのアプリケーションに与えられるすべてのアクセス許可を含んでいます。
   
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
   
    `permissions` 要素に注目してください。この Chrome アプリケーションが GCM からプッシュ通知を受信できる、ということを指定しています。 また、この要素では、Chrome アプリケーションが REST を呼び出して登録を行うための Azure Notification Hubs の URI も指定する必要があります。
    このサンプル アプリでは、元の GCM サンプルから再利用されるソースにあるアイコン ファイル (`gcm_128.png`) を使用します。 [アイコンの基準](https://developer.chrome.com/apps/manifest/icons)さえ満たせば、どのような画像でも代わりに使用することができます。
4. 次のコードを含む `background.js` という名前のファイルを作成します。
   
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
   
    これは、Chrome アプリ ウィンドウの HTML (**register.html**) をポップアップ表示するファイルであり、受信したプッシュ通知を処理するためのハンドラー **messageReceived** も定義しています。
5. `register.html` というファイルを作成します。Chrome アプリケーションの UI が、このファイルによって定義されます。 
   
   > [!NOTE]
   > このサンプルでは **CryptoJS v3.1.2**を使用します。 別のバージョンのライブラリをダウンロードした場合は、 `src` パスのバージョンを忘れずに変更してください。
   > 
   > 
   
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
6. 次のコードを含む `register.js` というファイルを作成します。 このファイルは、 `register.html`の基盤となるスクリプトを指定します。 Chrome アプリケーションではインライン実行が許可されないため、UI 用の基盤となるスクリプトを別途作成する必要があります。
   
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
   
        // From http://msdn.microsoft.com/library/dn495627.aspx
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
                updateLog("Notification Hub Registration succesful!");
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
   
    このスクリプトの主要なパラメーターは次のとおりです。
   
   * **window.onload** は、UI の 2 つのボタンのボタン クリック イベントを定義します。 1 つは GCM への登録を行い、もう 1 つは、GCM への登録後に返される登録 ID を使用して Azure Notification Hubs への登録を行います。
   * **updateLog** は、単純なログ機能の役割を果たす関数です。
   * **registerWithGCM** は、現在の Chrome アプリ インスタンスを登録するために、GCM に対して `chrome.gcm.register` を呼び出す最初のボタン クリック ハンドラーです。
   * **registerCallback** は、GCM 登録の呼び出しから戻った時点で呼び出されるコールバック関数です。
   * **registerWithNH** は 2 番目のボタン クリック ハンドラーで、Notification Hubs への登録を行います。 また、ユーザーが指定した `hubName` と `connectionString` を取得して、Notification Hubs 登録の REST API 呼び出しを作成します。
   * **splitConnectionString** と **generateSaSToken** は、すべての REST API 呼び出しで使用する必要のある SaS トークン作成プロセスの JavaScript の実装です。 詳細については、「 [一般的な概念](http://msdn.microsoft.com/library/dn495627.aspx)」を参照してください。
   * **sendNHRegistrationRequest** は、Azure Notification Hubs に対して HTTP REST 呼び出しを実行する関数です。
   * **registrationPayload** は、登録の XML ペイロードを定義します。 詳細については、「 [登録の作成]」をご覧ください。 このペイロードでは、GCM から受信した ID を使用して登録 ID を更新します。
   * **client** は、HTTP POST 要求に使用する **XMLHttpRequest** のインスタンスです。 `sasToken` を使用して `Authorization` ヘッダーを更新している点に注意してください。 この呼び出しが正常に完了すると、Chrome アプリケーション インスタンスが Azure Notification Hubs に登録されます。

このプロジェクトの全体的なフォルダー構造は、次のようになっている必要があります。![Google Chrome アプリ - フォルダー構造][21]

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
> プッシュ通知は、パブリック <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を介してどのバックエンドからでも Notification Hubs を使用して送信できます。 クロスプラットフォームの詳しい例については、Microsoft の [ドキュメント ポータル](https://azure.microsoft.com/documentation/services/notification-hubs/) を参照してください。
> 
> 

1. Visual Studio で、**[ファイル]** メニューから **[新規作成]**、**[プロジェクト]** の順に選択します。 **[Visual C#]** で、**[Windows]**、**[コンソール アプリケーション]** の順にクリックし、**[OK]** をクリックします。  これで、新しいコンソール アプリケーション プロジェクトが作成されます。
2. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。 これで、パッケージ マネージャー コンソールが表示されます。
3. コンソール ウィンドウで、次のコマンドを実行します。
   
        Install-Package Microsoft.Azure.NotificationHubs
   
       This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.
4. `Program.cs` を開いて、次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.NotificationHubs;
5. `Program` クラスで、次のメソッドを追加します。
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
       Make sure to replace the `<hub name>` placeholder with the name of the notification hub that appears in the [portal](https://portal.azure.com) in your Notification Hub blade. Also, replace the connection string placeholder with the connection string called `DefaultFullSharedAccessSignature` that you obtained in the notification hub configuration section.
   
   > [!NOTE]
   > **リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。 **リッスン** アクセスの接続文字列では、プッシュ通知を送信するためのアクセス許可が付与されません。
   > 
   > 
6. `Main` メソッドに次の呼び出しを追加します。
   
         SendNotificationAsync();
         Console.ReadLine();
7. Chrome が実行中であることを確認し、コンソール アプリケーションを実行します。
8. 次の通知のポップアップがデスクトップに表示されます。
   
       ![Google Chrome - Notification][13]
9. Chrome の実行中に、(Windows の) タスク バーの Chrome 通知ウィンドウを使用してすべての通知を確認することもできます。
   
       ![Google Chrome - Notifications List][14]

> [!NOTE]
> Chrome アプリケーションをブラウザーで実行したり開いたりする必要はありません (ただし、Chrome ブラウザー自体は実行しておく必要があります)。 また、Chrome の通知ウィンドウにはすべての通知がまとめて表示されます。
> 
> 

## <a name="next-steps"> </a>次のステップ
Notification Hubs の詳細については、「 [Notification Hubs の概要]」を参照してください。

特定のユーザーを対象とする方法については、「 [Azure Notification Hubs と .NET バックエンドによるユーザーへの通知] 」チュートリアルをご覧ください。 

対象グループごとにユーザーを区分する場合は、「 [Azure Notification Hubs を使用したニュース速報の送信] 」チュートリアルをご覧ください。

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
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
[Google Cloud Console]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs の概要]: notification-hubs-push-notification-overview.md
[Chrome Apps Overview (Chrome アプリケーションの概要)]: https://developer.chrome.com/apps/about_apps
[Chrome アプリケーションの GCM サンプル]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[モバイルでの Chrome アプリケーションの実行]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[登録の作成]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js ライブラリ]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs と .NET バックエンドによるユーザーへの通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs を使用したニュース速報の送信]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
