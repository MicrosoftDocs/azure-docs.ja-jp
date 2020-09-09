---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156332"
---
### <a name="add-the-google-services-json-file"></a>Google Services JSON ファイルを追加する

1. **Ctrl** キーを押しながら **android** フォルダーを**クリック**し、 **[Open in Android Studio]\(Android Studio で開く\)** を選択します。 次に、 **[Project]\(プロジェクト\)** ビューに切り替えます (まだ切り替えられていない場合)。

1. [Firebase Console](https://console.firebase.google.com) で以前 **PushDemo** プロジェクトを設定したときにダウンロードした *google-services.json* ファイルを探します。 次に、それを **app** モジュールのルート ディレクトリ (**android** > **android** > **app**) にドラッグします。

### <a name="configure-build-settings-and-permissions"></a>ビルド設定とアクセス許可の構成

1. **[Project]\(プロジェクト\)** ビューを **[Android]** に切り替えます。

1. **AndroidManifest.xml** を開き、**INTERNET** および **READ_PHONE_STATE** アクセス許可を、**application** 要素の後、閉じる **</manifest>** タグの前に追加します。

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Firebase SDK の追加

1. **Android Studio** で、プロジェクトレベルの **build.gradle** ファイル (**Gradle Scripts** > **build.gradle (Project: android)** ) を開きます。 また、``buildscript`` > **dependencies** ノードに "com.google.gms:google-services" クラスパスがあることを確認します。

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > **Android プロジェクト**を作成したときに [Firebase Console](https://console.firebase.google.com) で提供された手順に従って、最新バージョンを参照していることを確認します。

1. アプリレベルの **build.gradle** ファイル (**Gradle Scripts** > **build.gradle (Module: app)** ) で、**Google サービス Gradle プラグイン**を適用します。 **android** ノードのすぐ上でプラグインを適用します。

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. 同じファイルの **dependencies** ノードで、**Cloud Messaging** Android ライブラリの依存関係を追加します。

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > [Cloud Messaging Android クライアント ドキュメント](https://firebase.google.com/docs/cloud-messaging/android/client)に従って、最新バージョンを参照していることを確認します。

1. 変更を保存し、 **[Sync Now]\(今すぐ同期\)** ボタン (ツール バー プロンプト) または **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** をクリックします。

### <a name="handle-push-notifications-for-android"></a>Android 用のプッシュ通知の処理

1. **Android Studio** で、**Ctrl** キーを押しながら **com.<your_organization>.pushdemo** パッケージ フォルダー (**app** > **src** > **main** > **kotlin**) を**クリック**し、 **[New]\(新規作成\)** メニューの **[Package]\(パッケージ\)** を選択します。 名前として「**services**」と入力し、**Enter** キーを押します。

1. **Ctrl** キーを押しながら **services** フォルダーを**クリック**し、 **[New]\(新規作成\)** メニューの **[Kotlin File/Class]\(Kotlin ファイル/クラス\)** を選択します。 名前として「**DeviceInstallationService**」と入力し、**Enter** キーを押します。

1. 次のコードを使用して、**DeviceInstallationService** を実装します。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > このクラスは、`com.<your_organization>.pushdemo/deviceinstallation` チャンネルのプラットフォーム固有の対応物を実装します。 これは、**DeviceInstallationService.dart** 内のアプリの Flutter 部分で定義されています。 この場合、呼び出しは共通コードからネイティブ ホストに対して行われます。 **<your_organization>** が使用されている箇所は、必ず自分の組織に置き換えてください。
    >
    > このクラスでは、通知ハブの登録ペイロードの一部として一意の ID ([Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID) を使用) が提供されます。

1. *NotificationRegistrationService* という名前のもう 1 つの **Kotlin ファイルおよびクラス**を **services** フォルダーに追加し、次のコードを追加します。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > このクラスは、`com.<your_organization>.pushdemo/notificationregistration` チャンネルのプラットフォーム固有の対応物を実装します。 これは、**NotificationRegistrationService.dart** 内のアプリの Flutter 部分で定義されています。 この場合、呼び出しはネイティブ ホストから共通コードに対して行われます。 ここでも、 **<your_organization>** が使用されている箇所は、自分の組織に置き換えるように注意してください。

1. *NotificationActionService* という名前のもう 1 つの **Kotlin ファイルおよびクラス**を **services** フォルダーに追加し、次のコードを追加します。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > このクラスは、`com.<your_organization>.pushdemo/notificationaction` チャンネルのプラットフォーム固有の対応物を実装します。 それは **NotificationActionService. dart** 内のアプリの Flutter 部分で定義されました。 この場合、呼び出しは双方向で行うことができます。 **<your_organization>** が使用されている箇所は、必ず自分の組織に置き換えてください。

1. *PushNotificationsFirebaseMessagingService* という名前の新しい **Kotlin ファイルおよびクラス**を **com.<your_organization>.pushdemo** パッケージに追加し、次のコードを使用して実装します。

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > このクラスは、アプリがフォアグラウンドで実行されているときの通知を処理します。 **onMessageReceived** で受信した通知ペイロードにアクションが含まれている場合、**NotificationActionService** の **triggerAction** を条件付きで呼び出します。 また、**onNewToken** 関数のオーバーライドによって **Firebase** トークンが再生成される場合、**NotificationRegistrationService** の **refreshRegistration** も呼び出します。
    >
    > ここでも、 **<your_organization>** が使用されている箇所は、自分の組織に置き換えるように注意してください。

1. **AndroidManifest.xml** (**app** > **src** > **main**) で、**application** 要素の末尾に、**PushNotificationsFirebaseMessagingService** を `com.google.firebase.MESSAGING_EVENT` インテント フィルター付きで追加します。

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. **DeviceInstallationService** に戻って、ファイルの先頭に以下のインポートがあることを確認します。

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > **<your_organization>** を自分の組織の値に置き換えます。

1. **PushNotificationFirebaseMessagingService** からトークン値を取得するように、プレースホルダー テキスト *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* を更新します。

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. **MainActivity** で、ファイルの先頭に以下のインポートがあることを確認します。

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > **<your_organization>** を自分の組織の値に置き換えます。

1. **DeviceInstallationService** への参照を格納する変数を追加します。

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. **Intent** に **action** という名前の追加の値があるかどうかを調べるために、**processNotificationActions** という名前の新しいメソッドを追加します。 アクションがアプリの起動中に処理されている場合は、そのアクションを条件付きでトリガーするか、後で使用するために格納します。

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. **processNotificationActions** を呼び出すように、**onNewIntent** 関数をオーバライドします。

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > **MainActivity** の **LaunchMode** は **SingleTop** に設定されているため、**Intent** は **onCreate** 関数ではなく **onNewIntent** 関数を通じて既存の **Activity** インスタンスに送信されます。そのため、**onCreate** 関数と **onNewIntent** 関数の両方で、受信**インテント**を処理する必要があります。

1. **onCreate** 関数をオーバーライドし、**deviceInstallationService** を **DeviceInstallationService** の新しいインスタンスに設定します。

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. **PushNotificationFirebaseMessagingServices** の **notificationActionService** プロパティと **notificationRegistrationService** プロパティを設定します。

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. 同じ関数で、**FirebaseInstanceId.getInstance().instanceId** を条件付きで呼び出します。 **refreshRegistration** を呼び出す前に結果の**トークン**値を **PushNotificationFirebaseMessagingService** に設定するように、**OnCompleteListener** を実装します。

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. 引き続き **onCreate** で、関数の末尾で **processNotificationActions** を呼び出します。 このアクションがアプリの起動中に処理されていることを示すには、*launchAction* 引数に *true* を使用します。

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> プッシュ通知を受信し続けるには、アプリを実行して停止するたびにデバッグ セッションからアプリを再登録する必要があります。
