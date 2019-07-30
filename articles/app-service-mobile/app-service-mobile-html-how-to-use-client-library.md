---
title: Azure Mobile Apps 向け JavaScript SDK の使用方法
description: Azure Mobile Apps 向け JavaScript SDK の使用方法
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5aa2e326739a97ff3d518ec383f4cf14311ca74
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446334"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Azure Mobile Apps 向け JavaScript クライアント ライブラリの使用方法
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-html-how-to-use-client-library) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このガイドでは、最新の [Azure Mobile Apps 向け JavaScript SDK]を使用して一般的なシナリオを実行する方法について説明します。 Azure Mobile Apps を初めて使用する場合は、まず、「 [Apache Cordova アプリの作成] 」を参照して、バックエンドおよびテーブルの作成を行ってください。 このガイドでは、HTML/JavaScript Web アプリケーションでのモバイル バックエンドの使用に重点を置いています。

## <a name="supported-platforms"></a>サポートされるプラットフォーム
ブラウザーのサポートは、現在のバージョンおよび最新バージョンの主要ブラウザー (Google Chrome、Microsoft Edge、Microsoft Internet Explorer、Mozilla Firefox) に制限されています。  この SDK は、比較的新しいブラウザーで機能すると思われます。

パッケージは Universal JavaScript モジュールとして配布されるので、グローバル、AMD、CommonJS の各形式をサポートします。

## <a name="Setup"></a>セットアップと前提条件
このガイドでは、バックエンドとテーブルを作成済みであることを前提としています。 このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。

Azure Mobile Apps JavaScript SDK のインストールは、次の `npm` コマンドで行うことができます。

```
npm install azure-mobile-apps-client --save
```

ライブラリは、Browserify や Webpack などの CommonJS 環境では ES2015 モジュールとして使用できるほか、AMD ライブラリとしても使用できます。  例:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

CDN から直接ダウンロードして、SDK のビルド済みバージョンを使用することもできます。

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>方法:ユーザーを認証する
Azure App Service は、Facebook、Google、Microsoft アカウント、Twitter などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートしています。 テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。 さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。 詳細については、チュートリアル「 [モバイル サービスでの認証の使用] 」を参照してください。

サーバー フローとクライアント フローの 2 つの認証フローがサポートされます。  サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。 クライアント フローでは、プロバイダー固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>方法:外部リダイレクト URL 用に Mobile App Service を構成する方法
いくつかの種類の JavaScript アプリケーションでは、ループバック機能を使用して OAuth UI フローを処理します。  次のような機能があります。

* サービスをローカルで実行する。
* Ionic Framework でライブ リロードを使用する。
* 認証のために App Service にリダイレクトする。

既定では、App Service 認証は、モバイル アプリ バックエンドからのアクセスだけを許可するように構成されているため、ローカルで実行すると、問題が発生する可能性があります。 App Service 設定を変更して、サーバーがローカルで実行されているときに認証を有効にするには、次の手順を実行します。

1. [Azure Portal]
2. モバイル アプリ バックエンドに移動します。
3. **[開発ツール]** メニューの **[リソース エクスプローラー]** を選択します。
4. **[移動]** をクリックして、新しいタブまたはウィンドウでモバイル アプリ バックエンドのリソース エクスプローラーを開きます。
5. アプリの **config** ノード、 > **authsettings** ノードの順に展開します。
6. **[編集]** ボタンをクリックして、リソースの編集を有効にします。
7. **allowedExternalRedirectUrls** 要素を探します。この要素は null になっています。 配列に実際の URL を追加します。

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    配列内の URL をサービスの URL に置き換えます。この例では、ローカルの Node.js サンプル サービス用の `http://localhost:3000` を使用しています。 アプリケーションの構成に応じて、Ripple サービス用の `http://localhost:4400` や他の URL を使用することもできます。
8. ページの上部で **[読み取り/書き込み]** 、 **[PUT]** の順にクリックして、更新を保存します。

CORS のホワイトリスト設定にも、同じループバック URL を追加する必要があります。

1. [Azure Portal]に戻ります。
2. モバイル アプリ バックエンドに移動します。
3. **API** メニューの **[CORS]** をクリックします。
4. 空の **[許可される元のドメイン]** ボックスに各 URL を入力します。  新しいテキスト ボックスが作成されます。
5. **[保存]**

バックエンドの更新が済むと、アプリケーションで新しいループバック URL を使用できるようになります。

<!-- URLs. -->
[Apache Cordova アプリの作成]: app-service-mobile-cordova-get-started.md
[モバイル サービスでの認証の使用]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[Azure Mobile Apps 向け JavaScript SDK]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
