---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9330488debb0b7611fdd63656e4c555566c749dd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060440"
---
### <a name="configure-required-android-packages"></a>必要な Android パッケージを構成する

パッケージは、アプリをビルドするときに[自動的にリンク](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)されます。 構成プロセスを完了するには、次のいくつかの追加の手順を実行します。

### <a name="configure-android-manifest"></a>Android マニフェストを構成する

"android/app/src/main/AndroidManifest.xml" で、パッケージ名、アクセス許可、および必要なサービスを確認します。 `RNPushNotificationPublisher` および `RNPushNotificationBootEventReceiver` レシーバーを登録済みであり、`RNPushNotificationListenerService` サービスを登録済みであることを確認します。 通知メタデータを使用して、プッシュ通知の外観をカスタマイズできます。

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>Google サービスを構成する

"android/app/build.gradle" で、Google サービスを登録します。

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

FCM の設定中にダウンロードした "google-services.json" ファイルをプロジェクト フォルダー "android/app/" にコピーします。

### <a name="handle-push-notifications-for-android"></a>Android 用のプッシュ通知を処理する

受信した Android プッシュ通知を処理するように既存の `RNPushNotificationListenerService` サービスを構成済みです。 このサービスは、以前にアプリケーション マニフェストに登録されています。 これにより、受信した通知を処理し、クロスプラットフォームの React Native の部分にプロキシします。 追加の手順は必要ありません。
