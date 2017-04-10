---
title: "Azure Mobile Engagement Android SDK の統合"
description: "Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 97ce7f4d682ec12470db4248d046a8367840f0bd
ms.lasthandoff: 11/17/2016


---
# <a name="upgrade-procedures"></a>アップグレードの手順
既に古いバージョンの SDK をアプリケーションに統合している場合は、SDK をアップグレードする際に次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。 たとえば、1.4.0 から 1.6.0 に移行する場合、まず「1.4.0から 1.5.0」への手順を実行してから「1.5.0 から 1.6.0」への手順を実行する必要があります。

どのバージョンからアップグレードする場合でも、 `mobile-engagement-VERSION.jar` を新しいバージョンのものに置き換える必要があります。

## <a name="from-420-to-421"></a>4.2.0 から 4.2.1 に移行
この手順は、実際にはどのバージョンの SDK でも実行できます。これは、リーチ アクティビティを統合する際のセキュリティ強化です。

現時点では、すべてのリーチ アクティビティに `exported="false"` を追加する必要があります。

`AndroidManifest.xml`では、リーチ アクティビティは次のようになります。

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

## <a name="from-400-to-410"></a>4.0.0 から 4.1.0 に移行
SDK で Android M の新しいアクセス許可モデルを処理できるようになりました。

場所の機能または大きな画像の機能を利用する場合、 [このセクション](mobile-engagement-android-integrate-engagement.md#android-m-permissions)をお読みください。

新しいアクセス許可モデルに加え、実行時に場所の機能を構成できるようになりました。
場所のマニフェスト パラメーターとの互換性は維持されていますが、推奨されません。 ランタイム構成を使用するには、 ``AndroidManifest.xml``から次のセクションを削除します。

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

そして、 [この更新されたプロシージャ](mobile-engagement-android-integrate-engagement.md#location-reporting) を読み込み、代わりにランタイム構成を使用します。

## <a name="from-300-to-400"></a>3.0.0 から 4.0.0 に移行
### <a name="native-push"></a>ネイティブ プッシュ通知
ネイティブ プッシュ通知 (GCM/ADM) がアプリ内通知にも使用されるようになったので、すべての種類のプッシュ キャンペーンについてネイティブのプッシュの資格情報を設定する必要があります。

設定が完了していない場合は、 [こちらの手順](mobile-engagement-android-integrate-engagement-reach.md#native-push)に従ってください。

### <a name="androidmanifestxml"></a>AndroidManifest.xml
``AndroidManifest.xml``の Reach 統合が変更されました。

こちらに置き換えてください。

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

別

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

アナウンス (テキスト/Web コンテンツを含む) またはポーリングをクリックすると、場合によって読み込み画面が表示されるようになりました。
4.0.0 でこれらのキャンペーンを動作させるには、次を追加する必要があります。

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>リソース
プロジェクトに新しい `res/layout/engagement_loading.xml` ファイルが埋め込まれました。

## <a name="from-240-to-300"></a>2.4.0 から 3.0.0 に移行
Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。 以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 2.4.0 に移行し、次の手順を適用してください。

> [!IMPORTANT]
> Capptain と Mobile Engagement は、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。 アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。
> 
> 

### <a name="jar-file"></a>JAR ファイル
`libs` フォルダーの `capptain.jar` を `mobile-engagement-VERSION.jar` に置き換えます。

### <a name="resource-files"></a>リソース ファイル
提供されるすべてのリソース ファイル (`capptain_` で始まるファイル) を新しいファイル (`engagement_` で始まるファイル) に置き換える必要があります。

これらのファイルがカスタマイズされている場合、新しいファイルにもそのカスタマイズを再適用する必要があります。**リソース ファイル内のすべての識別子の名前も変更されています**。

### <a name="application-id"></a>アプリケーション ID
Engagement では、接続文字列を使用してアプリケーション ID などの SDK の識別子を構成します。

ランチャー アクティビティで、 `EngagementAgent.init` メソッドを次のように実行します。

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

`EngagementAgent.init` メソッドは `CapptainAgent.configure` に置き換えられるため、このメソッドに対する呼び出しをすべて削除してください。

`appId` は `AndroidManifest.xml` を使用して構成できなくなりました。

まだ存在する場合は、`AndroidManifest.xml` からこのセクションを削除してください。

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java API
SDK の Java クラスに対する呼び出しの名前をすべて変更する必要があります。たとえば、`CapptainAgent.getInstance(this)` は `EngagementAgent.getInstance(this)`、`extends CapptainActivity` は `extends EngagementActivity` などのように名前を変更します。

既定のエージェントの設定ファイルに統合されている場合、既定のファイル名は `engagement.agent` に、キーは `engagement:agent` になります。

Web 通知を作成する場合、Javascript バインダーは `engagementReachContent` になります。

### <a name="androidmanifestxml"></a>AndroidManifest.xml
多数の変更が発生し、サービスが共有されなくなったため、多くの受信者をエクスポートできなくなりました。

サービスの宣言が簡単になり、インテント フィルターとその内部のすべてのメタデータが削除され、 `exportable=false`が追加されました。

また、すべての名前に engagement が追加されました。

次のようになります。

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

テスト ログを有効にする場合は次のようにします。メタデータはアプリケーション タグに移動され、名前が変更されています。

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

その他すべてのメタデータの名前も変更されています。一覧は次のとおりです (使用するメタデータの名前のみ変更します)。

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

SDK から Google Play と SmartAd の追跡機能が削除されたため、この部分は置き換えではなく削除する必要があります。

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Reach のアクティビティは次のように宣言します。

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

Reach のアクティビティがカスタマイズされている場合は、インテント アクションを `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` または `com.microsoft.azure.engagement.reach.intent.action.POLL` のいずれかに一致するように変更します。

ブロードキャスト レシーバーの名前が変更され、`exported=false` が追加されました。 レシーバーの新しい詳細の一覧は次のとおりです (使用する受信者の名前のみ変更します)。

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

追跡のレシーバーは削除されたため、このセクションは削除する必要があります。

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

ブロードキャスト レシーバー **EngagementMessageReceiver** を実装する宣言は `AndroidManifest.xml` では変更されているのでご注意ください。 これは、任意の XMPP エンティティから任意の XMPP メッセージを送受信する API と、デバイス間でメッセージを送受信する API が削除されているためです。 このため、 **EngagementMessageReceiver** の実装から次のコールバックを削除する必要があります。

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

と

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

その後、 **EngagementAgent** に対する呼び出しを削除します。

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

と

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard
Proguard の構成はブランド変更の影響を受けるため、ルールは次のようになります。

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }


