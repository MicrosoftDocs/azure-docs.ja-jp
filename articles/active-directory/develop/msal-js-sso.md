---
title: シングル サインオン (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を使用したシングル サインオン エクスペリエンスのビルドについて説明します。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84690780"
---
# <a name="single-sign-on-with-msaljs"></a>MSAL.js でのシングル サインオン

シングル サインオン (SSO) を使用すると、ユーザーは、資格情報を 1 回入力してサインインし、認証を再度行う必要なく複数のアプリケーションで再利用できるセッションを確立することができます。 これにより、ユーザーにシームレスなエクスペリエンスが提供され、資格情報を繰り返し要求されることが減ります。

Azure AD では、ユーザーが初めてを認証を行うときに、セッション Cookie を設定することで、アプリケーションに SSO 機能を提供します。 MSAL.js ライブラリでは、アプリケーションは複数の方法でこれを利用できます。

## <a name="sso-between-browser-tabs"></a>ブラウザーのタブ間の SSO

アプリケーションで複数のタブが開かれ、ユーザーは最初に 1 つのタブでサインインする場合、ユーザーは要求されることなく他のタブにもサインインします。 MSAL.js は、ブラウザーの `localStorage` にユーザーの ID トークンをキャッシュし、他の開いているタブでユーザーをアプリケーションにサインインさせます。

既定で MSAL.js が使用する `sessionStorage` では、セッションをタブ間で共有することはできません。 タブ間の SSO を利用するには、次に示すように、MSAL.js で `cacheLocation` を `localStorage` に設定します。

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>アプリ間の SSO

ユーザーが認証を行うと、ブラウザーの Azure AD ドメインでセッション Cookie が設定されます。 MSAL.js では、このセッション Cookie に依存して、ユーザーに異なるアプリケーション間の SSO が提供されます。 また、MSAL.js では、アプリケーション ドメインごとに、ユーザーの ID トークンとアクセス トークンがブラウザーのストレージにキャッシュされます。 その結果、SSO の動作は場合によって異なります。  

### <a name="applications-on-the-same-domain"></a>同じドメイン上のアプリケーション

アプリケーションが同じドメインでホストされている場合、ユーザーは、アプリに 1 回サインインすれば、他のアプリについてもプロンプトなしで認証されます。 MSAL.js は、ドメインにキャッシュされているユーザーのトークンを利用して、SSO を提供します。

### <a name="applications-on-different-domain"></a>異なるドメイン上のアプリケーション

アプリケーションが異なるドメインでホストされているときは、ドメイン A でキャッシュされたトークンに、ドメイン B の MSAL.js はアクセスできません。

つまり、ユーザーはドメイン A でサインインしてドメイン B のアプリケーションに移動すると、Azure AD のページでリダイレクトされるか、プロンプトを表示されます。 Azure AD はまだユーザーのセッション Cookie を持っているため、ユーザーは Azure AD によってサインインされ、資格情報を再入力する必要はありません。 ユーザーが Azure AD でセッションに複数のユーザー アカウントを持っている場合、ユーザーはサインインするアカウントの選択を求められます。

### <a name="automatically-select-account-on-azure-ad"></a>Azure AD でのアカウントの自動選択

アプリケーションがユーザーの認証コンテキストにアクセスでき、複数のアカウントでサインインしているときに、Azure AD のアカウント選択プロンプトが表示されないようにしたい場合があります。  これは、いくつかの異なる方法で実行できます。

**セッション ID (SID) の使用**

セッション ID は、ID トークンで構成できる[オプションの要求](active-directory-optional-claims.md)です。 この要求により、アプリケーションでは、ユーザーのアカウント名やユーザー名とは関係なく、ユーザーの Azure AD セッションを識別できます。 `acquireTokenSilent` の呼び出しへの要求パラメーターで、SID を渡すことができます。 これにより、Azure AD はアカウントの選択をバイパスできます。 SID はセッション Cookie にバインドされていて、ブラウザーのコンテキストをまたぎません。

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID は、MSAL.js での `acquireTokenSilent` の呼び出しによって行われるサイレント認証要求でのみ使用できます。
アプリケーション マニフェストでオプションの要求を構成する手順については、[こちら](active-directory-optional-claims.md)をご覧ください。

**ログイン ヒントの使用**

SID の要求を構成していない場合、または対話型認証の呼び出しでアカウント選択のプロンプトをバイパスする必要がある場合は、MSAL.js の対話型メソッド (`loginPopup`、`loginRedirect`、`acquireTokenPopup`、`acquireTokenRedirect`) において、要求パラメーターで `login_hint` を提供し、必要に応じて `domain_hint` を `extraQueryParameters` として渡すことにより、これを行うことができます。 次に例を示します。

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

ユーザーに対する ID トークンで返される要求を読み取ることにより、login_hint と domain_hint の値を取得できます。

* **loginHint** には、ID トークンの `preferred_username` 要求を設定する必要があります。

* **domain_hint** は、/common 機関を使用するときにのみ、渡す必要があります。 ドメイン ヒントは、テナント ID (tid) によって決定されます。  ID トークンの `tid` 要求が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、それはコンシューマーです。 それ以外の場合は、組織になります。

ログイン ヒントとドメイン ヒントの値について詳しくは、[こちら](v2-oauth2-implicit-grant-flow.md)をご覧ください。

> [!Note]
> SID と login_hint を同時に渡すことはできません。 これはエラー応答になります。

## <a name="sso-without-msaljs-login"></a>MSAL.js ログインなしの SSO

仕様上、MSAL.js では、API のトークンを取得する前に、ログイン メソッドを呼び出してユーザー コンテキストを確立する必要があります。 ログイン メソッドは対話形式であるため、ユーザーにはプロンプトが表示されます。

アプリケーションが別のアプリケーションで開始された認証を通じて認証されたユーザーのコンテキストまたは ID トークンにアクセスすることができ、最初に MSAL.js 経由でサインインすることなく、SSO を利用してトークンを取得することが望ましい場合があります。

たとえば次のような場合です。ユーザーは、アドオンまたはプラグインとして実行される別の JavaScript アプリケーションがホストされている親 Web アプリケーションにサインインします。

このシナリオでの SSO は、次のようにして実現できます。

次のように、MSAL.js の `acquireTokenSilent` の呼び出しへの要求パラメーターとして、使用可能な場合は `sid` を渡します (または、`login_hint` および必要に応じて `domain_hint`)。

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>MSAL.js の更新に対する ADAL.js での SSO

MSAL.js には、Azure AD の認証シナリオに対する ADAL.js での機能パリティがあります。 ADAL.js から MSAL.js への移行を容易にし、ユーザーにサインインを繰り返し要求しなくて済むように、ライブラリは ADAL.js キャッシュ内にあるユーザーのセッションを表す ID トークンを読み取り、ユーザーを MSAL.js にシームレスにサインインさせます。  

ADAL.js から更新するときにシングル サインオン (SSO) の動作を利用するには、ライブラリでトークンのキャッシュ用に `localStorage` が使用されるようにする必要があります。 次のように、初期化時に、MSAL.js と ADAL.js 両方の構成で、`cacheLocation` を `localStorage` に設定します。


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

これを構成すると、MSAL.js は ADAL.js で認証されたユーザーのキャッシュされた状態を読み取って、MSAL.js で SSO を提供するために使用することができます。

## <a name="next-steps"></a>次の手順

Azure AD での[シングル サインオン セッションとトークンの有効期間](active-directory-configurable-token-lifetimes.md)の値に関する詳細を確認してください。
