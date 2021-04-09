---
title: ページの再読み込みを回避する (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript (MSAL.js) 用 Microsoft 認証ライブラリを使用してトークンを自動的に取得、更新するときにページのリロードを回避する方法を説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0c96d161e55261af1bbe04eae6ead1d245158d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064829"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>MSAL.js を使用してトークンを自動的に取得、更新するときにページのリロードを回避する
JavaScript (MSAL.js) 用 Microsoft Authentication Library では非表示の `iframe` 要素を使用して、バックグラウンドでトークンが自動的に取得、更新されます。 Azure AD によって、トークン要求で指定された登録済み redirect_uri にトークンが戻されます (既定では、これはアプリのルート ページです)。 応答は 302 なので、結果は `iframe` にロードされる `redirect_uri` に対応する HTML になります。 通常、アプリの `redirect_uri` はルート ページで、これにより、リロードされます。

それ以外の場合として、アプリのルート ページへの移動で認証が要求されると、`iframe` 要素が入れ子になるか `X-Frame-Options: deny` エラーになる場合があります。

MSAL.js は Azure AD によって発行された 302 を無視できず、返されたトークンを処理する必要があるため、`redirect_uri` が `iframe` にロードされることを回避できません。

アプリ全体のリロードやこれにより発生するその他のエラーを回避するには、次の回避策に従ってください。

## <a name="specify-different-html-for-the-iframe"></a>iframe に別の HTML を指定する

config 上の `redirect_uri`プロパティを、単純なページに設定します。ここで認証は不要です。 Azure portal に登録された `redirect_uri` と一致することを確認する必要があります。 ユーザーがログイン プロセスを開始し、ログインが完了した後に正確な場所にリダイレクトされたときに MSAL によりスタート ページが保存されるため、ユーザーのログイン エクスペリエンスに影響は出ません。

## <a name="initialization-in-your-main-app-file"></a>メイン アプリ ファイルの初期化

アプリの初期化、ルーティングなどを定義する中央の単一 Javascript ファイルが存在するようにアプリが構成されている場合、アプリが `iframe` に読み込まれるかどうかに基づいてアプリ モジュールを条件付きで読み込むことができます。 次に例を示します。

AngularJS: app.js の場合

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

Angular: app.module.ts の場合

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>次のステップ
詳細については、MSAL.js を使用した[シングル ページ アプリケーション (SPA) のビルド](scenario-spa-overview.md)に関する記事をご覧ください。