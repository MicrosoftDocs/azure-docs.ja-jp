---
title: "Azure Mobile Engagement Android SDK の統合"
description: "Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 35bd92e52b7a02f58620a03156902f9f91be57ae
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-integrate-engagement-on-android"></a>Engagement を Android に統合する方法
> [!div class="op_single_selector"]
> * [Windows ユニバーサル](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

この手順では、Engagement の分析機能と監視機能を Android アプリでアクティブ化するための最も簡単な方法について説明します。

> [!IMPORTANT]
> Android SDK の最小の API レベルは 10 以降 (Android 2.3.3 以降) である必要があります。
> 
> 

ユーザー、セッション、アクティビティ、クラッシュ、および技術に関するすべての統計をコンピューティングするために必要なログ レポートをアクティブ化するには、以下の手順で十分です。 イベント、エラー、ジョブなどの他の統計情報をコンピューティングするのに必要なログのレポートについては、これらの統計がアプリケーションに依存しているので、エンゲージメント API を使用して手動で実行する必要があります ( [Android で高度な Mobile Engagement タグ付け API を使用する方法を参照](mobile-engagement-android-use-engagement-api.md) )。

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Android プロジェクトに Engagement SDK とサービスを埋め込む
Android SDK を[こちら](https://aka.ms/vq9mfn)からダウンロードします。`mobile-engagement-VERSION.jar` を選択して、Andorid プロジェクトの `libs` フォルダーに配置します (libs フォルダーが存在しない場合は作成します)。

> [!IMPORTANT]
> アプリケーション パッケージを ProGuard でビルドする場合は、いくつかのクラスを保持する必要があります。 次の構成スニペットを使うことができます。
> 
> -keep public class * extends android.os.IInterface -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

ランチャー アクティビティ内で次のメソッドを呼び出すことで、Engagement の接続文字列を指定します。

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

* 次の Android のアクセス権限が不足している場合は (`<application>` タグの前に) 追加します。
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* 次のセクションを (`<application>` タグと `</application>` タグの間に) 追加します。
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* `<Your application name>` を自分のアプリケーションの名前に変更します。

> [!TIP]
> `android:label` 属性を使って、エンド ユーザーの電話の "実行中のサービス" 画面に表示される Engagement サービスの名前を選択することができます。 この属性には `"<Your application name>Service"` を設定することをお勧めします (例: `"AcmeFunGameService"`)。
> 
> 

`android:process` 属性を指定すると、Engagement サービスが独自のプロセスで実行されることが保証されます (Engagement をアプリケーションと同じプロセス内で実行すると、メイン/UI スレッドの反応が遅くなる可能性があります)。

> [!NOTE]
> `Application.onCreate()` に配置したすべてのコードとその他のアプリケーション コールバックは、Engagement サービスを含むすべてのアプリケーションのプロセスで実行されます。 望ましくない副作用が発生する可能性があります (Engagement のプロセス内の不要なメモリとスレッドの割り当て、重複するブロードキャストの受信側またはサービスなど)。
> 
> 

`Application.onCreate()` をオーバーライドする場合は、次のコード スニペットを `Application.onCreate()` 関数の先頭に追加することをお勧めします。

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

同じことを `Application.onTerminate()`、`Application.onLowMemory()`、`Application.onConfigurationChanged(...)` に対しても行うことができます。

`EngagementApplication` を `Application` の代わりに拡張することもできます。コールバック `Application.onCreate()` は、プロセス チェックを実行し、現在のプロセスが Engagement サービスをホストするプロセスでない場合のみ `Application.onApplicationProcessCreate()` を呼び出します。

## <a name="basic-reporting"></a>基本的なレポート
### <a name="recommended-method-overload-your-activity-classes"></a>推奨される方法: `Activity` クラスをオーバーロードします
ユーザー、セッション、アクティビティ、クラッシュ、および技術に関する統計をコンピューティングするために Engagement が必要とするすべてのログ レポートをアクティブ化するには、すべての `*Activity` サブ クラスが対応する `Engagement*Activity` クラスから継承されるようにする必要があります (例: レガシー アクティビティが `ListActivity` を拡張している場合は、`EngagementListActivity` も拡張します)。

**エンゲージメントを使用しない場合:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**エンゲージメントを使用する場合:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> `EngagementListActivity` または `EngagementExpandableListActivity` を使う場合は、`requestWindowFeature(...);` に対するすべての呼び出しが `super.onCreate(...);` に対する呼び出しの前に行われることを確認します。それ以外の場合、クラッシュが発生します。
> 
> 

これらのクラスは `src` フォルダーで見つけることができ、プロジェクトにコピーできます。 クラスは **JavaDoc**にも用意されています。

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>別の方法: `startActivity()` と `endActivity()` を手動で呼び出す
`Activity` クラスをオーバーロードできない、またはそうしたくない場合は、代わりに `EngagementAgent` のメソッドを直接呼び出すことによって、アクティビティの開始と終了を実行できます。

> [!IMPORTANT]
> Android SDK は、アプリケーションが閉じられる場合でも `endActivity()` メソッドを呼び出すことはありません (Android では、アプリケーションは実際には閉じられることはありません)。 このため、"*すべて*" のアクティビティの `onResume` コールバック内で `startActivity()` メソッドを呼び出し、"*すべて* のアクティビティの `onPause()` コールバック内で `endActivity()` メソッドを呼び出すことを "*強く*" お勧めします。 これは、セッションがリークしないことを保証する唯一の方法です。 セッションがリークした場合、(セッションが保留中である限り、サービスが接続されたままになるため)、Engagement サービスが Engagement バックエンドから切断されることはありません。
> 
> 

たとえば次のようになります。

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

この例は、`EngagementActivity` クラスとそのバリアントによく似ています。ソース コードは `src` フォルダーに提供されています。

## <a name="test"></a>テスト
エミュレーターまたはデバイスでモバイル アプリを実行し、このアプリによって [監視] タブにセッションが登録されることを確認して、統合を検証してください。

この後のセクションはオプションです。

## <a name="location-reporting"></a>場所レポート
場所を報告する場合は、数行の構成行を (`<application>` タグと `</application>` タグの間に) 追加する必要があります。

### <a name="lazy-area-location-reporting"></a>遅延エリアの場所レポート
大まかなエリアの位置報告では、デバイスに関連付けられた国、リージョン、地域をレポートできます。 このタイプの場所レポートでは、セル ID または WIFI に基づいたネットワークの場所のみを使用します。 デバイス エリアがセッションごとに最大&1; 回レポートされます。 GPS を使用しないため、このタイプの場所レポートでは、まったくとは言わないまでも、電力消費量にほとんど影響がありません。

報告されたエリアを基に、ユーザー、セッション、イベント、エラーに関する地理的な統計をコンピューティングします。 また、リーチ キャンペーンの条件としても使用されます。

大まかなエリアの位置報告を有効にするには、この手順の前の構成を利用できます。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

さらに、次のアクセス権限が不足している場合は追加する必要があります。

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

あるいは、アプリケーションで ``ACCESS_FINE_LOCATION`` を使用している場合、引き続きそれを利用できます。

### <a name="real-time-location-reporting"></a>リアル タイム場所レポート
リアル タイム場所レポートでは、デバイスに関連付けられた緯度と経度をレポートできます。 既定では、このタイプの場所レポートでは (セル ID または WIFI に基づいた) ネットワークの場所のみが使用されます。レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。

リアル タイム場所レポートは、統計のコンピューティングに使用することは *できません* 。 その唯一の目的は、リーチ キャンペーンでリアルタイム ジオフェンシング \<Reach-Audience-geofencing\> 基準の利用を可能にすることです。

リアルタイム場所レポートを有効にするには、この手順の前の構成を利用できます。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

さらに、次のアクセス権限が不足している場合は追加する必要があります。

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

あるいは、アプリケーションで ``ACCESS_FINE_LOCATION`` を使用している場合、引き続きそれを利用できます。

#### <a name="gps-based-reporting"></a>GPS ベースのレポート
既定では、リアル タイム場所レポートでは、ネットワーク ベースの場所のみを使用します。 GPS ベースの場所 (正確性が格段に優れています) を使用できるようにするには、構成オブジェクトを使用します。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

さらに、次のアクセス権限が不足している場合は追加する必要があります。

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>背景レポート
既定では、リアル タイム場所レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。 バックグラウンドでのレポートも有効にするには、構成オブジェクトを使用します。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> アプリケーションを背景で実行した場合、GPS を有効にしても、ネットワーク ベースの場所のみがレポートされます。
> 
> 

バックグラウンド ロケーション レポートは、ユーザーがデバイスを再起動すると停止します。以下を追加することで、起動時に自動的に再開されるように設定できます。

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

さらに、次のアクセス権限が不足している場合は追加する必要があります。

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M アクセス許可
Android M より、一部のアクセス許可が実行時に管理され、ユーザーの承認を必要とします。

Android API レベル 23 が対象の場合、新しいアプリのインストールでは既定で実行時のアクセス許可がオフになります。 それ以外の場合、既定でオンになります。

デバイスの設定メニューからアクセス許可の有効/無効を設定できます。 システム メニューでアクセス許可をオフにした場合、アプリケーションのバックグラウンド プロセスが停止します。これはシステム動作であり、バックグラウンドでプッシュを受信する機能には影響を与えません。

Mobile Engagement の場合、次のアクセス許可で実行時に承認が必要になります。

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (この場合、Android API レベル 23 を対象とするときのみ)

外部ストレージはリーチの大きい画像機能でのみ使用されます。 このアクセス許可をユーザーに求めることが混乱を招くようであれば、Mobile Engagement のためにのみ使用した場合、このアクセス許可を削除できます。ただし、大きい画像機能を無効にする必要があります。

場所機能の場合、標準のシステム ダイアログを利用してユーザーにアクセス許可を要求する必要があります。 ユーザーが承認した場合、その変更をリアルタイムで考慮するように ``EngagementAgent`` に通知する必要があります (認識されない場合、ユーザーが次回アプリケーションを起動したときに変更が処理されます)。

ここにコード サンプルがあります。これはアクセス許可を要求し、``EngagementAgent`` が肯定された場合、結果を転送するアプリケーション アクティビティで使用されます。

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>詳細な報告
オプションとして、アプリケーション固有のイベント、エラー、ジョブをレポートする必要がある場合は、 `EngagementAgent` クラスのメソッドを通じて Engagement API を使用します。 このクラスのオブジェクトは、 `EngagementAgent.getInstance()` 静的メソッドを呼び出すことで取得できます。

Engagement API では、Engagement の高度な機能をすべて利用できます。詳細は「Engagement API を Android で使用する方法」にあります(`EngagementAgent` クラスの技術文書にもあります)。

## <a name="advanced-configuration-in-androidmanifestxml"></a>高度な構成 (AndroidManifest.xml 内)
### <a name="wake-locks"></a>ウェイク ロック
WIFI を使っているとき、または画面がオフのときにに統計がリアルタイムで送信されることを保証する場合は、次のオプションのアクセス権限を追加します。

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>クラッシュ レポート
クラッシュ レポートを無効にする場合は、以下を (`<application>` タグと `</application>` タグの間) に追加します。

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>バーストのしきい値
既定では、エンゲージメント サービスはログをリアルタイムで報告します。 アプリケーションがログを送信する回数が非常に多い場合は、ログをバッファーに格納して、一定時間ごとにまとめて報告することをお勧めします (これは "バースト モード" と呼ばれます) 。 これを行うには、以下を (`<application>` タグと `</application>` タグの間に) 追加します。

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

バースト モードではわずかにバッテリーの寿命が延びますが、エンゲージメントの監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。 バーストのしきい値は、30000 (30 秒) よりも長くしないことをお勧めします。

### <a name="session-timeout"></a>セッションのタイムアウト
既定では、セッションは最後のアクティビティが終了した後、10 秒経過した時点で終了します (アクティビティの終了は、通常は、[ホーム] または [戻る] キーを押す、電話をアイドル状態に設定する、または他のアプリケーションに移動することで発生します)。 これは、ユーザーがアプリケーションを終了した後、非常に短時間で戻ってくるたびにセッションが分割されることを避けるためです (このユーザーの行動は、画像の選択や通知の確認などを行うときに発生する可能性があります)。 このパラメーターを変更することができます。 これを行うには、以下を (`<application>` タグと `</application>` タグの間に) 追加します。

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>ログ レポートの無効化
### <a name="using-a-method-call"></a>メソッド呼び出しを使用した場合
Engagement でログの送信を停止したい場合は、以下を呼び出します。

            EngagementAgent.getInstance(context).setEnabled(false);

この呼び出しは永続的であり、共有設定ファイルを使います。

この関数を呼び出したときに Engagement がアクティブの場合、サービスが停止するまで 1 分ほどかかることがあります。 ただし、次にアプリケーションが起動したときにサービスが起動することはありません。

ログ レポートは、同じ関数を `true`でもう一度呼び出すことによって有効にすることができます。

### <a name="integration-in-your-own-preferenceactivity"></a>独自の `PreferenceActivity`
上記の関数を呼び出す代わりに、その設定を既存の `PreferenceActivity` の中に直接統合することもできます。

`AndroidManifest.xml` ファイル内の設定ファイルを (目的のモードで) `application meta-data` と共に使うように Engagement を構成できます。

* `engagement:agent:settings:name` キーを使って、共有設定ファイルの名前を定義します。
* `engagement:agent:settings:mode` キーを使って、共有設定ファイルのモードを定義します。`PreferenceActivity` と同じモードを使う必要があります。 モードは数値として渡す必要があります。コード内で定数フラグの組み合わせを使っている場合は、合計値を確認します。

Engagement では、この設定を管理するために設定ファイル内で常に `engagement:key` ブール キーを使います。

次の `AndroidManifest.xml` の例は、既定値を示しています。

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

次のような `CheckBoxPreference` を設定レイアウトに追加できます。

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094

