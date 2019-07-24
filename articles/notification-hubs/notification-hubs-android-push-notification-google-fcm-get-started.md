---
title: Azure Notification Hubs と Firebase Cloud Messaging を使用して Android アプリにプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs と Google Firebase Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する方法について学習します。
services: notification-hubs
documentationcenter: android
keywords: プッシュ通知,プッシュ通知,android プッシュ通知,fcm,firebase cloud messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/05/2019
ms.author: jowargo
ms.openlocfilehash: 2fe448f3ed91f2c6dd242c24aa378c3541eceecc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57857948"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>チュートリアル:Azure Notification Hubs と Google Firebase Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

このチュートリアルでは、Azure Notification Hubs と Firebase Cloud Messaging (FCM) を使用して Android アプリケーションにプッシュ通知を送信する方法を示します。 このチュートリアルでは、Firebase Cloud Messaging (FCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。

このチュートリアルの完成したコードについては、GitHub の [こちら](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp)からダウンロードできます。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Android Studio プロジェクトを作成します。
> * Firebase Cloud Messaging をサポートする Firebase プロジェクトを作成します。
> * 通知ハブを作成します。
> * アプリを通知ハブに接続します。
> * アプリをテストします。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/free/)を参照してください。

* このチュートリアルでは、上記のアクティブな Azure アカウント以外に、最新版の [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)が必要です。
* Android 2.3 以上 (Firebase Cloud Messaging 対応)。
* Firebase Cloud Messaging には Google Repository revision 27 以上が必要です。
* Google Play Services 9.0.2 以上 (Firebase Cloud Messaging 対応)。
* このチュートリアルを完了することは、Android アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。

## <a name="create-an-android-studio-project"></a>Android Studio プロジェクトを作成する

