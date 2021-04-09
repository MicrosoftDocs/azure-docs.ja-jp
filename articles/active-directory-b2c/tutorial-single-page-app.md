---
title: チュートリアル:シングルページ アプリで認証を行う
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Azure Active Directory B2C を使用して、JavaScript ベースのシングルページ アプリケーション (SPA) にユーザー ログインを提供する方法を学習します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96171230"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>チュートリアル:Azure AD B2C を使用してシングルページ アプリケーションで認証を有効にする

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) を使用してシングルページ アプリケーション (SPA) でユーザーをサインアップおよびサインインする方法を説明します。次のいずれかを使用します。
* [OAuth 2.0 認証コード フロー](./authorization-code-flow.md) ([MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) 使用)
* [OAuth 2.0 の暗黙的な許可のフロー](./implicit-flow-single-page-application.md) ([MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) 使用)

2 部構成のシリーズの最初の部分であるこのチュートリアルでは、次の作業を行います。

> [!div class="checklist"]
> * Azure AD B2C テナントに登録されているアプリケーションに応答 URL を追加する
> * GitHub からコード サンプルをダウンロードする
> * サンプル アプリケーションのコードをテナントで動作するように変更する
> * サインアップ/サインイン ユーザー フローを使用してサインアップする

このシリーズの[次のチュートリアル](tutorial-single-page-app-webapi.md)では、コード サンプルの Web API の部分を有効にします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を続ける前に、次の Azure AD B2C リソースを用意しておく必要があります。

* [Azure AD B2C テナント](tutorial-create-tenant.md)
* ご利用のテナントで[登録されているアプリケーション](tutorial-register-spa.md)
* ご利用のテナントで[作成したユーザー フロー](tutorial-create-user-flows.md)

さらに、ご利用のローカル開発環境には次のものが必要です。

* [Visual Studio Code](https://code.visualstudio.com/) または別のコード エディター
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>アプリケーションの更新

前提条件の一環として完了した [2 番目のチュートリアル](./tutorial-register-spa.md)で、Azure AD B2C にシングルページ アプリケーションを登録しました。 このチュートリアルのコード サンプルとの通信を可能にするには、アプリケーションの登録に応答 URL (リダイレクト URI とも呼ばれます) を追加します。

Azure AD B2C テナントでアプリケーションを更新するには、Microsoft の新しい統合 **アプリの登録** エクスペリエンスか以前の **アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](./app-registrations-training-guide.md)。

#### <a name="app-registrations-auth-code-flow"></a>[アプリの登録 (認証コード フロー)](#tab/app-reg-auth/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** 、 **[所有しているアプリケーション]** タブ、*spaapp1* アプリケーションの順に選択します。
1. **[シングルページ アプリケーション]** の下で、 **[URI の追加]** リンクを選択し、「`http://localhost:6420`」と入力します。
1. **[保存]** を選択します。
1. **[概要]** を選択します。
1. 単一ページの Web アプリケーションでコードを更新する場合は、後の手順で使用するために **アプリケーション (クライアント) ID** をメモしておきます。

#### <a name="app-registrations-implicit-flow"></a>[アプリの登録 (暗黙的なフロー)](#tab/app-reg-implicit/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** 、 **[所有しているアプリケーション]** タブ、*spaapp1* アプリケーションの順に選択します。
1. **[シングルページ アプリケーション]** の下で、 **[URI の追加]** リンクを選択し、「`http://localhost:6420`」と入力します。
1. **[暗黙の付与]** で、 **[アクセス トークン]** および **[ID トークン]** のチェック ボックスをオンにし (まだの場合)、 **[保存]** を選択します。
1. **[概要]** を選択します。
1. 単一ページの Web アプリケーションでコードを更新する場合は、後の手順で使用するために **アプリケーション (クライアント) ID** をメモしておきます。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション (レガシ)]** を選択し、*spaapp1* アプリケーションを選択します。
1. **[応答 URL]** に「`http://localhost:6420`」を追加します。
1. **[保存]** を選択します。
1. プロパティ ページで、**アプリケーション ID** をメモします。 このアプリ ID は、後の手順でシングル ページ Web アプリケーションのコードを更新する際に使用します。

* * *

## <a name="get-the-sample-code"></a>サンプル コードの取得

このチュートリアルでは、GitHub からダウンロードしたコード サンプルを B2C テナントで動作するように構成します。 このサンプルでは、シングルページ アプリケーションでユーザーのサインアップおよびサインイン、保護された Web API の呼び出しに Azure AD B2C を使用する方法が示されています (このシリーズの次のチュートリアルで、Web API を有効にします)。

* MSAL.js 2.x 認証コード フローのサンプル:

    [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip)するか、GitHub からサンプルを複製します。

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* MSAL.js 1.x の暗黙的なフローのサンプル:

    [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)するか、GitHub からサンプルを複製します。

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>サンプルを更新する

サンプルを入手したので、ご利用の Azure AD B2C テナント名と前の手順でメモしたアプリケーション ID を使用してコードを更新します。

#### <a name="auth-code-flow-sample"></a>[認証コード フローのサンプル](#tab/config-auth/)

