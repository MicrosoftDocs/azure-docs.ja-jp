---
title: "Azure AD v2.0 .NET AngularJS シングル ページ アプリの概要 | Microsoft Docs"
description: "ユーザーのサインインに個人の Microsoft アカウントと会社/学校アカウントの両方を使用する Angular JS シングル ページ アプリを構築する方法について説明します。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 6a341781-278f-461b-92ca-7572a06e6852
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 9cd676554542e4effef54790bf9095c5b7a8f75b
ms.openlocfilehash: 0ab6506e14997c0c6d58afa22db63f928d7cceb9


---
# <a name="add-sign-in-to-an-angularjs-single-page-app---net"></a>AngularJS シングル ページ アプリへのサインインの追加 - .NET
この記事では、Microsoft が提供するアカウントでのサインインを、Azure Active Directory v2.0 エンドポイントを使用して AngularJS アプリに追加します。  v2.0 エンドポイントを使用すると、アプリで単一の統合を実行し、個人アカウントと職場/学校アカウントの両方でユーザーを認証できます。

ここで扱うサンプルは、タスクをバックエンドの REST API に格納する簡単な To-Do List シングル ページ アプリです。.NET 4.5 MVC フレームワークで記述され、Azure AD の OAuth ベアラー トークンを使用して保護されます。  AngularJS アプリは、オープン ソースの JavaScript 認証ライブラリ [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) を使用して、サインイン プロセス全体を処理し、REST API を呼び出すためのトークンを取得します。  [Microsoft Graph](https://graph.microsoft.com) などの他の REST API に対する認証にも、同じパターンを適用できます。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。  v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
> 
> 

## <a name="download"></a>ダウンロード
作業を開始するには、Visual Studio をダウンロードしてインストールする必要があります。  インストールが完了したら、スケルトン アプリを複製または [ダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) できます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

スケルトン アプリには、簡単な AngularJS アプリ用の定型コードがすべて含まれますが、ID 関連の部分はまったく含まれません。  代わりに、完全なサンプルを複製または [ダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) することもできます。

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## <a name="register-an-app"></a>アプリを登録します
最初に、[アプリ登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)でアプリを作成するか、または[詳細な手順](active-directory-v2-app-registration.md)に従います。  次のことを確認します。

* アプリの **Web** プラットフォームを追加します。
* 適切な **リダイレクト URI**を入力します。 このサンプルの既定値は `https://localhost:44326/`です。
* **[暗黙的フローを許可する]** チェック ボックスはオンのままにします。 

アプリに割り当てられた**アプリケーション ID** をメモしておきます。これは後で必要になります。 

## <a name="install-adaljs"></a>adal.js をインストールする
まず、ダウンロードしたプロジェクトに移動し、adal.js をインストールします。  [bower](http://bower.io/) をインストールしてある場合は、次のコマンドを実行するだけで済みます。  依存関係のバージョンの不一致がある場合は、高い方のバージョンを選択します。

```
bower install adal-angular#experimental
```

または、[adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) と [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js) を手動でダウンロードすることもできます。  両方のファイルを `TodoSPA` プロジェクトの `app/lib/adal-angular-experimental/dist` ディレクトリに追加します。

Visual Studio でプロジェクトを開き、メイン ページ本文の最後に adal.js を読み込みます。

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>REST API を設定する
バックエンドの REST API が動作するように設定します。  プロジェクトのルートにある `web.config` を開き、`audience` の値を置き換えます。  REST API は、この値を使用して、AJAX 要求で Angular アプリから受け取ったトークンを検証します。

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>

...
```

REST API のしくみについて説明するときにはよくあることです。  自由にコードをいろいろ調べてください。ただし、Azure AD による Web API の保護をもっと詳しく知りたい場合は、[こちらの記事](active-directory-v2-devquickstarts-dotnet-api.md)を参照してください。 

## <a name="sign-users-in"></a>ユーザーをサインインする
次に、ID コードを作成します。  既にお気付きかもしれませんが、adal.js に含まれる AngularJS プロバイダーが Angular のルーティング メカニズムを適切に処理します。  最初に、adal モジュールをアプリケーションに追加します。

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

アプリケーション ID で `adalProvider` を初期化できるようになります。

```js
// app/scripts/app.js

...

adalProvider.init({

        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 

        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',

        // Your application id from the registration portal
        clientId: '<Your-application-id>',

        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',

    }, $httpProvider);
```

アプリを保護し、ユーザーをサインインするために必要な情報はすべて adal.js に含まれています。  アプリの特定のルートにサインインを強制するためのコードは&1; 行だけで済みます。

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

ユーザーが `TodoList` リンクをクリックしたとき、サインインの処理が必要な場合は、adal.js によって Azure AD に自動的にリダイレクトされます。  また、コントローラーで adal.js を呼び出すことによって、サインインおよびサインアウト要求を明示的に送信することもできます。

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {

        // Redirect the user to sign in
        adalService.login();

    };
    $scope.logout = function () {

        // Redirect the user to log out    
        adalService.logOut();

    };
...
```

## <a name="display-user-info"></a>ユーザー情報を表示する
ユーザーがサインインしたら、サインインしたユーザーの認証データにアプリケーションからアクセスする必要があります。  adal.js は、 `userInfo` オブジェクトでこの情報を公開します。  ビューでこのオブジェクトにアクセスするには、まず adal.js を対応するコントローラーのルート スコープに追加します。

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

こうすると、ビューで `userInfo` オブジェクトを直接アドレス指定できます。 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

また、 `userInfo` オブジェクトを使用してユーザーがサインインしているかどうかも判定できます。

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>REST API を呼び出す
最後に、いくつかのトークンを取得し、タスクを作成、読み取り、更新、削除する REST API を呼び出します。  ただし、  開発者は*何も*行う必要がありません。  トークンの取得、キャッシュ、更新は adal.js が自動的に行います。  また、REST API に送信される AJAX 要求へのトークンの添付も行われます。  

そのしくみは すべては [AngularJS インターセプター](https://docs.angularjs.org/api/ng/service/$http)のおかげです。AngularJS インターセプターにより、adal.js は送信および受信する http メッセージを変換できます。  さらに、adal.js は、ウィンドウと同じドメインに送信される要求が AngularJS アプリと同じアプリケーション ID 用のトークンを使用するものと想定します。  そのために、Angular アプリと NodeJS REST API の両方で同じアプリケーション ID を使用しました。  もちろん、必要に応じてこの動作をオーバーライドし、他の REST API 用のトークンを取得するよう adal.js に指示できますが、このシナリオではシンプルに既定のままにします。

次のスニペットは、Azure AD からベアラー トークンを含む要求を簡単に送信できることを示しています。

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

お疲れさまでした。  Azure AD に統合されたシングル ページ アプリが完成しました。  アプリは、  ユーザーの認証を行い、OpenID Connect を使用してバックエンドの REST API を安全に呼び出し、ユーザーについての基本情報を取得できます。  個人の Microsoft アカウントまたは Azure AD の職場/学校アカウントを持つユーザーが既定でサポートされます。  アプリを実行し、ブラウザーで `https://localhost:44326/` に移動して、  個人の Microsoft アカウントまたは職場/学校アカウントを使用してサインインしてください。  ユーザーの To-Do List にタスクを追加し、サインアウトした後で、  他の種類のアカウントを使用してサインインしてみてください。 職場/学校ユーザーを作成するために Azure AD テナントが必要な場合は、[こちらで取得方法がわかります](active-directory-howto-tenant.md) (無料です)。

v2.0 エンドポイントについての学習を続けるには、 [v2.0 開発者ガイド](active-directory-appmodel-v2-overview.md)に戻ってください。  その他のリソースについては、以下を参照してください。

* [GitHub の Azure 用サンプル >>](https://github.com/Azure-Samples)
* [Stack Overflow の Azure AD >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [Azure.com](https://azure.microsoft.com/documentation/services/active-directory/) の Azure AD ドキュメント >>

## <a name="get-security-updates-for-our-products"></a>Microsoft 製品のセキュリティ更新プログラムの取得
セキュリティの問題が発生したときに通知を受け取ることをお勧めします。そのためには、[このページ](https://technet.microsoft.com/security/dd252948)にアクセスし、セキュリティ アドバイザリ通知を受信登録してください。




<!--HONumber=Feb17_HO1-->


