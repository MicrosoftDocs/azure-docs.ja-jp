---
title: "Azure AD AngularJS の概要 | Microsoft Docs"
description: "サインインに関して Azure AD と連携し、Azure AD で保護されている API を OAuth を使用して呼び出す AngularJS シングルページ アプリケーションを構築する方法。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a9997b6a6d30fbd2d21dee5d9c1e3ea92dfa97ab
ms.openlocfilehash: 0ace1ee96d9266db9310ba73c36788a787a9dd15


---
# <a name="help-secure-angularjs-single-page-apps-by-using-azure-ad"></a>Azure AD を使用して AngularJS シングルページ アプリの保護をサポートする

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) を使用すると、サインイン、サインアウト、セキュリティで保護された OAuth API の呼び出しをシングルページ アプリに簡単に追加できます。  アプリでは、Windows Server Active Directory アカウントを使用してユーザー認証を処理することも、Office 365 API や Azure API など、Azure AD で保護がサポートされた任意の Web API を使用することもできます。

ブラウザーで実行される JavaScript アプリケーションに対しては、Active Directory 認証ライブラリ (ADAL) である adal.js が用意されています。 adal.js の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。 それがどれほど簡単であるかを示すために、ここで、次のような、AngularJS To Do List アプリケーションを構築します。

* Azure AD を ID プロバイダーとして使用し、ユーザーがアプリにサインインする処理を実行します。
* ユーザーについての、いくつかの情報を表示します。
* Azure AD から提供されるベアラー トークンを使用して、アプリの To Do List API を安全に呼び出します。
* アプリからユーザーがサインアウトする処理を実行します。

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

1. アプリを Azure AD に登録する。
2. ADAL をインストールし、シングルページ アプリを構成する。
3. ADAL を使用して、シングルページ アプリ内のページの保護をサポートする。

最初に、[アプリのスケルトンをダウンロード](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)します。 また、ユーザーを作成してアプリケーションを登録するための Azure AD テナントも必要です。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## <a name="step-1-register-the-directorysearcher-application"></a>手順 1: DirectorySearcher アプリケーションを登録する
アプリがユーザーを認証し、トークンを取得できるようにするには、まず、アプリを Azure AD テナントに登録する必要があります。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部のバーで、自分のアカウントをクリックします。 **[ディレクトリ]** の一覧で、アプリケーションを登録する Azure AD テナントを選択します。
3. 左側のウィンドウで **[その他のサービス]** をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[追加]** を選択します。
5. 画面の指示に従い、新しい Web アプリケーションや Web API を作成します。
  * **[名前]** には、ユーザーがアプリケーションの機能を把握できる名前を入力します。
  * **[リダイレクト URI]** は、Azure AD がトークンを返す場所です。 このサンプルの既定の場所は `https://localhost:44326/` です。
6. 登録が完了すると、Azure AD によって、一意のアプリケーション ID がアプリに割り当てられます。  この値は次のセクションで必要になるので、[アプリケーション] タブからコピーします。
7. adal.js は Azure AD との通信に、OAuth の暗黙的なフローを使用します。 次の手順を実行して、アプリケーションに対して暗黙的なフローを有効にする必要があります。
  1. アプリケーションをクリックし、**[マニフェスト]** を選択して、インライン マニフェスト エディターを開きます。
  2. `oauth2AllowImplicitFlow` プロパティを見つけます。 値を `true`に設定します。
  3. **[保存]** をクリックして、マニフェストを保存します。
8. アプリケーションに対してテナントのアクセス許可を付与します。 **[設定]** > **[プロパティ]** > **[必要なアクセス許可]** の順に移動し、上部のバーで **[アクセス許可の付与]** をクリックします。 **[はい]** をクリックして確定します。

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>手順 2: ADAL をインストールし、シングルページ アプリを構成する
アプリケーションを Azure AD に登録したので、adal.js をインストールし、ID 関連のコードを記述できます。

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

シングルページ アプリのバックエンド To Do List API がブラウザーからトークンを受け入れることができるように、バックエンドにアプリの登録に関する構成情報が必要です。 TodoSPA プロジェクトで、 `web.config`を開きます。 `<appSettings>` セクションの要素の値を、Azure Portal で使用した値に置き換えます。 これらの値は、コードで ADAL を使用する際に常に参照されます。
  * `ida:Tenant` は、Azure AD テナントのドメイン (contoso.onmicrosoft.com など) です。
  * `ida:Audience` は、ポータルからコピーしたアプリケーションのクライアント ID です。

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>手順 3: ADAL を使用して、シングルページ アプリ内のページの保護をサポートする
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
3. アプリの `TodoList` ビューの保護をサポートします。使用するコードは、`requireADLogin` の&1; 行のみです。

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>概要
これで、ユーザーのサインインを処理し、ベアラー トークンで保護された要求をバックエンド API に送信できる安全なシングルページ アプリが完成しました。 ユーザーが **TodoList** リンクをクリックしたとき、サインインの処理が必要な場合は、adal.js によって Azure AD に自動的にリダイレクトされます。 さらに、アプリのバックエンドに送信されるすべての Ajax 要求に対して、アクセス トークンが adal.js により自動的にアタッチされます。  

上記の手順は、adal.js を使用してシングルページ アプリを構築するのに最低限必要な操作です。 シングルページ アプリには、他にも役立つ機能がいくつかあります。

* サインイン要求とサインアウト要求を明示的に発行するために、adal.js を呼び出すコントローラーで関数を定義できます。  `App/Scripts/homeCtrl.js`で、次のように記述します。

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

* ユーザーがサインインしているかどうかを確認することが必要なシナリオが多数あります。 `userInfo` オブジェクトを使用して、この情報を収集することもできます。  たとえば、`index.html` で、認証ステータスに応じて、**[ログイン]** ボタンまたは **[ログアウト]** ボタンのいずれかを表示できます。

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Azure AD と統合されたシングルページ アプリは、ユーザーの認証処理を行い、OAuth 2.0 を使用してバックエンドを安全に呼び出し、ユーザーについての基本情報を取得することができます。 テナントに一連のユーザーを設定します (設定していない場合)。 To Do List シングルページ アプリを実行し、ユーザーの&1; 人としてサインインします。 タスクをユーザーの To Do List に追加し、サインアウトして、再びサインインします。

adal.js を使用すると、共通 ID 機能を容易にアプリケーションに組み込むことができます。 キャッシュ管理、OAuth プロトコル サポート、サインイン UI を使用してのユーザーの提示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。

完全なサンプル (構成値を除く) については、[GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) で入手できます。

## <a name="next-steps"></a>次のステップ
ここからは、さらなるシナリオに進むことができます。 [シングルページ アプリからの CORS Web API の呼び出し](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)を試してみてください。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]



<!--HONumber=Feb17_HO2-->


