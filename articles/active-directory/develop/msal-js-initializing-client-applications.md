---
title: MSAL.js クライアント アプリを初期化する | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を使用することによるクライアント アプリケーションの初期化について説明します。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b041d8777f81f1796a2e2f7926f324e3b601bd93
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916504"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL.js を使用してクライアント アプリケーションを初期化する
この記事では、ユーザー エージェント アプリケーションのインスタンスを使用して JavaScript 用 Microsoft Authentication Library (MSAL.js) を初期化する方法について説明します。 ユーザー エージェント アプリケーションは、Web ブラウザーなどのユーザー エージェントでクライアント コードが実行されるパブリック クライアント アプリケーションの一種です。 これらのクライアントでは、シークレットは格納されません。ブラウザーのコンテキストが公開されアクセス可能であるからです。 クライアント アプリケーションの種類とアプリケーションの構成オプションの詳細については、[概要](msal-client-applications.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
アプリケーションを初期化する前に、まず、そのアプリケーションを [Azure portal に登録](scenario-spa-app-registration.md)して、Microsoft ID プラットフォームに統合できるようにする必要があります。 登録後に、次の情報が必要な場合があります (Azure portal で検索できます)。

- クライアント ID (ご利用のアプリケーションの GUID を表す文字列)
- ID プロバイダーの URL (名前付きインスタンス) とアプリケーションのサインイン対象ユーザー。 これら 2 つのパラメーターは機関と総称されます。
- 組織専用の基幹業務アプリケーション (および名前付きシングル テナント アプリケーション) を作成している場合は、テナント ID。
- Web アプリの場合は、redirectUri も設定する必要があります。そこで ID プロバイダーは、セキュリティ トークンを使用してお客様のアプリケーションに返ります。

## <a name="initializing-applications"></a>アプリケーションの初期化

プレーンな JavaScript または Typescript アプリケーションでは、MSAL.js を次のように使用できます。 構成オブジェクトを使用して `UserAgentApplication` をインスタンス化することで、MSAL 認証コンテキストを初期化します。 MSAL.js を初期化するために最低限必要な構成は、ご利用のアプリケーションの clientID です。これは、アプリケーション登録ポータルから取得する必要があります。

リダイレクト フローを使用した認証メソッドの場合 (`loginRedirect` と `acquireTokenRedirect`)、`handleRedirectCallback()` メソッドを介して成功またはエラーに対するコールバックを明示的に登録する必要があります。 これが必要な理由は、ポップアップ エクスペリエンスを備えたメソッドとは違い、リダイレクト フローからは Promise が返されないことにあります。

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js は、`UserAgentApplication` の 1 つのインスタンスと構成によって、1 つの認証コンテキストを表すように設計されています。 複数のインスタンスは推奨されません。ブラウザー内でキャッシュ エントリおよび動作が競合する原因となるからです。

## <a name="configuration-options"></a>構成オプション

MSAL.js には、次に示す構成オブジェクトが含まれ、`UserAgentApplication` のインスタンスを作成する場合に使用できる構成可能なオプションがグループ分けされています。

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

構成オブジェクトで現在サポートされている構成可能なオプション セットの全体を次に示します。

- **clientID**: 必須。 ご利用のアプリケーションの clientID。これは、アプリケーション登録ポータルから取得する必要があります。

- **authority**: 省略可能。 MSAL からトークンを要求できるディレクトリを示す URL。 既定値は `https://login.microsoftonline.com/common` です。
    * Azure AD では、その形式は https://&lt;instance&gt;/&lt;audience&gt; となります。ここで、&lt;instance&gt; は ID プロバイダー ドメイン (たとえば、`https://login.microsoftonline.com`) であり、&lt;audience&gt; はサインイン対象ユーザーを表す識別子です。 次のいずれかの値とすることができます。
        * `https://login.microsoftonline.com/<tenant>` - tenant はテナント (contoso.onmicrosoft.com など) に、または特定の組織のユーザーにサインインするためにのみ使用されるディレクトリの `TenantID` プロパティを表す GUID に関連付けられているドメインです。
        * `https://login.microsoftonline.com/common` - 職場および学校のアカウント、または Microsoft の個人アカウントを持つユーザーにサインインする場合に使用されます。
        * `https://login.microsoftonline.com/organizations/` - 職場および学校のアカウントを持つユーザーにサインインする場合に使用されます。
        * `https://login.microsoftonline.com/consumers/` - 個人用の Microsoft アカウント (ライブ) のみを持つユーザーにサインインする場合に使用されます。
    * Azure AD B2C では、その形式は `https://<instance>/tfp/<tenant>/<policyName>/` となります。ここで、instance は Azure AD B2C ドメインです。tenant は Azure AD B2C テナントの名前です。policyName は適用する B2C ポリシーの名前です。


- **validateAuthority**: 省略可能。  トークンの発行者を検証します。 既定値は `true` です。 B2C アプリケーションの場合、機関の値は既知でありポリシーによって異なる可能性があるので、機関の検証は機能せず、必然的に `false` に設定されます。

- **redirectUri**: 省略可能。  アプリのリダイレクト URI。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録した URI のいずれかと完全に一致させる必要があります。 既定値は `window.location.href` です。

- **postLogoutRedirectUri**: 省略可能。  サインアウト後、ユーザーを `postLogoutRedirectUri` にリダイレクトします。既定では、 `redirectUri`です。

- **navigateToLoginRequestUrl**: 省略可能。 ログイン後に、スタート ページへの既定のナビゲーションをオフにする機能です。 既定値は true です。 これは、リダイレクト フローに対してのみ使用されます。

- **cacheLocation**: 省略可能。  ブラウザー ストレージを `localStorage` または `sessionStorage` のいずれかに設定します。 既定では、 `sessionStorage`です。

- **storeAuthStateInCookie**: 省略可能。  このフラグは、Microsoft Internet Explorer および Microsoft Edge 上での[認証ループの問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones)に対する修正プログラムとして MSAL.js v0.2.2 内に導入されました。 この修正プログラムを利用するには、フラグ `storeAuthStateInCookie` を true に設定して有効にします。 これが有効にされると、ブラウザーの cookie の認証フローを検証するために必要な認証要求の状態が MSAL.js によって保存されます。 既定では、このフラグは `false` に設定されます。

- **logger**: 省略可能。  ユーザー設定の方法でログを使用および公開するために開発者が指定できるコールバック インスタンスを備えたロガー オブジェクト。 ロガー オブジェクトを渡す方法の詳細については、[msal.js によるログ記録](msal-logging.md)に関するページを参照してください。

- **loadFrameTimeout**: 省略可能。  Azure AD からのトークン更新応答をタイムアウトと見なすまでの非アクティビティ状態のミリ秒数。既定値は 6 秒です

- **tokenRenewalOffsetSeconds**: 省略可能。 有効期限の前にトークンを更新するために必要なオフセットのウィンドウを設定するミリ秒数。 既定値は 300 ミリ秒です。

- **navigateFrameWait**:省略可能。 非表示の iframe が移動先に移動するまでの待機時間を設定するミリ秒数。 既定値は 500 ミリ秒です。

以下は、MSAL Angular ラッパー ライブラリから渡される場合にのみ適用可能です。
- **unprotectedResources**: 省略可能。  保護されていないリソースである URI の配列です。 MSAL では、これらの URI を含む送信要求にトークンは添付されません。 既定値は `null` です。

- **protectedResourceMap**: 省略可能。  これは MSAL によって使用されるスコープへのリソースのマッピングであり、Web API 呼び出しにアクセス トークンを自動的に添付する場合に行われます。 リソース用に 1 つのアクセス トークンが取得されます。 このため、特定のリソース パスを {"https://graph.microsoft.com/v1.0/me", ["user.read"]} のようにマップすることも、リソースのアプリ URL を {"https://graph.microsoft.com/", ["user.read", "mail.send"]} のようにマップすることもできます。 これは、CORS 呼び出しのために必要です。 既定値は `null` です。
