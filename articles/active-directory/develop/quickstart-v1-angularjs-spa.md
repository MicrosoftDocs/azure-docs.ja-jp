---
title: Azure AD を使用したサインインおよびサインアウトを行う AngularJS シングルページ アプリを構築する | Microsoft Docs
description: サインインに関して Azure AD と連携し、Azure AD で保護されている API を OAuth を使用して呼び出す AngularJS シングルページ アプリケーションを構築する方法について説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20c62d379006382d4208e4b111202581bc75454f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380760"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>クイック スタート:Azure Active Directory を使用したサインインおよびサインアウトを行う AngularJS シングルページ アプリを構築する

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (Azure AD) を使用すると、サインイン、サインアウト、セキュリティで保護された OAuth API の呼び出しをシングルページ アプリに簡単に追加できます。 アプリでは、Windows Server Active Directory アカウントを使用してユーザー認証を処理することも、Office 365 API や Azure API など、Azure AD で保護がサポートされた任意の Web API を使用することもできます。

ブラウザーで実行される JavaScript アプリケーションに対しては、Active Directory 認証ライブラリ (ADAL) である adal.js が用意されています。 adal.js の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。

このクイック スタートでは、以下の操作を行う AngularJS To Do List アプリケーションを構築する方法について説明します。

* Azure AD を ID プロバイダーとして使用し、ユーザーがアプリにサインインする処理を実行します。
* ユーザーについての、いくつかの情報を表示します。
* Azure AD から提供されるベアラー トークンを使用して、アプリの To Do List API を安全に呼び出します。
* アプリからユーザーがサインアウトする処理を実行します。

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

1. アプリを Azure AD に登録する。
2. ADAL をインストールし、シングルページ アプリを構成する。
3. ADAL を使用して、シングルページ アプリ内のページの保護をサポートする。

> [!NOTE]
> 職場や学校のアカウントに加えて個人のアカウントのサインインを有効にする必要がある場合は、 *[Microsoft ID プラットフォーム エンドポイント](azure-ad-endpoint-comparison.md)* を使用できます。 詳細については、[こちらの JavaScript SPA のチュートリアル](tutorial-v2-javascript-spa.md)と、*Microsoft ID プラットフォーム エンドポイント*を説明している[こちらの記事](active-directory-v2-limitations.md)をご覧ください。 

## <a name="prerequisites"></a>前提条件

最初に、以下の前提条件を完了します。

* [アプリのスケルトンをダウンロード](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)する。
* ユーザーを作成してアプリケーションを登録するための Azure AD テナントがある。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](quickstart-create-new-tenant.md)」を参照して取得してください。

## <a name="step-1-register-the-directorysearcher-application"></a>手順 1:DirectorySearcher アプリケーションを登録する

アプリがユーザーを認証し、トークンを取得できるようにするには、まず、アプリを Azure AD テナントに登録する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 複数のディレクトリにサインインしている場合は、適切なディレクトリを表示していることを確認する必要があります。 これを行うには、上部のバーで、自分のアカウントをクリックします。 **[ディレクトリ]** の一覧で、アプリケーションを登録する Azure AD テナントを選択します。
1. 左側のウィンドウで **[すべてのサービス]** をクリックし、 **[Azure Active Directory]** を選択します。
1. **[アプリの登録]** をクリックし、 **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの名前を入力します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
1. **[リダイレクト URI]** セクションで **[Web]** プラットフォームを選択し、値を `https://localhost:44326/` (Azure AD がトークンを返す先の場所) に設定します。
1. 終了したら、 **[登録]** を選択します。 アプリの **[概要]** ページで、 **[Application (client) ID]\(アプリケーション (クライアント) ID\)** の値を書き留めます。
1. adal.js は Azure AD との通信に、OAuth の暗黙的なフローを使用します。 次の手順を実行して、アプリケーションに対して暗黙的なフローを有効にする必要があります。 登録済みのアプリケーションの左側のナビゲーション ウィンドウで、 **[認証]** を選択します。
1. **[詳細設定]** の **[暗黙的な許可]** で、 **[ID トークン]** と **[アクセス トークン]** の両方のチェック ボックスをオンにします。 このアプリではユーザーをサインインし、API を呼び出す必要があるため、ID トークンとアクセス トークンが必要になります。
1. **[保存]** を選択します。
1. アプリケーションに対してテナントのアクセス許可を付与します。 **[API のアクセス許可]** に移動し、 **[同意する]** の下にある **[管理者の同意の付与]** ボタンを選択します。
1. **[はい]** を選択して確定します。

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>手順 2:ADAL をインストールし、シングルページ アプリを構成する

