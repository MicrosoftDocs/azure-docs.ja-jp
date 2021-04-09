---
title: 'クイックスタート: JavaScript シングルページ アプリでユーザーをサインインさせる | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Microsoft ID プラットフォームによって発行されるアクセス トークンを必要とする API を、JavaScript アプリから呼び出す方法について説明します。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: cf5b24bb55f278d9d33916d2d54d3ee5a169c3e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224403"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>クイック スタート:JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する

このクイックスタートでは、JavaScript シングルページ アプリケーション (SPA) でユーザーをサインインし、Microsoft Graph を呼び出す方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、Microsoft Graph API または任意の Web API を呼び出すためのアクセス トークンを取得する方法も示します。

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (プロジェクト ファイルの編集に使用)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>クイック スタート アプリケーションを登録してダウンロードする
> クイックスタートのアプリケーションを起動するには、次のオプションのいずれかを使用します。
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1 (簡易): アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure portal のアプリの登録</a>クイックスタート エクスペリエンスに移動します。
> 1. アプリケーションの名前を入力します。
> 1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
> 1. **[登録]** を選択します。
> 1. 指示に従って新しいアプリケーションをダウンロードし、自動構成します。
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2 (手動): アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
>
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
> 1. **Azure Active Directory** を検索して選択します。
> 1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
> 1. アプリケーションの **[名前]** を入力します。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
> 1. **[登録]** を選択します。 後で使用するために、アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。
> 1. このクイック スタートでは、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。 **[管理]** で、 **[認証]** を選択します。
> 1. **[プラットフォーム構成]**  >  **[プラットフォームを追加]** を選択します。 **[Web]** を選択します。
> 1. **[リダイレクト URI]** の値を `http://localhost:3000/` に設定します。 
> 1. **[暗黙的な許可およびハイブリッド フロー]** で、 **[アクセス トークン]** と **[ID トークン]** を選択します。
> 1. **[構成]** をクリックします。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイックスタートのコード サンプルを動作させるには、**リダイレクト URI** (`http://localhost:3000/`) を追加し、 **[暗黙の付与]** を有効にします。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-javascript/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

> [!div renderon="docs"]
> Node.js を使用して Web サーバーでプロジェクトを実行するために、[コア プロジェクト ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)します。

> [!div renderon="portal" class="sxs-lookup"]
> Node.js を使用して Web サーバーでプロジェクトを実行する

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>手順 3:JavaScript アプリの構成
>
> *JavaScriptSPA* フォルダーで、*authConfig.js* を編集し、`msalConfig` の下にある `clientID`、`authority`、および `redirectUri` の値を設定します。
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> 各値の説明:
> - `Enter_the_Application_Id_Here` は、登録したアプリケーションの **アプリケーション (クライアント) ID** です。
>
>    **[アプリケーション (クライアント) ID]** の値を見つけるには、Azure portal でアプリの **[概要]** ページに移動します。
> - `Enter_the_Cloud_Instance_Id_Here` は、Azure クラウドのインスタンスです。 メイン (グローバル) Azure クラウドの場合は、単に「 `https://login.microsoftonline.com` 」と入力します。 **各国** のクラウド (中国など) の場合は、「[各国のクラウド](./authentication-national-cloud.md)」を参照してください。
> - `Enter_the_Tenant_info_here` には、次のオプションのいずれかが設定されます。
>    - アプリケーションで "*この組織のディレクトリ内のアカウントのみ*" がサポートされる場合は、この値を **テナント ID** または **テナント名** (例: `contoso.microsoft.com`) に置き換えます。
>
>    **[ディレクトリ (テナント) ID]** の値を見つけるには、Azure portal でアプリ登録の **[概要]** ページに移動します。
>    - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を `organizations` に置き換えます。
>    - アプリケーションで "*任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント*" がサポートされる場合は、この値を `common` に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を `consumers` に置き換えます。
>
>    **[サポートされているアカウントの種類]** 値を見つけるには、Azure portal でアプリ登録の **[概要]** ページに移動します。
>
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
> アプリのプロパティの値を使用してプロジェクトを構成しました。

> [!div renderon="docs"]
>
> 次に、引き続き同じフォルダー内の *graphConfig.js* ファイルを編集して、`apiConfig` オブジェクトの `graphMeEndpoint` と `graphMeEndpoint` を設定します。
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> 各値の説明:
> - *\<Enter_the_Graph_Endpoint_Here>* は、API 呼び出しの対象となるエンドポイントです。 メインまたはグローバル Microsoft Graph API サービスの場合は、単に「`https://graph.microsoft.com`」と入力します。 詳細については、[各国のクラウドのデプロイ](/graph/deployments)に関する記事をご覧ください。
>
> #### <a name="step-4-run-the-project"></a>手順 4:プロジェクトを実行する

