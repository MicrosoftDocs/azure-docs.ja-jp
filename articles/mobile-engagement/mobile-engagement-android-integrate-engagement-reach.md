---
title: "Azure Mobile Engagement Android SDK の統合"
description: "Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd8ba95ee1fb2703926fb35cdb49e6a503637a7d
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Engagement Reach を Android に統合する方法
> [!IMPORTANT]
> このガイドの手順を実行する前に、「Engagement を Android に統合する方法」のドキュメントの統合手順を実行する必要があります。
> 
> 

## <a name="standard-integration"></a>標準統合
Reach SDK には **Android サポート ライブラリ (v4)**が必要です。

**Eclipse** でプロジェクトにライブラリを追加する最も簡単な方法は、`Right click on your project -> Android Tools -> Add Support Library...` です。

Eclipse を使用しない場合は、 [こちら]の手順をご覧ください。

プロジェクトの SDK から Reach リソースのファイルをコピーします。

* SDK とともに配布されたファイルを、`res/layout` フォルダーからアプリケーションの `res/layout` フォルダーにコピーします。
* SDK とともに配布されたファイルを、`res/drawable` フォルダーからアプリケーションの `res/drawable` フォルダーにコピーします。

`AndroidManifest.xml` ファイルを編集します。

* 次のセクションを (`<application>` タグと `</application>` タグの間に) 追加します。
  
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
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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
* ブート時にクリックされなかったシステム通知を再生するには、このアクセス許可が必要です (アクセス許可がない場合、それらの通知はディスクに残りますが表示できなくなるため、必ず含める必要があります)。
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* 次のセクション (`<application>` タグと `</application>` タグの間) をコピーして編集することで、通知 (アプリとシステムの両方) に使用するアイコンを指定します。
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> このセクションは、Reach キャンペーンの作成時にシステム通知を使用する場合に **必須** です。 Android では、アイコンのないシステム通知は表示されません。 そのため、このセクションを省略すると、エンド ユーザーは通知を受け取ることができません。
> 
> 

