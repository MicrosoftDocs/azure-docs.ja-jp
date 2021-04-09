---
title: 'クイックスタート: Angular シングルページ アプリでユーザーをサインインさせる | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Microsoft ID プラットフォームによって発行されるアクセス トークンを必要とする API を、Angular アプリから呼び出す方法について説明します。
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 5ac6a19fc569d60f75f9da788629331a70b0a4c9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225049"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>クイック スタート:Angular シングルページ アプリケーションでユーザーをサインインさせてアクセス トークンを取得する

このクイックスタートでは、Angular シングルページ アプリケーション (SPA) でユーザーをサインインし、Microsoft Graph を呼び出す方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、Microsoft Graph API または任意の Web API を呼び出すためのアクセス トークンを取得する方法を示します。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 [無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Node.js](https://nodejs.org/en/download/)。
* [Visual Studio Code](https://code.visualstudio.com/download) (プロジェクト ファイルの編集) または [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (プロジェクトの実行)。

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>クイックスタート アプリを登録してダウンロードする
> クイックスタート アプリを起動するには、次のオプションのいずれかを使用します。
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>オプション 1 (簡易): アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure portal のアプリの登録</a>クイックスタート エクスペリエンスに移動します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. クイックスタート ペインに移動し、Angular クイックスタートを表示します。 指示に従って新しいアプリケーションをダウンロードし、自動構成します。
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>オプション 2 (手動): アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
>
> #### <a name="step-1-register-the-application"></a>手順 1:アプリケーションを登録する
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
> 1. 手順に従って、Azure portal に[シングルページ アプリケーションを登録](./scenario-spa-app-registration.md)します。
> 1. アプリの登録の **[認証]** ペインで新しいプラットフォームを追加し、リダイレクト URI (`http://localhost:4200/`) を登録します。
> 1. このクイックスタートでは、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)を使用します。 **[Implicit grant and hybrid flows]\(暗黙的な許可およびハイブリッド フロー\)** セクションで、 **[ID トークン]** と **[アクセス トークン]** を選択します。 このアプリはユーザーのサインインを実行して API を呼び出すため、ID トークンとアクセス トークンが必要です。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイックスタートのコード サンプルを動作させるには、リダイレクト URI として **http://localhost:4200/** を追加し、 **[暗黙の付与]** を有効にする必要があります。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-javascript/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-code-sample"></a>手順 2:コード サンプルのダウンロード
>[!div renderon="docs"]
>Node.js を使用して Web サーバーでプロジェクトを実行するために、[サンプル リポジトリをクローン](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)するか、[コア プロジェクト ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)します。 Visual Studio Code などのエディターを使用して、ファイルを開きます。

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>手順 3:JavaScript アプリを構成する
>
> *src/app* フォルダーの *app.module.ts* を編集し、`MsalModule.forRoot` の `clientId` と `authority` の値を設定します。
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> 次の値を置き換えます。
>
>|値の名前|説明|
>|---------|---------|
>|Enter_the_Application_Id_Here|これは、アプリケーションの登録の **[概要]** ページの **[アプリケーション (クライアント) ID]** の値です。 |
>|Enter_the_Cloud_Instance_Id_Here|これは、Azure クラウドのインスタンスです。 メイン (グローバル) Azure クラウドの場合は、「 **https://login.microsoftonline.com** 」と入力します。 各国のクラウド (中国など) の場合は、「[各国のクラウド](./authentication-national-cloud.md)」を参照してください。|
>|Enter_the_Tenant_Info_Here| 次のいずれかのオプションに設定します。1) お使いのアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値をディレクトリ (テナント) ID またはテナント名 (例: "**contoso.microsoft.com**") に置き換えます。 アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます。 アプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。 |
>|Enter_the_Redirect_Uri_Here|**http://localhost:4200** に置き換えます。|
>|cacheLocation  | (省略可能) 認証状態のブラウザー ストレージを設定します。 既定では **sessionStorage** です。   |
>|storeAuthStateInCookie  | (省略可能) 認証要求の状態を格納するライブラリを識別します。 認証フローを検証するためには、ブラウザーの Cookie にこの状態が必要になります。 この Cookie は、Internet Explorer および Microsoft Edge の 2 つのブラウザーに適合するように設定されます。 詳細については、[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues)に関するページを参照してください。 |
>
> **[アプリケーション (クライアント) ID]** 、 **[ディレクトリ (テナント) ID]** 、 **[サポートされているアカウントの種類]** の値を見つけるには、Azure portal でアプリの **[概要]** ページに移動します。

使用できる構成オプションの詳細については、[クライアント アプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

MSAL.js ライブラリのソース コードは、GitHub の [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) リポジトリにあります。

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>手順 3:プロジェクトを実行する

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>手順 4:プロジェクトを実行する

Node.js を使用している場合:

1. プロジェクト ディレクトリから次のコマンドを実行することによってサーバーを起動します。

   ```console
   npm install
   npm start
   ```

1. **http://localhost:4200/** を参照します。
1. **[ログイン]** を選択します。
1. **[プロファイル]** を選択して Microsoft Graph を呼び出します。

ブラウザーにアプリケーションが読み込まれたら、 **[ログイン]** を選択します。 初回サインイン時に、自分のプロファイルにアプリケーションがアクセスして自分をサインインさせることについて同意を求められます。 正常にサインインした後、 **[プロファイル]** を選択すると、自分のユーザー プロファイル情報がページに表示されます。

## <a name="how-the-sample-works"></a>このサンプルのしくみ

![このクイックスタートのサンプル アプリによる処理を示す図](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>次のステップ

引き続き、ユーザーをサインインさせてトークンを取得する方法を Angular のチュートリアルでご覧ください。

> [!div class="nextstepaction"]
> [Angular チュートリアル](./tutorial-v2-angular.md)