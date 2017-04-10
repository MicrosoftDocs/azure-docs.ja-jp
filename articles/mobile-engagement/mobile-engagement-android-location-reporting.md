---
title: "Azure Mobile Engagement Android SDK の位置報告"
description: "Azure Mobile Engagement Android SDK の位置報告を構成する方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 777d5719cce505b55dfb61c91dcac7e713b077a9


---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Azure Mobile Engagement Android SDK の位置報告
> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

このトピックでは、Android アプリケーションの位置報告を実行する方法について説明します。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>場所レポート
場所を報告する場合は、数行の構成行を (`<application>` タグと `</application>` タグの間に) 追加する必要があります。

### <a name="lazy-area-location-reporting"></a>遅延エリアの場所レポート
大まかなエリアの位置報告では、デバイスに関連付けられた国、リージョン、地域をレポートできます。 このタイプの場所レポートでは、セル ID または WIFI に基づいたネットワークの場所のみを使用します。 デバイス エリアがセッションごとに最大 1 回レポートされます。 GPS を使用しないため、このタイプの位置報告では、電力消費量にほとんど影響がありません。

報告されたエリアを基に、ユーザー、セッション、イベント、エラーに関する地理的な統計をコンピューティングします。 また、リーチ キャンペーンの条件としても使用されます。

大まかなエリアの位置報告を有効にするには、この手順の前の構成を利用できます。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

場所のアクセス許可を指定する必要があります。 このコードは ``COARSE`` アクセス許可を使用します。

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

アプリで必要な場合は、代わりに ``ACCESS_FINE_LOCATION`` を使用できます。

### <a name="real-time-location-reporting"></a>リアルタイムの位置報告
リアルタイムの位置報告では、デバイスに関連付けられた緯度と経度をレポートできます。 既定では、このタイプの位置報告では、セル ID または WiFi に基づいたネットワークの場所のみを使用します。 このレポートは、アプリケーションを前景で実行した場合 (セッション中など) にのみアクティブになります。

リアルタイムの位置報告を統計のコンピューティングに使用することは *できません* 。 その唯一の目的は、リーチ キャンペーンでリアルタイム ジオフェンシング \<Reach-Audience-geofencing\> 基準の利用を可能にすることです。

リアルタイムの位置報告を有効にするには、ランチャー アクティビティの Engegement 接続文字列を設定した場所にコード行を追加します。 結果は次のようになります。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS ベースのレポート
既定では、リアルタイムの位置報告では、ネットワーク ベースの位置のみを使用します。 (格段に正確な) GPS ベースの位置を使用できるようにするには、構成オブジェクトを使用します。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

さらに、次のアクセス権限が不足している場合は追加する必要があります。

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>背景レポート
既定では、リアルタイムの位置報告は、アプリケーションを前景で実行した場合 (セッション中など) にのみアクティブになります。 バックグラウンドでのレポートも有効にするには、この構成オブジェクトを使用します。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> アプリケーションをバックグラウンドで実行した場合、GPS を有効にしても、ネットワーク ベースの位置のみが報告されます。
> 
> 

ユーザーがデバイスを再起動すると、バックグラウンドの位置報告は停止します。 ブート時にバックグラウンドの位置報告を自動的に再開するには、次のコードを追加します。

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

さらに、次のアクセス権限が不足している場合は追加する必要があります。

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M アクセス許可
Android M より、一部のアクセス許可が実行時に管理され、ユーザーの承認を必要とします。

Android API レベル 23 が対象の場合、新しいアプリのインストールでは既定で実行時のアクセス許可がオフになります。 それ以外の場合、既定でオンになります。

デバイスの設定メニューからアクセス許可の有効/無効を設定できます。 システム メニューでアクセス許可をオフにした場合、アプリケーションのバックグラウンド プロセスが停止します。これはシステム動作であり、バックグラウンドでプッシュを受信する機能には影響を与えません。

Mobile Engagement の場所レポートの場合、次のアクセス許可で実行時に承認が必要になります。

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

標準のシステム ダイアログを利用してユーザーにアクセス許可を要求します。 ユーザーが承認すると、その変更がリアルタイムに考慮されるように、 ``EngagementAgent`` に通知します。 そうしない場合、その変更は次回ユーザーがアプリケーションを起動したときに処理されます。

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }



<!--HONumber=Nov16_HO3-->


