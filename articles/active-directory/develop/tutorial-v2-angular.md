---
title: チュートリアル:認証に Microsoft ID プラットフォームを使用する Angular アプリを作成する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、Microsoft ID プラットフォームを使用してユーザーのサインインを処理し、アクセス トークンを取得してそのユーザーに代わって Microsoft Graph API を呼び出す Angular シングルページ アプリ (SPA) を作成します。
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 105353598a2af60c407bacf02b4527b2de84e450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756147"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>チュートリアル:Angular シングルページ アプリケーションからユーザーをサインインさせて Microsoft Graph API を呼び出す

このチュートリアルでは、ユーザーのサインインを行い、Microsoft Graph API を呼び出す Angular シングルページ アプリケーション (SPA) を構築します。

このチュートリアルの内容:

> [!div class="checklist"]
> * `npm` で Angular プロジェクトを作成する
> * Azure portal でアプリケーションを登録する
> * ユーザーのサインインとサインアウトをサポートするコードを追加する
> * Microsoft Graph API を呼び出すコードを追加する
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

* ローカル Web サーバーを実行するための [Node.js](https://nodejs.org/en/download/)。
* プロジェクト ファイルを編集するためのエディター ([Visual Studio Code](https://code.visualstudio.com/download) など)。

## <a name="how-the-sample-app-works"></a>このサンプル アプリのしくみ

![このチュートリアルで生成されたサンプル アプリの動作を示す図](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

このチュートリアルで作成したサンプル アプリケーションを使用すると、Angular SPA で、Microsoft ID プラットフォームによって発行されたトークンを受け取る Microsoft Graph API や Web API に対してクエリを実行することができます。 Microsoft Authentication Library (MSAL) for Angular という、コア MSAL.js ライブラリのラッパーを使用します。 MSAL Angular では、Angular (6 以降) のアプリケーションが Azure Active Directory (Azure AD) を使用して、エンタープライズ ユーザーや Microsoft アカウントを持つユーザー、さらにソーシャル ID (Facebook、Google、LinkedIn など) を持つユーザーを認証することができます。 また、アプリケーションは、このライブラリを通じて、Microsoft クラウド サービスや Microsoft Graph にアクセスすることができます。

このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、MSAL によって処理されます。

### <a name="libraries"></a>ライブラリ

このチュートリアルでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript Angular Wrapper 用の Microsoft Authentication Library|

MSAL.js ライブラリのソース コードは、GitHub の [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) リポジトリにあります。

## <a name="create-your-project"></a>プロジェクトを作成する

次の npm コマンドを使用して、新しい Angular アプリケーションを生成します。

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>アプリケーションの登録

Azure portal に[シングルページ アプリケーションを登録する手順](./scenario-spa-app-registration.md)を実行します。

後で使用するために、登録の **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。

**[リダイレクト URI]** の値を **http://localhost:4200/** として登録し、暗黙的な許可の設定を有効にします。

## <a name="configure-the-application"></a>アプリケーションの構成

1. *src/app* フォルダーで、次に示すように、*app.module.ts* を編集して、`MSALModule` を `imports` に追加し、`isIE` 定数を追加します。

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    次の値を置き換えます。

    |値の名前|概要|
    |---------|---------|
    |Enter_the_Application_Id_Here|これは、アプリケーションの登録の **[概要]** ページの **[アプリケーション (クライアント) ID]** の値です。 |
    |Enter_the_Cloud_Instance_Id_Here|これは、Azure クラウドのインスタンスです。 メイン (グローバル) Azure クラウドの場合は、「 **https://login.microsoftonline.com** 」と入力します。 各国のクラウド (中国など) の場合は、「[各国のクラウド](./authentication-national-cloud.md)」を参照してください。|
    |Enter_the_Tenant_Info_Here| 次のいずれかのオプションに設定します。1) お使いのアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値をディレクトリ (テナント) ID またはテナント名 (例: "**contoso.microsoft.com**") に置き換えます。 アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます。 アプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。 |
    |Enter_the_Redirect_Uri_Here|**http://localhost:4200** に置き換えます。|

    使用できる構成オプションの詳細については、[クライアント アプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

2. 同じファイルの先頭に、次の import ステートメントを追加します。

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. 次の import ステートメントを `src/app/app.component.ts` の先頭に追加します。

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>ユーザーのサインイン

ユーザーをサインインさせるには、`AppComponent` に次のコードを追加します。

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> Internet Explorer のユーザーには `loginRedirect` を使用することをお勧めします。

## <a name="acquire-a-token"></a>トークンを取得する

### <a name="angular-interceptor"></a>Angular Interceptor

MSAL Angular には `Interceptor` クラスが用意されています。これは、既知の保護されたリソースに対して Angular の `http` クライアントを使用する送信要求のためにトークンを自動的に取得します。

まず、`Interceptor` クラスをアプリケーションのプロバイダーとして含めます。

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

次に、保護されたリソースのマップを `protectedResourceMap` として `MsalModule.forRoot()` に指定し、`consentScopes` にそれらのスコープを含めます。 `protectedResourceMap` コレクションに指定する URL は、大文字と小文字が区別されます。

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

最後に、HTTP 要求でユーザーのプロファイルを取得します。

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent、acquireTokenPopup、acquireTokenRedirect
MSAL は、`acquireTokenRedirect`、`acquireTokenPopup`、`acquireTokenSilent` の 3 つのメソッドを使用してトークンを取得します。 ただし、前のセクションで示したように、Angular アプリではなく `MsalInterceptor` クラスを使用することをお勧めします。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 `loginRedirect` または `loginPopup` メソッドを初めて実行した後、後続の呼び出しでは保護されたリソースにアクセスするためのトークンを取得するために、`acquireTokenSilent` が一般的に使用されます。 トークンを要求または更新するための呼び出しは自動的に行われます。

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

このコードの `scopes` には、API のアクセス トークンに含めて返すよう要求するスコープが指定されます。

次に例を示します。

* Microsoft Graph の場合 `["user.read"]`
* カスタム Web API の場合 `["<Application ID URL>/scope"]` (つまり、`api://<Application ID>/access_as_user`)

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

場合によっては、ユーザーに Microsoft ID プラットフォームと対話させる必要があります。 次に例を示します。

* パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある。
* お使いのアプリケーションが、ユーザーによる同意が必要な追加のリソース スコープへのアクセスを要求している。
* 2 要素認証が必須である。

ほとんどのアプリケーションでは、最初に `acquireTokenSilent` を呼び出し、例外をキャッチしてから、`acquireTokenPopup` (または `acquireTokenRedirect`) を呼び出して、対話型要求を開始するというパターンをお勧めしています。

`acquireTokenPopup` を呼び出すと、サインインのポップアップ ウィンドウが表示されます。 または、`acquireTokenRedirect` によってユーザーが Microsoft ID プラットフォームにリダイレクトされます。 そのウィンドウで、ユーザーは資格情報を確認するか、必要なリソースに同意するか、または 2 要素認証を完了する必要があります。

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> このクイックスタートでは、Microsoft Internet Explorer の場合は `loginRedirect` および `acquireTokenRedirect` メソッドを使用しています。これは、Internet Explorer によるポップアップ ウィンドウの処理に関連した[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)があるためです。

## <a name="log-out"></a>ログアウト

次のコードを追加して、ユーザーをログアウトさせます。

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>UI を追加する
Angular Material コンポーネント ライブラリを使用して UI を追加する方法の例については、[サンプル アプリケーション](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)を参照してください。

## <a name="test-your-code"></a>コードのテスト

1.  アプリケーション フォルダーからコマンドライン プロンプトで次のコマンドを実行して、Web サーバーを起動してポートをリッスンします。

    ```bash
    npm install
    npm start
    ```
1. ブラウザーに「 **http://localhost:4200** 」または「 **http://localhost:{port}** 」と入力します。ここで、*port* は、Web サーバーがリッスンしているポートです。


### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションへの初回サインイン時には、お使いのプロファイルへのアクセスをアプリケーションに許可してサインインに同意するよう求められます。

![[Permissions requested]\(アクセス許可が要求されています\) ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可を追加する

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 このスコープは既定で、登録ポータルに登録されているすべてのアプリケーションで自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API には、ユーザーの予定表を一覧表示するために *Calendars.Read* スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、*Calendars.Read* の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、*Calendars.Read* スコープを `acquireTokenSilent` 呼び出しに追加します。

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

バックエンド API でスコープを必要としない (推奨されません) 場合は、トークンを取得するための呼び出しでスコープとして *clientId* を使用できます。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

Microsoft ID プラットフォームにおけるシングルページ アプリケーション (SPA) 開発の詳細を、複数のパートから成る一連の記事でご覧ください。

> [!div class="nextstepaction"]
> [シナリオ:シングルページ アプリ](scenario-spa-overview.md)