1. Android Studio を起動します。
2. メニューの **[File]\(ファイル\)** を選択し、**[New]\(新規\)** をポイントして、**[New Project]\(新しいプロジェクト\)** を選択します。 
2. **[Choose your project]\(プロジェクトの選択\)** ページで、**[Empty Activity]\(空のアクティビティ\)** を選択して **[Next]\(次へ\)** を選択します。 
3. **[Configure your project]\(プロジェクトの構成\)** ページで、次の手順のようにします。 
    1. アプリケーションの**名前**を入力します。
    2. プロジェクト ファイルを保存する場所を指定します。 
    3. **[完了]** を選択します。 

        ![プロジェクトを構成する](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>FCM をサポートする Firebase プロジェクトを作成する

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>通知ハブを構成する

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Firebase Cloud Messaging の設定をハブ用に構成する

1. 左側のメニューの **[Settings]\(設定\)** で、**[Google (GCM/FCM)]** を選択します。 
2. 前に保存した FCM プロジェクトの**サーバー キー**を貼り付けます。 
3. ツールバーの **[保存]** を選択します。 

    ![Azure Notification Hubs - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. 通知ハブが正常に更新されたことを示すアラートのメッセージが表示されます。 **[Save]\(保存\)** ボタンが無効になります。 

これで、通知ハブが Firebase Cloud Messaging と連動するように構成されました。接続文字列を使用して、プッシュ通知の受信と送信の両方にアプリを登録できます。

## <a id="connecting-app"></a>通知ハブにアプリを接続する

### <a name="add-google-play-services-to-the-project"></a>プロジェクトへの Google Play Services の追加

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Azure Notification Hubs ライブラリを追加する

1. **[app (アプリ)]** の `Build.Gradle` ファイルの **dependencies** セクションに次の行を追加します。

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. **[dependencies]** セクションの後に次のリポジトリを追加します。

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Google Firebase のサポートを追加する

1. **アプリ**の `Build.Gradle` ファイルで、**dependencies** セクションに次の行を追加します (まだ存在しない場合)。 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.7'
    ```

2. ファイルの最後に次のプラグインを追加します (まだ存在しない場合)。 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>AndroidManifest.xml ファイルを更新する

1. FCM をサポートするには、[Google の FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) を利用し、[登録トークンの取得](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register)に使用されるインスタンス ID リスナー サービスをコードに実装する必要があります。 このチュートリアルでは、クラスの名前は `MyInstanceIDService` です。

    次のサービス定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。

    ```xml
    <service android:name=".MyInstanceIDService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. FirebaseInstanceId API から FCM 登録トークンを受信したら、それを使用して [Azure 通知ハブに登録します](notification-hubs-push-notification-registration-management.md)。 この登録は、`RegistrationIntentService` という名前の `IntentService` を使用してバックグラウンドでサポートします。 このサービスはまた、FCM 登録トークンを更新する役割も果たします。

    次のサービス定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. 通知を受信するレシーバーも定義する必要があります。 次のレシーバー定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。 

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

    > [!IMPORTANT]
    > `<your package NAME>` プレースホルダーを、`AndroidManifest.xml` ファイルの先頭にある実際のパッケージ名に置き換えます。
4. ツール バーの **[Sync Now]\(今すぐ同期\)** を選択します。
5. 次の必要な FCM 関連のアクセス許可を `</application>` タグの**下**に追加します。

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>コードを追加する

1. [Project (プロジェクト)] ビューで、**[app]** > **[src]** > **[main]** > **[java]** の順に展開します。 **[java]** のパッケージ フォルダーを右クリックして、**[New (新規)]**、**[Java Class (Java クラス)]** の順にクリックします。 名前に「`NotificationSettings`」と入力して、**[OK]** を選択します。

    以下の `NotificationSettings` クラスのコード内にある次の 3 つのプレースホルダーを忘れずに更新してください。

   * **HubListenConnectionString**: ハブの **DefaultListenAccessSignature** 接続文字列。 接続文字列をコピーするには、[Azure Portal] で、ハブにある **[アクセス ポリシー]** をクリックします。
   * **HubName**: [Azure portal] のハブ ページに表示される通知ハブの名前を使用します。

     `NotificationSettings` のコードは次のとおりです。

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > 先に進む前に、通知ハブの**名前**と **DefaultListenSharedAccessSignature** を入力します。 
2. `MyInstanceIDService`という名前の別のクラスを追加します。 このクラスは、インスタンス ID リスナー サービスの実装です。

    このクラスのコードで、`IntentService` が呼び出され、バックグラウンドで [FCM トークンが更新](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)されます。

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class MyInstanceIDService extends FirebaseInstanceIdService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.d(TAG, "Refreshing FCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. `RegistrationIntentService`という名前の別の新しいクラスをプロジェクトに追加します。 このクラスは `IntentService` インターフェイスを実装し、[FCM トークンの更新](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)と[通知ハブへの登録](notification-hubs-push-notification-registration-management.md)を処理します。

    次のコードをこのクラスに使用します。

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                String FCM_token = FirebaseInstanceId.getInstance().getToken();
                Log.d(TAG, "FCM Registration Token: " + FCM_token);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check if the token may have been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating our registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. `MainActivity` クラスで、クラス宣言の上に次の `import` ステートメントを追加します。

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

5. クラスの先頭に次のメンバーを追加します。 これらのフィールドは、[Google の推奨に従って Google Play 開発者サービスの可用性をチェックする](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)ために使用します。

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

6. `MainActivity` クラスに、Google Play 開発者サービスが利用可能かどうかを確認する次のメソッドを追加します。

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog that allows users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. `MainActivity` クラスに次のコードを追加します。このコードは、Google Play 開発者サービスを確認してから、`IntentService` を呼び出して FCM 登録トークンを取得し、通知ハブに登録します。

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. `MainActivity` クラスの `OnCreate` メソッドで、アクティビティの作成時に登録プロセスを開始するために次のコードを追加します。

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        MyHandler.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

9. アプリの状態を確認してアプリ内で報告するには、次の追加メソッドを `MainActivity` に追加します。

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. `ToastNotify` メソッドでは "*Hello World*" `TextView` コントロールを使用し、アプリで状態と通知を継続的に報告します。 **res** -> **layout** -> **activity_main.xml** レイアウトで、そのコントロールに次の ID を追加します。

    ```java
    android:id="@+id/text_hello"
    ```

11. 次に、AndroidManifest.xml で定義したレシーバーのサブクラスを追加します。 `MyHandler`という名前の別の新しいクラスをプロジェクトに追加します。

12. 次の import ステートメントを `MyHandler.java` の先頭に追加します。

    ```java
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;    
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    ```

13. `MyHandler` クラス用に次のコードを追加して、`com.microsoft.windowsazure.notifications.NotificationsHandler` のサブクラスにします。

    このコードによって `OnReceive` メソッドがオーバーライドされるため、ハンドラーは受信した通知を報告します。 ハンドラーは `sendNotification()` メソッドを使用して Android の通知マネージャーにもプッシュ通知を送信します。 `sendNotification()` メソッドは、アプリが動作していないときに通知を受信した場合に実行される必要があります。

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;
    
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
                NotificationsManager.handleNotifications(context, "", MyHandler.class);
            }
        }
    }
    ```

14. Android Studio のメニュー バーで、**[Build (ビルド)]** > **[Rebuild Project (プロジェクトのリビルド)]** の順にクリックし、コードにエラーがないことを確認します。 `ic_launcher` アイコンに関するエラーが発生する場合は、AndroidManifest.xml ファイルから次のステートメントを削除します。 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. お使いのデバイスでアプリを実行し、そのアプリが通知ハブに正常に登録されることを確認します。

    > [!NOTE]
    > インスタンス ID サービスの `onTokenRefresh()` メソッドが呼び出されるまでは、初期起動時に登録が失敗する場合があります。 更新すると、通知ハブに正常に登録されるようになります。

    ![デバイス登録成功](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>通知ハブから通知をテスト送信する

次のアクションを実行することによって、[Azure Portal] からプッシュ通知を送信できます。

1. Azure portal に移動し、お使いの通知ハブの **[Notification Hub]** ページで、**[トラブルシューティング]** セクションの **[テスト送信]** を選択します。
3. **[プラットフォーム]** として、**[Android]** を選択します。
4. **[送信]** を選択します。  Android デバイス上でモバイル アプリを実行していないため、まだそのデバイスに通知は表示されません。 モバイル アプリを実行したら、再度 **[送信]** ボタンを選択して通知メッセージを表示します。
5. 一番下の一覧にある操作の**結果**を確認します。

    ![Azure Notification Hubs - Test Send](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. デバイスに通知メッセージが表示されます。 

    ![デバイスの通知メッセージ](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>エミュレーターでモバイル アプリを実行する
エミュレーターの内部でプッシュ通知をテストする場合は、エミュレーター イメージがアプリケーション用に選択した Google API レベルをサポートしていることを確認してください。 イメージでネイティブの Google API がサポートされていない場合、**SERVICE\_NOT\_AVAILABLE** 例外を受け取ることがあります。

さらに、**[設定]** > **[アカウント]** で、実行中のエミュレーターに Google アカウントを追加したことを確認します。 アカウントを追加していないと、GCM での登録の試行が **AUTHENTICATION\_FAILED** 例外につながる可能性があります。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Firebase Cloud Messaging を使用して Android デバイスにプッシュ通知を送信しました。 Google Cloud Messaging を使用してプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[Google Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する](notification-hubs-android-push-notification-google-gcm-get-started.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
