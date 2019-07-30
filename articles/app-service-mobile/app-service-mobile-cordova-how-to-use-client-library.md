---
title: Azure Mobile Apps 向け Apache Cordova プラグインの使用方法
description: Azure Mobile Apps 向け Apache Cordova プラグインの使用方法
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 327cb3a3667c63454549ec694790769c9ea1fd58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446417"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Azure Mobile Apps 向け Apache Cordova クライアント ライブラリの使用方法
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-how-to-use-client-library) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このガイドでは、最新の [Azure Mobile Apps 向け Apache Cordova プラグイン]を使用して一般的なシナリオを実行する方法について説明します。 Azure Mobile Apps を初めて使用する場合は、まず、「 [Apache Cordova アプリの作成] 」を参照して、バックエンドの作成、テーブルの作成、構築済みの Apache Cordova プロジェクトのダウンロードを行ってください。 このガイドでは、クライアント側の Apache Cordova プラグインに重点を置いています。

## <a name="supported-platforms"></a>サポートされるプラットフォーム
この SDK は、iOS、Android、Windows の各デバイスで Apache Cordova v6.0.0 以降をサポートしています。  プラットフォームのサポートは次のとおりです。

* Android API 19 ～ 24 (KitKat から Nougat)
* iOS バージョン 8.0 以降
* Windows Phone 8.1
* ユニバーサル Windows プラットフォーム

## <a name="Setup"></a>セットアップと前提条件
このガイドでは、バックエンドとテーブルを作成済みであることを前提としています。 このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。 さらに、Apache Cordova プラグインがコードに追加済みであることを前提とします。  まだこれらの操作を行っていない場合は、次のコマンド ラインで Apache Cordova プラグインをプロジェクトに追加します。

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Apache Cordova アプリを初めて作成する場合は、 [こちら]のドキュメントを参照してください。

## <a name="ionic"></a>Ionic v2 アプリのセットアップ

Ionic v2 プロジェクトを適切に構成するには、最初に基本的なアプリを作成してから、Cordova プラグインを追加します。

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

次の行を `app.component.ts` に追加してクライアント オブジェクトを作成します。

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

これで、ブラウザーでプロジェクトを構築して実行できます。

```
ionic platform add browser
ionic run browser
```

Azure Mobile Apps の Cordova プラグインは、Ionic v1 アプリと Ionic v2 アプリの両方をサポートしています。  Ionic v2 アプリの場合のみ、追加で `WindowsAzure` オブジェクトの宣言が必要です。

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>方法:ユーザーを認証する
Azure App Service は、Facebook、Google、Microsoft アカウント、Twitter などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートしています。 テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。 さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。 詳細については、チュートリアル「 [モバイル サービスでの認証の使用] 」を参照してください。

Apache Cordova アプリで認証を使用する場合は、次の Cordova プラグインが使用できる状態になければなりません。

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

サーバー フローとクライアント フローの 2 つの認証フローがサポートされます。  サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。 クライアント フローでは、プロバイダー固有およびデバイス固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>方法:外部リダイレクト URL 用に Mobile App Service を構成する方法
いくつかの種類の Apache Cordova アプリケーションでは、ループバック機能を使用して OAuth UI フローを処理します。  既定では認証サービスで認識されるのはサービスの利用方法だけであるため、localhost 上の OAuth UI フローによって問題が発生します。  問題のある OAuth UI フローの例は次のとおりです。

* Ripple エミュレーター
* Ionic による Live Reload
* モバイル バックエンドのローカルでの実行
* 認証を提供するものとは別の Azure App Service でのモバイル バックエンドの実行

ローカル設定を構成に追加するには、以下の手順に従います。

1. [Azure Portal]
2. **[すべてのリソース]** または **[App Services]** を選択し、モバイル アプリの名前をクリックします。
3. **[ツール]**
4. [監視] メニューの **[リソース エクスプローラー]** をクリックし、 **[実行]** をクリックします。  新しいウィンドウまたはタブが開きます。
5. 左側のナビゲーションで、サイトの **[config]** ノード、 **[authsettings]** ノードの順に展開します。
6. **[編集]**
7. "allowedExternalRedirectUrls" 要素を探します。  この要素は、null または値の配列に設定できます。  値を次の値に変更します。

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    URL をご使用のサービスの URL に置き換えます。  例には、`http://localhost:3000` (Node.js サンプル サービス用)、または `http://localhost:4400` (Ripple サービス用) が含まれています。  ただし、これらの URL は例にすぎません。例に示されているサービスの状況など、状況が異なる場合があります。
8. 画面の右上隅にある **[読み取り/書き込み]** ボタンをクリックします。
9. 緑色の **[PUT]** ボタンをクリックします。

この時点で設定が保存されます。  設定の保存が完了するまで、ブラウザー ウィンドウを閉じないでください。
また、App Service の CORS 設定に、これらのループバック URL を追加します。

1. [Azure Portal]
2. **[すべてのリソース]** または **[App Services]** を選択し、モバイル アプリの名前をクリックします。
3. [設定] ブレードが自動的に開きます。  開かない場合は、 **[すべての設定]** をクリックします。
4. API メニューの **[CORS]** をクリックします。
5. 表示されたボックスに追加する URL を入力して、Enter キーを押します。
6. 必要に応じて、さらに URL を入力します。
7. **[保存]** をクリックして設定を保存します。

新しい設定が有効になるまで約 10 ～ 15 秒かかります。

## <a name="register-for-push"></a>方法:プッシュ通知に登録する
プッシュ通知を処理するには、 [phonegap-plugin-push] をインストールします。  このプラグインは、コマンド ラインで `cordova plugin add` コマンドを使用するか、Visual Studio 内で Git プラグイン インストーラーを使用することで簡単に追加できます。  Apache Cordova アプリの次のコードによって、デバイスがプッシュ通知に登録されます。

```javascript
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

プッシュ通知は、Notification Hubs SDK を使用してサーバーから送信します。  プッシュ通知をクライアントから直接送信しないでください。 この操作は、Notification Hubs または PNS に対するサービス拒否攻撃のトリガーに使用される可能性があります。  このような攻撃が発生すると、PNS はトラフィックを禁止する可能性があります。

## <a name="more-information"></a>詳細情報

API の詳細については、[API に関するドキュメント](https://azure.github.io/azure-mobile-apps-js-client/)を参照してください。

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Apache Cordova アプリの作成]: app-service-mobile-cordova-get-started.md
[モバイル サービスでの認証の使用]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Mobile Apps 向け Apache Cordova プラグイン]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[こちら]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
