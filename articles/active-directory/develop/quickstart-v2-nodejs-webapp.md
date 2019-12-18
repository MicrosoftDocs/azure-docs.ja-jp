---
title: Node.js Web アプリに OIDC サインインを追加する - Microsoft ID プラットフォーム | Azure
description: OpenID Connect を使用して Node.js Web アプリケーションに認証を実装する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2869333fe0e9103724b9b1075270a5c3301c472
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968019"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>クイック スタート:OpenID Connect を使用したサインインを Node.js Web アプリに追加する

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

このクイックスタートでは、Node.js と Express を使用して構築された Web アプリケーションで OpenID Connect 認証を設定する方法について説明します。 サンプルは、任意のプラットフォームで動作するように設計されています。

## <a name="prerequisites"></a>前提条件

このサンプルを実行するには、次のものが必要になります。

* http://nodejs.org/ からの Node.js のインストール

* [Microsoft アカウント](https://www.outlook.com)または [Office 365 Developer Program](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>アプリケーションの登録 
1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
1. アカウントが複数の Azure AD テナントに存在する場合:
    - ページの右上隅にあるメニューからプロファイルを選択し、 **[ディレクトリの切り替え]** を選択します。
    - アプリケーションを作成する Azure AD テナントにセッションを変更します。

1. [[Azure Active Directory] > [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) に移動してアプリを登録します。

1. **[新規登録]** を選択します。

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリの登録情報を入力します。
    - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすい名前を入力します。 例: MyWebApp
    - **[サポートされているアカウントの種類]** セクションで、 **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** を選択します。

    複数のリダイレクト URI がある場合は、アプリが正常に作成された後、 **[認証]** タブからこれらを追加する必要があります。

1. **[登録]** を選択して、アプリを作成します。

1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。 この値は、このプロジェクトで後からアプリケーションを構成するために必要になります。

1. アプリのページの一覧から **[認証]** を選択します。
    - **[リダイレクト URI]** セクションで、コンボボックスの **[Web]** を選択し、リダイレクト URI の「`http://localhost:3000/auth/openid/return`」を入力します。
    - **[詳細設定]** セクションの **[ログアウト URL]** を「`http://localhost:3000`」に設定します。
    - このサンプルでは、ユーザーのサインインに[暗黙的な許可のフロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow)が有効になっている必要があるため、 **[詳細設定] > [暗黙の付与]** セクションで、 **[ID トークン]** をチェックします。

1. **[保存]** を選択します。

1. **[証明書とシークレット]** ページの **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** を選択します。
    - キーの説明 (インスタンス アプリ シークレット用) を入力します。
    - キーの有効期間として **[1 年]、[2 年]** 、または **[有効期限なし]** を選択します。
    - **[追加]** ボタンをクリックすると、キーの値が表示されます。 キー値をコピーして安全な場所に保存します。

    このキーは、後でアプリケーションを構成するために必要になります。 このキー値は、再度表示することも、その他の方法で取得することもできないため、Azure Portal から参照できるようになったらすぐに記録してください。

## <a name="download-the-sample-application-and-modules"></a>サンプル アプリケーションとモジュールのダウンロード

次に、サンプル リポジトリを複製し、NPM モジュールをインストールします。

シェルまたはコマンド ラインから:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

or

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

プロジェクト ルート ディレクトリから、次のコマンドを実行します。

`$ npm install`  

## <a name="configure-the-application"></a>アプリケーションの構成

指示に従って、config.js の `exports.creds` にパラメーターを指定します。

* `exports.identityMetadata` の `<tenant_name>` を、\*.onmicrosoft.com という形式の Azure AD テナント名で更新します。
* アプリの登録で書き留めておいたアプリケーション ID で `exports.clientID` を更新します。
* アプリの登録で書き留めておいたアプリケーション シークレットで `exports.clientSecret` を更新します。
* アプリの登録で書き留めておいたリダイレクト URI で `exports.redirectUrl` を更新します。

**運用アプリのオプション構成:**

* 別の `post_logout_redirect_uri` を使用する場合、config.js の `exports.destroySessionUrl` を更新します。

* [mongoDB](https://www.mongodb.com) または他の[互換性のあるセッション ストア](https://github.com/expressjs/session#compatible-session-stores)を使用する場合、config.js の `exports.useMongoDBSessionStore` を true に設定します。
このサンプルの既定のセッション ストアは `express-session` です。 既定のセッション ストアは、運用環境には適していません。

* mongoDB セッション ストアと別のデータベース URI を使用する場合は、`exports.databaseUri` を更新します。

* `exports.mongoDBSessionMaxAge` を更新します。 ここでは、mongoDB でセッションを保持する期間を指定できます。 単位は秒です。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

mongoDB サービスを開始します。 このアプリで mongoDB セッション ストアを使用している場合は、[mongoDB をインストール](http://www.mongodb.org/)して、まずサービスを開始する必要があります。 既定のセッション ストアを使用している場合は、この手順をスキップできます。

コマンド ラインで次のコマンドを使用して、アプリを実行します。

```
$ node app.js
```

**サーバーの出力を理解しづらい場合:** このサンプルのログ記録には `bunyan` を使用します。 bunyan をインストールして上記のようにサーバーを実行しながら bunyan バイナリでパイプする場合を除き、コンソールはあまり意味を持ちません。

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>以上で終わりです。

`http://localhost:3000` でサーバーが正常に実行されます。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次の手順
Microsoft ID プラットフォームでサポートされている Web アプリのシナリオの詳細については、次を参照してください。
> [!div class="nextstepaction"]
> [ユーザーをサインインさせる Web アプリのシナリオ](scenario-web-app-sign-user-overview.md)
