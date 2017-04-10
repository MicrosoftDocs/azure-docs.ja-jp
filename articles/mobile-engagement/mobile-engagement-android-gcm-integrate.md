---
title: "Azure Mobile Engagement Android SDK の統合"
description: "Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0282abbf44406cac89c13520bc2a4e375817ed1f


---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>GCM を Mobile Engagement に統合する方法
> [!IMPORTANT]
> このガイドの手順を実行する前に、「Engagement を Android に統合する方法」のドキュメントの統合手順を実行する必要があります。
> 
> このドキュメントは、既に Reach モジュールを統合してあり、Google Play デバイスのプッシュを計画する場合にのみ役立ちます。 アプリケーションに Reach キャンペーンを統合するには、Engagement Reach を Android に統合する方法に関するドキュメントを先にお読みください。
> 
> 

## <a name="introduction"></a>はじめに
GCM を統合すると、アプリケーションをプッシュできます。

SDK にプッシュされた GCM ペイロードのデータ オブジェクトには常に `azme` キーが含まれています。 そのため、アプリケーションで別の目的で GCM を使用する場合、そのキーに基づいてプッシュをフィルター処理できます。

> [!IMPORTANT]
> Android 2.2 以降を実行中のデバイスで、Google Play がインストールされ、Google バックグラウンド接続が有効になっているデバイスのみを GCM でプッシュできます。ただし、このコードはサポートされていないデバイスで安全に統合できます (インテントのみを使用します)。
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>API キーを使用して Google Cloud Messaging プロジェクトを作成する
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>SDK の統合
### <a name="managing-device-registrations"></a>デバイス登録の管理
各デバイスは Google サーバーに登録コマンドを送信する必要があります。そうしないと、デバイスに接続できません。

デバイスは、GCM 通知の登録を解除することもできます (アプリケーションをアンインストールすると、デバイスは自動的に登録解除されます)。

[Google Play SDK] を使用していない場合、または登録インテントをまだ自分で送信していない場合は、Engagement にデバイスの自動登録を実行させることができます。

これを有効にするには、次を `AndroidManifest.xml` ファイルの `<application/>` タグ内に追加します。

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>登録 ID を Engagement Push サービスに伝達し、通知を受信する
デバイスの登録 ID を Engagement Push サービスに伝達し、その通知を受信するためには、以下を `AndroidManifest.xml` ファイルの `<application/>` タグ内に追加します (これはデバイス登録を自分で管理する場合にも実行します)。

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

次のアクセス権限が `AndroidManifest.xml` 内に設定されていることを確認します (`</application>` タグの後ろ)。

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Mobile Engagement に GCM API キーへのアクセス権限を付与する
[このガイド](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) に従って、Mobile Engagement に GCM API キーへのアクセス権限を付与します。

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start



<!--HONumber=Nov16_HO3-->