[Node.js](https://nodejs.org/en/download/) を使用して Web サーバーでプロジェクトを実行する

1. サーバーを起動するために、プロジェクトのディレクトリから次のコマンドを実行します。
    ```cmd
    npm install
    npm start
    ```
1. Web ブラウザーを開き、`http://localhost:3000/` に移動します｡

1. **[サインイン]** を選択してサインインを開始してから、Microsoft Graph API を呼び出します。

ブラウザーにアプリケーションが読み込まれたら、 **[サインイン]** を選択します。 初回サインイン時に、アプリケーションがユーザーのプロファイルにアクセスし、ユーザーをサインインすることへの同意を求められます。 正常にサインインした後は、ユーザー プロファイル情報がページに表示されている必要があります。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ

![サンプル JavaScript SPA のしくみは次のとおりです。1. SPA によってサインインが開始されます。 2. SPA が Microsoft ID プラットフォームから ID トークンを取得します。 3. SPA 呼び出しによってトークンが取得されます。 4. Microsoft ID プラットフォームから SPA にアクセス トークンが返されます。 5. SPA が、アクセス トークンを使用して、Microsoft Graph API に対する HTTP GET 要求を行います。 6. Graph API が HTTP 応答を SPA に返します。](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL ライブラリは、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API へのアクセスに使用されるトークンを要求します。 クイックスタートの *index.html* ファイルにはこのライブラリへの参照が含まれています。

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> 上記のバージョンは、[MSAL.js のリリース](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)に関するページにある最新のリリース バージョンに置き換えることができます。

別の方法として、Node.js がインストール済みの場合は、次のようにして最新バージョンを Node.js Package Manager (npm) を介してダウンロードすることもできます。

```cmd
npm install msal
```

### <a name="msal-initialization"></a>MSAL の初期化

クイックスタートのコードには、MSAL ライブラリを初期化する方法も示されています。

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Where  | 説明 |
> |---------|---------|
> |`clientId`     | Azure portal に登録されているアプリケーションのアプリケーション ID。|
> |`authority`    | (省略可能) 先ほど構成に関するセクションで説明したように、アカウントの種類をサポートする機関 URL。 既定の機関は `https://login.microsoftonline.com/common`です。 |
> |`redirectUri`     | アプリケーション登録の構成済みの応答およびリダイレクト URI。 例では、 `http://localhost:3000/`が使用されます。 |
> |`cacheLocation`  | (省略可能) 認証状態のブラウザー ストレージを設定します。 既定では sessionStorage です。   |
> |`storeAuthStateInCookie`  | (省略可能) 認証フローの検証に必要な認証要求の状態をブラウザーの Cookie に格納するライブラリ。 この Cookie は、特定の[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)に対処するために、IE および Edge ブラウザー用に設定されます。 |

使用できる構成オプションの詳細については、[クライアント アプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

### <a name="sign-in-users"></a>ユーザーのサインイン

次のコード スニペットは、ユーザーをサインインする方法を示しています。

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Where  | 説明 |
> |---------|---------|
> | `scopes`   | (省略可能) サインイン時にユーザーの同意を得るために必要なスコープが含まれます。 たとえば、Microsoft Graph の場合は `[ "user.read" ]`、カスタム Web API (つまり、`api://<Application ID>/access_as_user`) の場合は `[ "<Application ID URL>/scope" ]` になります。 |

> [!TIP]
> あるいは、`loginRedirect` メソッドを使用して、現在のページをポップアップ ウィンドウではなくサインイン ページにリダイレクトすることもできます。

### <a name="request-tokens"></a>トークンの要求

MSAL では、`acquireTokenRedirect`、`acquireTokenPopup`、`acquireTokenSilent` の 3 つのメソッドを使用してトークンを取得します。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `loginRedirect` または `loginPopup` メソッドが実行され、その後の呼び出しでは、保護されたリソースにアクセスするトークンを取得するために `acquireTokenSilent` メソッドが通常使用されます。 トークンを要求または更新するための呼び出しは自動的に行われます。

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Where  | 説明 |
> |---------|---------|
> | `scopes`   | API のアクセス トークンで返されるように要求されているスコープを含めます。 たとえば、Microsoft Graph の場合は `[ "mail.read" ]`、カスタム Web API (つまり、`api://<Application ID>/access_as_user`) の場合は `[ "<Application ID URL>/scope" ]` になります。|

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

ユーザーに Microsoft ID プラットフォームとのやり取りを強制する場合があります。 次に例を示します。
* パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある。
* お使いのアプリケーションが、ユーザーによる同意が必要な追加のリソース スコープへのアクセスを要求している。
* 2 要素認証が必須である。

ほとんどのアプリケーションでお勧めしている通常のパターンは、最初に `acquireTokenSilent` を呼び出し、例外をキャッチしてから、`acquireTokenPopup` (または `acquireTokenRedirect`) を呼び出して、対話型要求を開始するというものです。

`acquireTokenPopup` を呼び出すことで、サインインの際にポップアップ ウィンドウが表示されるようになります (または、`acquireTokenRedirect` によってユーザーが Microsoft ID プラットフォームにリダイレクトされるようになります)。 ユーザーはそのウィンドウ内で、自分の資格情報の確認、必要なリソースへの同意、2 要素認証の完了のいずれかの方法で操作を行う必要があります。

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> このクイックスタートでは、Microsoft Internet Explorer の場合は `loginRedirect` および `acquireTokenRedirect` メソッドを使用しています。Internet Explorer によるポップアップ ウィンドウの処理に関連した[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)があるためです。

## <a name="next-steps"></a>次のステップ

このクイックスタート用アプリケーションの構築に関する詳細なステップ バイ ステップ ガイドについては、次を参照してください。

> [!div class="nextstepaction"]
> [チュートリアル: ユーザーをサインインして、JavaScript シングルページ アプリケーション (SPA) から Microsoft Graph API を呼び出す](tutorial-v2-javascript-spa.md)
