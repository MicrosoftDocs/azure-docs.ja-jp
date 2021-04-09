---
title: 'クイックスタート: 承認コードを使用して JavaScript シングルページ アプリ (SPA) でユーザーをサインインさせる | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、JavaScript シングルページ アプリケーション (SPA) で認証コード フローを使用して、個人アカウント、職場アカウント、学校アカウントのユーザーをサインインさせる方法について説明します。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: dcb82e6cc50a2ff3291d5a900ec9367d69dcdde6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224913"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow-with-pkce"></a>クイックスタート: PKCE 対応の承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する 

このクイックスタートでは、PKCE (Proof Key for Code Exchange) 対応の承認コード フローを使用して、JavaScript シングルページ アプリケーション (SPA) でユーザーをサインインし、Microsoft Graph を呼び出す方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、Microsoft Graph API または任意の Web API を呼び出すためのアクセス トークンを取得する方法を示します。 

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

このクイック スタートでは、承認コード フローで MSAL.js 2.0 を使用します。 暗黙的なフローで MSAL.js 1.0 を使用する同様のクイック スタートについては、[クイック スタート: JavaScript シングルページ アプリ内でのユーザーのサインイン](./quickstart-v2-javascript.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [Azure サブスクリプションを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>クイック スタート アプリケーションを登録してダウンロードする
> クイックスタートのアプリケーションを起動するには、次のオプションのいずれかを使用します。
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1 (簡易): アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure portal の [アプリの登録]</a> に移動します。
> 1. アプリケーションの名前を入力します。
> 1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
> 1. **[登録]** を選択します。
> 1. クイックスタート ペインに移動し、指示に従って新しいアプリケーションをダウンロードして自動的に構成します。
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
> 1. **[管理]** で、 **[認証]** を選択します。
> 1. **[プラットフォーム構成]** で **[プラットフォームを追加]** を選択します。 表示されたウィンドウで **[シングルページ アプリケーション]** を選択します。
> 1. **[リダイレクト URI]** の値を `http://localhost:3000/` に設定します。
> 1. **[構成]** をクリックします。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイックスタートのコード サンプルを動作させるには、**リダイレクト URI** (`http://localhost:3000/`) を追加します。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-javascript/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

> [!div renderon="docs"]
> Node.js を使用して Web サーバーでプロジェクトを実行するために、[コア プロジェクト ファイルをダウンロード](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)します。

> [!div renderon="portal" class="sxs-lookup"]
> Node.js を使用して Web サーバーでプロジェクトを実行する

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>手順 3:JavaScript アプリの構成
>
> *app* フォルダーで、*authConfig.js* ファイルを開き、`msalConfig` オブジェクトの `clientID`、`authority`、`redirectUri` の値を更新します。
>
> ```javascript
> // Config object to be passed to Msal on creation
> const msalConfig = {
>   auth: {
>     clientId: "Enter_the_Application_Id_Here",
>     authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>     redirectUri: "Enter_the_Redirect_Uri_Here",
>   },
>   cache: {
>     cacheLocation: "sessionStorage", // This configures where your cache will be stored
>     storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>   }
> };
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> 以下の説明に従って、`msalConfig` セクションの値を変更します。
>
> - `Enter_the_Application_Id_Here` は、登録したアプリケーションの **アプリケーション (クライアント) ID** です。
>
>    **[アプリケーション (クライアント) ID]** の値を見つけるには、Azure portal でアプリ登録の **[概要]** ページに移動します。
> - `Enter_the_Cloud_Instance_Id_Here` は、Azure クラウドのインスタンスです。 メイン (グローバル) Azure クラウドの場合は、「`https://login.microsoftonline.com/`」と入力します。 **各国** のクラウド (中国など) の場合は、「[各国のクラウド](authentication-national-cloud.md)」を参照してください。
> - `Enter_the_Tenant_info_here` には、次のいずれかが設定されます。
>   - お使いのアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **テナント ID** または **テナント名** に置き換えます。 たとえば、「 `contoso.microsoft.com` 」のように入力します。
>
>    **[ディレクトリ (テナント) ID]** の値を見つけるには、Azure portal でアプリ登録の **[概要]** ページに移動します。
>   - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を `organizations` に置き換えます。
>   - アプリケーションにおいて "*任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント*" をサポートする場合は、この値を `common` に置き換えます。 **このクイック スタートでは**、`common` を使用します。
>   - "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を `consumers` に置き換えます。
>
>    **[サポートされているアカウントの種類]** 値を見つけるには、Azure portal でアプリ登録の **[概要]** ページに移動します。
> - `Enter_the_Redirect_Uri_Here` は `http://localhost:3000/` です。
>
> メイン (グローバル) Azure クラウドを使用している場合、*authConfig.js* の `authority` の値は、次のようになります。
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
> アプリのプロパティの値を使用してプロジェクトを構成しました。

> [!div renderon="docs"]
>
> 次に、引き続き同じフォルダー内の *graphConfig.js* ファイルを編集して、`apiConfig` オブジェクトの `graphMeEndpoint` と `graphMailEndpoint` の値を更新します。
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
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
> `Enter_the_Graph_Endpoint_Here` は、API 呼び出しの対象となるエンドポイントです。 メイン (グローバル) Microsoft Graph API サービスの場合は、「`https://graph.microsoft.com/`」 (末尾のスラッシュを含める) と入力します。 国内クラウドでの Microsoft Graph の詳細については、「[国内クラウドの展開](/graph/deployments)」を参照してください。
>
> メイン (グローバル) Microsoft Graph API サービスを使用している場合、*graphConfig.js* ファイルの `graphMeEndpoint` と `graphMailEndpoint` の値は次のようになります。
>
> ```javascript
> graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
> graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
> ```
>
> #### <a name="step-4-run-the-project"></a>手順 4:プロジェクトを実行する

Node.js を使用して Web サーバーでプロジェクトを実行します。

1. サーバーを起動するために、プロジェクト ディレクトリ内から次のコマンドを実行します。
    ```console
    npm install
    npm start
    ```
1. `http://localhost:3000/` を参照します。

1. **[サインイン]** を選択してサインイン プロセスを開始してから、Microsoft Graph API を呼び出します。

    初回サインイン時に、自分のプロファイルにアプリケーションがアクセスして自分をサインインさせることについて同意を求められます。 正常にサインインした後は、ユーザー プロファイル情報がページに表示されている必要があります。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ

![シングルページ アプリケーションの認証コード フローを示す図。](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

MSAL.js ライブラリは、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API へのアクセスに使用されるトークンを要求します。 サンプルの *index.html* ファイルにはこのライブラリへの参照が含まれています。

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Node.js がインストール済みの場合は、次のように Node.js Package Manager (npm) を使用して最新バージョンをダウンロードできます。

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>次のステップ

このクイック スタートで使用されるアプリケーションの構築に関する詳細なステップ バイ ステップ ガイドについては、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [サインインして MS Graph を呼び出すチュートリアル](./tutorial-v2-javascript-auth-code.md)
