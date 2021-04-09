---
title: 'クイックスタート: ユーザー サインインを Node.js Web アプリに追加する | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、OpenID Connect を使用して Node.js Web アプリケーションに認証を実装する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: f7f14b91dc69eeba4ac06f6608f6151634dc38d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103500"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>クイック スタート:OpenID Connect を使用したサインインを Node.js Web アプリに追加する

このクイックスタートでは、Node.js と Express を使用して構築された Web アプリケーションで OpenID Connect 認証を設定する方法を示すコード サンプルをダウンロードして実行します。 サンプルは、任意のプラットフォームで動作するように設計されています。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/en/download/)。

## <a name="register-your-application"></a>アプリケーションの登録

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの **名前** を入力します (例: `MyWebApp`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
1. **[サポートされているアカウントの種類]** セクションで、 **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** を選択します。

    複数のリダイレクト URI がある場合は、アプリが正常に作成された後、 **[認証]** タブからこれらを追加してください。

1. **[登録]** を選択して、アプリを作成します。
1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。 この値は、このプロジェクトで後からアプリケーションを構成するために必要になります。
1. **[管理]** で、 **[認証]** を選択します。
1. **[プラットフォームの追加]**  >  **[Web]** の順に選択します。 
1. **[リダイレクト URI]** セクションで、「`http://localhost:3000/auth/openid/return`」と入力します。
1. **[Front-channel logout URL]\(フロントチャネル ログアウト URL\)** に「`https://localhost:3000`」を入力します。
1. このサンプルでは、ユーザーをサインインさせるために [暗黙的な許可のフロー](./v2-oauth2-implicit-grant-flow.md)が有効になっている必要があるため、 **[暗黙的な許可およびハイブリッド フロー]** セクションで **[ID トークン]** を選択します。
1. **[構成]** をクリックします。
1. **[管理]** で、 **[Certificates & secrets]\(証明書およびシークレット\)**  >  **[新しいクライアント シークレット]** の順に選択します。
1. キーの説明 (インスタンス アプリ シークレット用) を入力します。
1. キーの有効期間として **[1 年]、[2 年]** 、または **[有効期限なし]** を選択します。
1. **[追加]** を選択します。 キー値が表示されます。 キー値をコピーし、後で使用できるように安全な場所に保存します。


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

## <a name="next-steps"></a>次のステップ
Microsoft ID プラットフォームでサポートされている Web アプリのシナリオの詳細については、次を参照してください。
> [!div class="nextstepaction"]
> [ユーザーをサインインさせる Web アプリのシナリオ](scenario-web-app-sign-user-overview.md)