アプリケーションを Azure AD に登録したので、adal.js をインストールし、ID 関連のコードを記述できます。

### <a name="configure-the-javascript-client"></a>JavaScript クライアントを構成する

まず、パッケージ マネージャー コンソールを使用して、adal.js を TodoSPA プロジェクトに追加します。

1. [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) をダウンロードし、`App/Scripts/` プロジェクト ディレクトリに追加します。
2. [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) をダウンロードし、`App/Scripts/` プロジェクト ディレクトリに追加します。
3. 各スクリプトを `</body>` in `index.html`の前に読み込みます。

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>バック エンド サーバーを構成する

シングルページ アプリのバックエンド To Do List API がブラウザーからトークンを受け入れることができるように、バックエンドにアプリの登録に関する構成情報が必要です。 TodoSPA プロジェクトで、 `web.config`を開きます。 `<appSettings>` セクションの要素の値を、Azure Portal で使用した値に置き換えます。 これらの値は、コードで ADAL を使用する際に常に参照されます。

   * `ida:Tenant` は、Azure AD テナントのドメイン (contoso.onmicrosoft.com など) です。
   * `ida:Audience` は、ポータルからコピーしたアプリケーションのクライアント ID です。

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>手順 3:ADAL を使用して、シングルページ アプリ内のページの保護をサポートする

adal.js には AngularJS ルートと HTTP プロバイダーが組み込まれているため、シングルページ アプリの個々のビューを保護するのに役立ちます。

1. `App/Scripts/app.js` に、adal.js モジュールを組み込みます。

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. `App/Scripts/app.js` でも、アプリケーション登録の構成値を使用して `adalProvider` を初期化します。

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. アプリの `TodoList` ビューの保護をサポートします。使用するコードは、`requireADLogin` の 1 行のみです。

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>まとめ

これで、ユーザーのサインインを処理し、ベアラー トークンで保護された要求をバックエンド API に送信できる安全なシングルページ アプリが完成しました。 ユーザーが **TodoList** リンクをクリックしたとき、サインインの処理が必要な場合は、adal.js によって Azure AD に自動的にリダイレクトされます。 さらに、アプリのバックエンドに送信されるすべての Ajax 要求に対して、アクセス トークンが adal.js により自動的にアタッチされます。

上記の手順は、adal.js を使用してシングルページ アプリを構築するのに最低限必要な操作です。 シングルページ アプリには、他にも役立つ機能がいくつかあります。

* サインイン要求とサインアウト要求を明示的に発行するために、adal.js を呼び出すコントローラーで関数を定義できます。 `App/Scripts/homeCtrl.js`で、次のように記述します。

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* アプリの UI にユーザー情報を表示できます。 ADAL サービスが既に `userDataCtrl` コントローラーに追加されているため、関連付けられたビュー `App/Views/UserData.html` の `userInfo` オブジェクトにアクセスできます。

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* ユーザーがサインインしているかどうかを確認することが必要なシナリオが多数あります。 `userInfo` オブジェクトを使用して、この情報を収集することもできます。 たとえば、`index.html` で、認証ステータスに応じて、 **[ログイン]** ボタンまたは **[ログアウト]** ボタンのいずれかを表示できます。

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Azure AD と統合されたシングルページ アプリは、ユーザーの認証処理を行い、OAuth 2.0 を使用してバックエンドを安全に呼び出し、ユーザーについての基本情報を取得することができます。 テナントに一連のユーザーを設定します (設定していない場合)。 To Do List シングルページ アプリを実行し、ユーザーの 1 人としてサインインします。 タスクをユーザーの To Do List に追加し、サインアウトして、再びサインインします。

adal.js を使用すると、共通 ID 機能を容易にアプリケーションに組み込むことができます。 キャッシュ管理、OAuth プロトコル サポート、サインイン UI を使用してのユーザーの提示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。

参考用に、完全なサンプル (環境に応じた構成値を除く) が [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) で提供されています。

## <a name="next-steps"></a>次の手順

ここからは、さらなるシナリオに進むことができます。

> [!div class="nextstepaction"]
> [シングルページ アプリから CORS Web API を呼び出します](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)。