* 大きな画像を使用するシステム通知のキャンペーンを作成する場合、次のアクセス許可 ( `</application>` タグの後) を追加する必要があります。
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * Android M で、アプリケーションの対象が Android API レベル 23 以上になる場合、 ``WRITE_EXTERNAL_STORAGE`` アクセス許可はユーザー承認を必要とします。 [このセクション](mobile-engagement-android-integrate-engagement.md#android-m-permissions)をお読みください。
* また、システム通知では、Reach キャンペーンでデバイスの音を鳴らしたりバイブレーションが動作したりするように指定できます。 キャンペーンが機能するには、次のアクセス許可 ( `</application>` タグの後) を宣言する必要があります。
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Reach キャンペーン マネージャーで音を鳴らすオプションやバイブレーションのオプションを有効にしても、このアクセス許可が存在しない場合は Android でシステム通知が表示されません。
* **ProGuard** を使用して構築したアプリケーションに Android サポート ライブラリや Engagement jar に関連するエラーが発生した場合は、次の行を `proguard.cfg` ファイルに追加します。
  
          -dontwarn android.**
          -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Native Push (ネイティブのプッシュ)
Reach モジュールを構成したので、デバイスのキャンペーンを受信できるようにネイティブ プッシュ通知を設定する必要があります。

Android では次の 2 つのサービスをサポートしています。

* Google Play デバイス: 「[GCM を Mobile Engagement に統合する方法](mobile-engagement-android-gcm-integrate.md)」ガイドに従って、[Google Cloud Messaging] を使用します。
* Amazon デバイス: 「[ADM を Engagement に統合する方法](mobile-engagement-android-adm-integrate.md)」ガイドに従って、[Amazon Device Messaging] を使用します。

Amazon と Google Play の両方のデバイスを対象とするコードは、1 つの AndroidManifest.xml/APK 内で開発することもできます。 ただし、アプリケーションに GCM のコードが見つかると、Amazon に送信する際に却下されることがあります。

その場合は、複数の APK を使用してください。

**お使いのアプリケーションで Reach キャンペーンを受信して表示する準備が整いました。**

## <a name="how-to-handle-data-push"></a>データプッシュを管理する方法
### <a name="integration"></a>統合
アプリケーションで Reach データプッシュを受信するには、`com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` のサブクラスを作成し、それを `AndroidManifest.xml` ファイル (`<application>` タグと `</application>` タグの間) で参照する必要があります。

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

これにより、`onDataPushStringReceived` コールバックと `onDataPushBase64Received` コールバックをオーバーライドできます。 たとえば次のようになります。

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>カテゴリ
カテゴリ パラメーターは、データ プッシュ キャンペーンを作成するときに指定でき、データ プッシュをフィルター処理するために使用できます。 これは、複数のブロードキャスト レシーバーが異なるタイプのデータプッシュを管理する場合や、異なる種類の「 `Base64` 」データをプッシュしてそれらを解析する前にタイプを特定する場合に便利です。

### <a name="callbacks-return-parameter"></a>コールバックのリターン パラメーター
`onDataPushStringReceived` や `onDataPushBase64Received` のリターン パラメーターを正しく管理するためのガイドラインを紹介します。

* データプッシュの管理方法を把握していないブロードキャスト レシーバーは、コールバックで `null` を返します。 ブロードキャスト レシーバーがデータ プッシュを管理するかどうかを指定するには、カテゴリを使用します。
* ブロードキャスト レシーバーのうち、データ プッシュを受け取る場合は、コールバックで `true` を返します。
* ブロードキャスト レシーバーのうち、データ プッシュを認識するもののなんらかの理由で破棄する場合は、コールバックで `false` を返します。 たとえば、受信したデータが無効である場合は `false` を返します。
* 同じデータ プッシュに対して、あるブロードキャスト レシーバーが `true` を、もう一方が `false` を返す場合は、動作が定義されていません。このようなことがないようにしてください。

リターン タイプは Reach の統計情報でのみ使用されます。

* `Replied` は、ブロードキャスト レシーバーの 1 つが `true` または `false` のいずれかを返すとインクリメントされます。
* `Actioned` は、ブロードキャスト レシーバーが `true` を返す場合にのみインクリメントされます。

## <a name="how-to-customize-campaigns"></a>キャンペーンをカスタマイズする方法
キャンペーンをカスタマイズするには、Reach SDK に用意されたレイアウトを修正します。

レイアウトに使用される識別子や識別子を使用するビューのタイプ (特にテキスト ビューやイメージ ビュー) はそのままにしてください。 領域を表示/非表示にするためだけに使用される一部のビューは変更してもかまいません。 用意されたレイアウトでタイプを変更する場合は、ソース コードを確認してください。

### <a name="notifications"></a>通知
通知にはシステム通知とアプリ内通知の 2 種類があり、別個のレイアウト ファイルが使用されます。

#### <a name="system-notifications"></a>システム通知
システム通知をカスタマイズするには、 **カテゴリ**を使用する必要があります。 [カテゴリ](#categories)をご覧ください

#### <a name="in-app-notifications"></a>アプリ内通知
既定では、アプリ内通知とは Android メソッド `addContentView()`によって現在のアクティビティ ユーザー インターフェイスに動的に追加されるビューを指します。 これは、通知オーバーレイと呼ばれます。 通知オーバーレイを使用すると、アプリケーションのレイアウトを変更する必要がないため、統合が簡単です。

通知オーバーレイの見た目を修正するには、`engagement_notification_area.xml` ファイルをニーズに合わせて修正します。

> [!NOTE]
> `engagement_notification_overlay.xml` ファイルは通知オーバーレイの作成に使用され、`engagement_notification_area.xml` ファイルが含まれます。 また、ニーズに合わせて変更できます (オーバーレイ内部の通知領域の配置など)。
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>通知レイアウトをアクティビティ レイアウトの一部に含める
オーバーレイを使用すると統合が簡単になりますが、場合によって不便であり、特定の条件では副作用が発生することもあります。 オーバーレイ システムはアクティビティ レベルでカスタマイズ可能であり、特定のアクティビティにおいて副作用を簡単に防ぐことができます。

Android の **include** ステートメントにより、既存のレイアウトに通知レイアウトを含めることができます。 次の例は、`ListView` のみが含まれる修正後の `ListActivity` レイアウトを示します。

**Engagement 統合前:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Engagement 統合後:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

この例では、元のレイアウトのトップ レベル要素としてリスト ビューが使用されていたため、親コンテナーを追加しました。 また、`android:layout_height="fill_parent"` で構成されたリスト ビューの下にビューを追加できるように、`android:layout_weight="1"` も追加しました。

Engagement Reach SDK では、このアクティビティに通知レイアウトが含まれていることが自動的に検知されるため、このアクティビティに対してオーバーレイを追加しません。

> [!TIP]
> アプリケーションで ListActivity を使用すると、表示される Reach オーバーレイによってリスト ビュー内のクリックされたアイテムに応答できなくなります。 これは既知の問題です。 この問題を解決するには、前述のサンプルのように独自のリスト アクティビティ レイアウトに通知レイアウトを組み込むことをお勧めします。
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>アクティビティごとにアプリケーション通知を無効にする
特定のアクティビティにオーバーレイが追加されないように独自のレイアウトに通知レイアウトを追加しない場合は、`AndroidManifest.xml` で次のように `meta-data` セクションを追加することで、そのアクティビティのオーバーレイを無効にできます。

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="a-namecategoriesa-categories"></a><a name="categories"></a> カテゴリ
指定のレイアウトを修正すると、通知すべての見た目が修正されます。 カテゴリを使用して、通知対象のさまざまな外観 (場合によっては動作) を定義できます。 リーチ キャンペーンを作成する場合にカテゴリを指定できます。 また、カテゴリを使用してお知らせとポーリングもカスタマイズできます。これについては、このドキュメントの後半で説明します。

通知のカテゴリ ハンドラーを登録するには、アプリケーションが初期化される際に呼び出しを追加する必要があります。

> [!IMPORTANT]
> 先に進む前に、「Engagement を Android に統合する方法」トピックで、android:process 属性 \<android-sdk-engagement-process\> に関する警告を確認してください。
> 
> 

次の例では、前述の警告について把握しており、 `EngagementApplication`のサブクラスを使用していることを前提としています。

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

`MyNotifier` オブジェクトは、通知カテゴリ ハンドラーの実装です。 これは、`EngagementNotifier` インターフェイスの実装または既定の実装 `EngagementDefaultNotifier` のサブクラスのいずれかです。

同じ通知で複数のカテゴリを管理できます。次のように登録します。

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

既定のカテゴリ実装を置き換えるには、実装を次の例のように登録します。

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

ハンドラーに使用される現在のカテゴリは、ほとんどのメソッドにパラメーターとして渡されます。これは `EngagementDefaultNotifier` でオーバーライドできます。

これは `String` パラメーターとして渡されるか、`getCategory()` メソッドを持つ `EngagementReachContent` オブジェクトに間接的に渡されます。

通知作成プロセスのほとんどは、`EngagementDefaultNotifier` のメソッドを再定義することで変更できます。高度なカスタマイズについては、技術ドキュメントやソース コードをご覧ください。

##### <a name="in-app-notifications"></a>アプリ内通知
特定のカテゴリに対して代替のレイアウトを使用するには、次の例のようにこれを実装します。

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**`my_notification_overlay.xml` の例:**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

オーバーレイのビュー識別子が標準のものとは異なることがわかります。 オーバーレイでは、各レイアウトで固有の識別子を使用することが重要です。

**`my_notification_area.xml` の例:**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

通知領域のビュー識別子が標準のものとは異なることがわかります。 通知領域では、各レイアウトで固有の識別子を使用することが重要です。

このカテゴリの簡単な例により、アプリケーション通知 (アプリ内通知) が画面の上部に表示されます。 通知領域で使用される標準の識別子自体は変更していません。

これを変更するには、 `EngagementDefaultNotifier.prepareInAppArea` メソッドを再定義する必要があります。 このレベルの高度なカスタマイズを実行するには、`EngagementNotifier` および `EngagementDefaultNotifier` の技術ドキュメントとソース コードをご覧になることをお勧めします。

##### <a name="system-notifications"></a>システム通知
`EngagementDefaultNotifier` を拡張することで、`onNotificationPrepared` をオーバーライドして既定の実装によって用意された通知を置き換えることができます。

次に例を示します。

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

この例では、"ongoing" カテゴリの使用によりコンテンツのシステム通知が進行中のイベントとして表示されます。

`Notification` オブジェクトを一から構築するには、メソッドに `false` を返し、`NotificationManager` で自身に `notify` を呼び出します。 この場合、`contentIntent`、`deleteIntent`、および `EngagementReachReceiver` で使用される通知識別子を維持することが重要です。

このような実装の正しい例は次のとおりです。

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>通知のみのアナウンス
通知のみのアナウンスに対するクリックは、`EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` をオーバーライドして用意された `Intent` を修正することで管理方法をカスタマイズできます。 このメソッドを使用すると、フラグを簡単にチューニングできます。

`SINGLE_TOP` フラグを追加する例は次のとおりです。

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

従来の Engagement とは異なり、アクション URL を使用しないシステム通知でバックグラウンドのアプリケーションが起動されるようになりました。このため、このメソッドはアクション URL を使用しないアナウンスで呼び出すことができます。 インテントをカスタマイズする際にはそれを考慮してください。

また、`EngagementNotifier.executeNotifAnnouncementAction` を一から実装することもできます。

##### <a name="notification-life-cycle"></a>通知のライフ サイクル
既定のカテゴリを使用する場合、いくつかのライフ サイクル メソッドは、`EngagementReachInteractiveContent` オブジェクトを呼び出して統計情報を報告し、キャンペーンの状態を更新します。

* 通知がアプリケーションまたはステータス バーに表示されると、`handleNotification` で `true` が返された場合、`EngagementReachAgent` によって `displayNotification` メソッドが呼び出されます (統計情報のレポートが作成されます)。
* 通知を閉じると、`exitNotification` メソッドが呼び出されて統計情報が報告され、次のキャンペーンを処理できるようになります。
* 通知がクリックされると、 `actionNotification` が呼び出され、統計情報のレポートが作成されて関連するインテントが起動されます。

`EngagementNotifier` の実装が既定の動作をバイパスする場合は、これらのライフ サイクル メソッドを自分で呼び出す必要があります。 次の例では、既定の動作がバイパスされるいくつかのケースを示しています。

* `EngagementDefaultNotifier` を拡張しない場合 (最初からカテゴリの処理を実装した場合など)。
* システム通知で、`onNotificationPrepared` をオーバーライドして `Notification` オブジェクトで `contentIntent` または `deleteIntent` を修正した。
* アプリ内通知で、`prepareInAppArea` をオーバーライドしてユーザー インターフェイス コントロールの 1 つに少なくとも `actionNotification` がマッピングされるようにした。

> [!NOTE]
> `handleNotification` で例外がスローされると、コンテンツが削除され `dropContent` が呼び出されます。 これは作成される統計情報のレポートに掲載され、次のキャンペーンが処理できるようになります。
> 
> 

### <a name="announcements-and-polls"></a>お知らせとポーリング
#### <a name="layouts"></a>レイアウト
`engagement_text_announcement.xml`、`engagement_web_announcement.xml`、および `engagement_poll.xml` ファイルを修正して、テキスト アナウンス、Web アナウンス、および投票をカスタマイズできます。

これらのファイルは、タイトル領域とボタン領域で 2 つのレイアウトを共有します。 タイトルのレイアウトは `engagement_content_title.xml` で、バックグラウンドにそれにちなんだ描画可能なファイルが使用されます。 アクション ボタンや終了ボタンのレイアウトは `engagement_button_bar.xml` で、バックグラウンドにそれにちなんだ描画可能なファイルが使用されます。

投票では、質問に `engagement_question.xml` レイアウト ファイル、選択肢に `engagement_choice.xml` ファイルを複数回使用することで、質問のレイアウトとその選択肢が動的に拡張されます。

#### <a name="categories"></a>カテゴリ
##### <a name="alternate-layouts"></a>代替レイアウト
通知と同様に、キャンペーンのカテゴリを使用して、お知らせとポーリングの代替レイアウトを設定できます。

たとえば、テキスト アナウンスのカテゴリを作成するには、`AndroidManifest.xml` ファイルで `EngagementTextAnnouncementActivity` を拡張してそれを参照します。

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

インテント フィルターのカテゴリは、既定のアナウンス アクティビティと差別化するために使用されます。

Reach SDK はインテント システムを使用して特定のカテゴリに対する正しいアクティビティを解決し、解決されない場合は既定のカテゴリにフォールバックします。

その後、`MyCustomTextAnnouncementActivity` を実装する必要があります。レイアウトのみを変更する (同じビュー識別子は維持する) 場合、次の例のようにクラスを定義します。

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

テキスト アナウンスの既定のカテゴリを置き換えるには、`android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` を独自の実装に置き換えます。

Web アナウンスと投票についても同様にカスタマイズできます。

Web アナウンスの場合、`AndroidManifest.xml` で次の例のように `EngagementWebAnnouncementActivity` を拡張してアクティビティを宣言します。

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

投票の場合、`AndroidManifest.xml` で次の例のように `EngagementPollActivity` を拡張してアクティビティを宣言します。

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>一から実装する
Reach SDK に用意された `Engagement*Activity` を拡張することなく、アナウンス (および投票) のアクティビティにカテゴリを実装できます。 これは、標準のレイアウトと異なるビューを使用するレイアウトを定義する場合などに便利です。

高度な通知カスタマイズと同様に、標準実装のソース コードをご確認ください。

次の点に注意してください。Reach は (インテント フィルターに対応する) 特定のインテントと、コンテンツ識別子である追加パラメーターを使用してアクティビティを起動します。

Web サイトのキャンペーンを作成する際に指定したフィールドが含まれるコンテンツ オブジェクトを取得するには、次のようにします。

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

統計情報については、`onResume` イベントに表示されるコンテンツのレポートを作成してください。

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

その後、アクティビティがバックグラウンドに移行する前に、コンテンツ オブジェクトで `actionContent(this)` または `exitContent(this)` を呼び出すことを忘れないようにします。

`actionContent` も `exitContent` も呼び出さないと、統計情報は送信されず (キャンペーンの解析が実行されず)、さらにアプリケーション プロセスを再開するまで次のキャンペーンが通知されません。

方向などの構成の変更はコードを複雑にし、アクティビティがバックグラウンドに移行するかどうかを判断するのが難しくなる場合があります。標準実装ではユーザーがアクティビティを終了する (`HOME` または `BACK` を押す) とコンテンツが終了したと見なされますが、方向が変わっている場合はこの限りではありません。

実装について注目する部分は次のとおりです。

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

`actionContent(this)` を呼び出してアクティビティを完了すると、`exitContent(this)` が安全に呼び出されることがわかります。

[こちら]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html

