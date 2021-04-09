---
title: 認証要求内でカスタムの状態を渡す (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript (MSAL.js) 用 Microsoft 認証ライブラリを使用して認証要求内でカスタムの状態パラメーターの値を渡す方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063673"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>MSAL.js を使用して認証要求内でカスタムの状態を渡す

OAuth 2.0 で定義されているように、"*状態*" パラメーターは認証要求に含まれ、クロスサイト リクエスト フォージェリ攻撃を防ぐためにもトークン応答で返されます。 既定では、JavaScript 用 Microsoft Authentication Library (MSAL.js) により、ランダムに生成された一意の "*状態*" パラメーター値が認証要求で渡されます。

状態パラメーターは、リダイレクト前に、アプリの状態情報をエンコードするために使用することもできます。 アプリ内のユーザーの状態 (ユーザーがいたページまたはビューなど) を、このパラメーターへの入力として渡すことができます。 MSAL.js ライブラリを使用すると、カスタムの状態を `Request` オブジェクトの状態パラメーターとして渡すことができます。

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> キャッシュされたトークンをスキップしてサーバーに移動する場合は、ブール型の `forceRefresh` を、ログインまたはトークンの要求を行うために使用する AuthenticationParameters オブジェクトに渡してください。
> `forceRefresh` は、アプリケーションのパフォーマンスに影響するため、既定では使用しないでください。
> キャッシュに依存すると、ユーザーのエクスペリエンスが向上します。
> キャッシュのスキップは、現在キャッシュされているデータに最新の情報がないことがわかっている場合にのみ使用する必要があります。
> たとえば、更新されたロールで新しいトークンを取得する必要があるユーザーに対して、ロールを追加する管理ツールなどです。

次に例を示します。

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

渡された状態は、要求の送信時に、MSAL.js によって設定される一意の GUID に追加されます。 応答が返されると、MSAL.js により状態が一致しているかどうか確認され、`Response` オブジェクトでカスタムの渡された状態が `accountState` として返されます。

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

詳細については、MSAL.js を使用した[シングル ページ アプリケーション (SPA) のビルド](scenario-spa-overview.md)に関する記事をご覧ください。