1. *App* フォルダー内の *authConfig.js* ファイルを開きます。
1. `msalConfig` オブジェクトで `clientId` の割り当てを見つけ、前の手順で記録した **アプリケーション (クライアント) ID** に置き換えます。
1. `policies.js` ファイルを開きます。
1. `names` の下のエントリを見つけて、それらの割り当てを、前の手順で作成したユーザー フローの名前 (`B2C_1_signupsignin1`など) に置き換えます。
1. `authorities` の下のエントリを見つけて、それらの割り当てを、前の手順で作成した適切なユーザー フローの名前 (`https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`など) に置き換えます。
1. `authorityDomain` の割り当てを見つけ、`<your-tenant-name>.b2clogin.com` に置き換えます。
1. `apiConfig.js` ファイルを開きます。
1. `b2cScopes` の割り当てを見つけ、URL を、Web API 用に作成したスコープ URL (たとえば、`b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`) に置き換えます。
1. `webApi` の割り当てを見つけ、現在の URL を、手順 4. で Web API をデプロイした URL (たとえば、`webApi: http://localhost:5000/hello`) に置き換えます。

#### <a name="implicit-flow-sample"></a>[暗黙的なフローのサンプル](#tab/config-implicit/)

1. *JavaScriptSPA* フォルダー内の *authConfig.js* ファイルを開きます。
1. `msalConfig` オブジェクトで `clientId` の割り当てを見つけ、前の手順で記録した **アプリケーション (クライアント) ID** に置き換えます。
1. `policies.js` ファイルを開きます。
1. `names` の下のエントリを見つけて、それらの割り当てを、前の手順で作成したユーザー フローの名前 (`B2C_1_signupsignin1`など) に置き換えます。
1. `authorities` の下のエントリを見つけて、それらの割り当てを、前の手順で作成した適切なユーザー フローの名前 (`https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`など) に置き換えます。
1. `apiConfig.js` ファイルを開きます。
1. `b2cScopes` の割り当てを見つけ、URL を、Web API 用に作成したスコープ URL (たとえば、`b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`) に置き換えます。
1. `webApi` の割り当てを見つけ、現在の URL を、手順 4. で Web API をデプロイした URL (たとえば、`webApi: http://localhost:5000/hello`) に置き換えます。

* * *

結果のコードは次のようになります。

#### <a name="auth-code-flow-sample"></a>[認証コード フローのサンプル](#tab/review-auth/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[暗黙的なフローのサンプル](#tab/review-implicit/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>サンプルを実行する

1. コンソール ウィンドウを開き、サンプルを含むディレクトリに移動します。 

    - MSAL.js 2.x 認証コード フローのサンプルの場合:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - MSAL.js 1.x の暗黙的なフローのサンプルの場合: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. 次のコマンドを実行します。

    ```console
    npm install && npm update
    npm start
    ```

    コンソール ウィンドウには、ローカルで稼働中の Node.js サーバーのポート番号が表示されます。

    ```console
    Listening on port 6420...
    ```
1. `http://localhost:6420` を参照して、ローカル コンピューターで実行されている Web アプリケーションを表示します。

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="ローカルで実行されているシングルページ アプリケーションを示す Web ブラウザー":::

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

このサンプル アプリケーションでは、サインアップ、サインイン、パスワードのリセットがサポートされています。 このチュートリアルでは、メール アドレスを使用してサインアップします。

1. **[Sign In]\(サインイン\)** を選択して、前の手順で指定した *B2C_1_signupsignin1* ユーザー フローを開始します。
1. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、 **[Sign up now]\(今すぐサインアップ\)** リンクを選択します。
1. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザーのパスワードと、ユーザー フローで定義されている要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Azure AD B2C ユーザー フローによって表示されるサインアップ ページ":::

1. **[作成]** を選択して、Azure AD B2C ディレクトリにローカル アカウントを作成します。

**[Create]\(作成\)** を選択すると、サインインしているユーザーの名前がアプリケーションに表示されます。

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="ログイン ユーザーを表示しているシングルページ アプリケーションを示す Web ブラウザー":::

サインインをテストする場合は、 **[Sign Out]\(サインアウト\)** ボタンを選択してから、 **[Sign In]\(サインイン\)** を選択し、サインアップした時に入力したメール アドレスとパスワードでサインインします。

### <a name="what-about-calling-the-api"></a>API の呼び出しについて

サインイン後に **[Call API]\(API の呼び出し\)** ボタンを選択した場合は、API 呼び出しの結果ではなく、サインアップ/サインイン ユーザー フロー ページが表示されます。 "*お使いの*" Azure AD B2C テナントに登録されている Web API アプリケーションと通信するようにアプリケーションの API 部分をまだ構成していないため、これは予期されていることです。

この時点では、アプリケーションはデモ テナント (fabrikamb2c.onmicrosoft.com) に登録されている API とまだ通信しようとしています。そのテナントで認証されないため、サインアップ/サインイン ページが表示されます。

このシリーズの次のチュートリアルに進んで、保護された API を有効にしてください (「[次のステップ](#next-steps)」セクションを参照)。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure AD B2C テナントのユーザー フローと連携するようにシングルページ アプリケーションを構成し、サインアップとサインインの機能を提供しました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure AD B2C テナントに登録されているアプリケーションに応答 URL を追加しました
> * GitHub からコード サンプルをダウンロードしました
> * サンプル アプリケーションのコードをテナントで動作するように変更しました
> * サインアップ/サインイン ユーザー フローを使用してサインアップしました

それでは、このシリーズの次のチュートリアルに進んで、SPA から保護された Web API へのアクセスを許可します。

> [!div class="nextstepaction"]
> [チュートリアル:シングルページ アプリケーションから Web API へのアクセスを保護および許可する >](tutorial-single-page-app-webapi.md)