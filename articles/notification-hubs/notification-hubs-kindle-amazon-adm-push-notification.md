---
title: "Kindle 用アプリの Azure Notification Hubs の概要 |Microsoft ドキュメント"
description: "このチュートリアルでは、Azure Notification Hubs を使用して、プッシュ通知を kindle 用アプリケーションに送信する方法を学習します。"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 7206f152ed7270abc62536a9ee164f7227833bcc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Kindle 用アプリの Notification Hubs を概要します。
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Notification Hubs を使用して、プッシュ通知を kindle 用アプリケーションに送信する方法を示します。
Amazon デバイス メッセージング (ADM) を使用して、プッシュ通知を受信する空の kindle 用アプリケーションを作成します。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、次の項目が必要です。

* Android SDK が (仮定 Eclipse を使用すること) を取得、 <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android サイト</a>です。
* 手順に従います<a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">開発環境の設定</a>kindle 用の開発環境を設定します。

## <a name="add-a-new-app-to-the-developer-portal"></a>開発者ポータルに新しいアプリを追加します。
1. アプリを最初に、作成、 [Amazon developer portal]です。
   
    ![][0]
2. コピー、**アプリケーション キー**です。
   
    ![][1]
3. ポータルで、アプリの名前をクリックし、 **Device Messaging**タブです。
   
    ![][2]
4. をクリックして**セキュリティ プロファイルを新しく作成**、し、新しいセキュリティ プロファイルを作成 (たとえば、 **TestAdm セキュリティ プロファイル**)。 次に、**[保存]** をクリックします。
   
    ![][3]
5. をクリックして**セキュリティ プロファイル**先ほど作成したセキュリティ プロファイルを表示します。 コピー、**クライアント ID**と**クライアント シークレット**後で使用する値。
   
    ![][4]

## <a name="create-an-api-key"></a>API キーを作成します。
1. 管理者特権でコマンド プロンプトを開きます。
2. Android SDK フォルダーに移動します。
3. 次のコマンドを入力します。
   
        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
   
    ![][5]
4. **キーストア**パスワードを入力**android**です。
5. コピー、 **MD5**指紋です。
6. 上の開発者ポータルでバックアップ、**メッセージング** タブで、をクリックして**Android/kindle 用**アプリのパッケージの名前を入力し、(たとえば、 **com.sample.notificationhubtest**) および**MD5**値に設定して、をクリックして**API キーの生成**です。

## <a name="add-credentials-to-the-hub"></a>ハブに資格情報を追加します。
ポータルでクライアント シークレットとクライアント ID を追加、**構成**通知ハブのタブです。

## <a name="set-up-your-application"></a>アプリケーションを設定します。
> [!NOTE]
> アプリケーションを作成するときは、少なくとも使用 API レベル 17 です。
> 
> 

ADM ライブラリを Eclipse のプロジェクトに追加します。

1. ADM ライブラリを取得する[SDK をダウンロードして]です。 SDK zip ファイルを抽出します。
2. Eclipse で、プロジェクトを右クリックし、をクリックして**プロパティ**です。 選択**Java ビルド パス**左側のクリックして、* * ライブラリ * * 上部にあるタブ。 をクリックして**外部 Jar の追加**、ファイルを選択して`\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar`Amazon SDK を展開したディレクトリからです。
3. NotificationHubs Android SDK (リンク) をダウンロードします。
4. パッケージを解凍し、ファイルをドラッグ`notification-hubs-sdk.jar`に、 `libs` Eclipse 内のフォルダーです。

ADM をサポートするために、アプリ マニフェストを編集します。

1. マニフェストのルート要素には、Amazon 名前空間を追加します。

        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

1. マニフェストの要素の下の最初の要素としてのアクセス許可を追加します。 Substitute **[、パッケージ名]**パッケージ アプリの作成に使用するとします。
   
        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />
   
        <uses-permission android:name="android.permission.INTERNET"/>
   
        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />
   
        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />
   
        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />
2. Application 要素の最初の子として、次の要素を挿入します。 代わりに注意してください**[、サービス名]** (パッケージを含む)、次のセクションで作成すると、置換 ADM メッセージ ハンドラーの名前を持つ**[、パッケージ名]**使用するアプリを作成したパッケージ名を持つ。
   
        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />
   
        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />
   
            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >
   
            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
   
          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>ADM メッセージ ハンドラーを作成します。
1. 継承する新しいクラスを作成する`com.amazon.device.messaging.ADMMessageHandlerBase`し名前を付けます`MyADMMessageHandler`次の図に示すように、します。
   
    ![][6]
2. 次の追加`import`ステートメント。
   
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub
3. 作成したクラスでは、次のコードを追加します。 ハブの名前と接続文字列 (リッスン) の代わりに注意してください。
   
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
          private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }
   
        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }
   
            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }
   
            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();
   
                mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
   
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);
   
            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.mipmap.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                  .setContentText(msg);
   
             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
4. 次のコードを追加、`OnMessage()`メソッド。
   
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
5. 次のコードを追加、`OnRegistered`メソッド。
   
            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }
6. 次のコードを追加、`OnUnregistered`メソッド。
   
         try {
             getNotificationHub(getApplicationContext()).unregister();
         } catch (Exception e) {
             Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
         }
7. `MainActivity`メソッドを次の import ステートメントを追加します。
   
        import com.amazon.device.messaging.ADM;
8. 末尾に次のコードを追加、`OnCreate`メソッド。
   
        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                         MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>アプリに追加して、API キー
1. Eclipse でという名前の新しいファイルを作成する**api_key.txt**プロジェクトの資産をディレクトリにします。
2. ファイルを開き、Amazon developer portal で生成した API キーをコピーします。

## <a name="run-the-app"></a>アプリを実行します。
1. エミュレーターを起動します。
2. エミュレーターでは、最上位からスワイプし、をクリックして**設定**、クリックして**マイ アカウント**Amazon の有効なアカウントを登録します。
3. Eclipse で、アプリを実行します。

> [!NOTE]
> 問題が発生する場合は、エミュレーター (またはデバイス) の時間を確認します。 時刻の値を正確にする必要があります。 Kindle エミュレーターの時間を変更するには、Android SDK プラットフォーム ツール ディレクトリから次のコマンドを実行できます。
> 
> 

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>メッセージを送信します。
.NET を使用してメッセージを送信します。

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[SDK をダウンロードして]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
