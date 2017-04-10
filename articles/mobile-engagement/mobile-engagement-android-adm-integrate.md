---
title: "Azure Mobile Engagement Android SDK の統合"
description: "Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 928c3d85f98f0bd073a5aaadf0ae4495840c5da6
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-integrate-adm-with-engagement"></a>ADM を Engagement に統合する方法
> [!IMPORTANT]
> このガイドの手順を実行する前に、「Engagement を Android に統合する方法」のドキュメントの統合手順を実行する必要があります。
> 
> このドキュメントは、既に Reach モジュールを統合してあり、Amazon デバイスのプッシュを計画する場合にのみ役立ちます。 アプリケーションに Reach キャンペーンを統合するには、Engagement Reach を Android に統合する方法に関するドキュメントを先にお読みください。
> 
> 

## <a name="introduction"></a>はじめに
ADM を統合すると、Amazon Android デバイスを対象とした場合に、アプリケーションをプッシュできます。

SDK にプッシュされた ADM ペイロードのデータ オブジェクトには常に `azme` キーが含まれています。 そのため、アプリケーションで別の目的で ADM を使用する場合、そのキーに基づいてプッシュをフィルター処理できます。

> [!IMPORTANT]
> Amazon Device Messaging によってサポートされるのは Android 4.0.3 以降を実行している Amazon Kindle デバイスのみですが、このコードはその他のデバイスでも安全に統合することができます。
> 
> 

## <a name="sign-up-to-adm"></a>ADM にサインアップする
まだ実行してない場合は、ADM を Amazon アカウントで有効にする必要があります。

この手順の詳細は [<https://developer.amazon.com/sdk/adm/credentials.html>] にあります。

手順を完了すると、以下が取得されます。

* Engagement がデバイスにプッシュできるようにする OAuth 資格情報 (クライアント ID とクライアント シークレット)。
* アプリケーションに統合する必要がある API キー。

## <a name="sdk-integration"></a>SDK の統合
### <a name="managing-device-registrations"></a>デバイス登録の管理
各デバイスは ADM サーバーに登録コマンドを送信する必要があります。そうしないと、デバイスに接続できません。

[ADM クライアント ライブラリ]を既に使用し、[統合 ADM] が既にある場合、android-sdk-adm-receive に直接移動できます。

ADM をまだ統合してしない場合は、Engagement では簡単な方法でアプリケーション内でそれを有効にできます。

`AndroidManifest.xml` ファイルを編集します。

* Amazon 名前空間を追加します。ファイルは次のように始まります。
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* `<application/>` タグの中にこのセクションを追加します。
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* amazon タグを追加した後、プロジェクトのビルド ターゲットが Android 2.1 より前の場合はビルド エラーが発生することがあります。 **Android 2.1+** ビルド ターゲットを使用する必要があります (`minSdkVersion` は問題なく 4 に設定することができます)。
* [この手順]に従い、ADM API キーを資産として統合します。

その後、次のセクションの指示に従います。

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>登録 ID を Engagement Push サービスに伝達し、通知を受信する
デバイスの登録 ID を Engagement Push サービスに伝達し、通知を受信するには、次を `AndroidManifest.xml` ファイルの `<application/>` タグ内に追加します (Engagement のない ADM を使用している場合でも)。

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

`AndroidManifest.xml` に次のアクセス許可があることを確認します (`</application>` タグの前にあります)。

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Engagement OAuth 資格情報を与える
Engagement ポータルで OAuth Credentials (クライアント ID とクライアントシークレット) を送信します。

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[ADM クライアント ライブラリ]:https://developer.amazon.com/sdk/adm/setup.html
[統合 ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[この手順]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset

