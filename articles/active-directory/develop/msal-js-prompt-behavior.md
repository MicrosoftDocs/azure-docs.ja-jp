---
title: 対話型要求のプロンプト動作 (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を使用して、対話的な呼び出しでのプロンプト動作をカスタマイズする方法について説明します。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85477551"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js の対話型要求におけるプロンプト動作

あるユーザーが複数のユーザー アカウントを使用してアクティブな Azure AD セッションを確立した場合、Azure AD のサインイン ページでは既定で、サインインへ進む前に1 つのアカウントを選択するよう求めるプロンプトをユーザーに表示します。 Azure AD での認証済みセッションが 1 つしかない場合は、アカウント選択のエクスペリエンスはユーザーに表示されません。

MSAL.js ライブラリ (v0.2.4 以降) では、対話型の要求 (`loginRedirect`、 `loginPopup`、`acquireTokenRedirect`、および `acquireTokenPopup`) の中でプロンプト パラメーターを送信しないため、プロンプト動作が発生することはありません。 `acquireTokenSilent` メソッドを使用したサイレント トークンの要求では、MSAL.js は `none` に設定されたプロンプト パラメーターを渡します。

お使いのアプリケーション シナリオに基づいて、メソッドに渡される要求パラメーター内にプロンプト パラメーターを設定することで、対話型の要求でのプロンプト動作を制御できます。 たとえば、アカウント選択のエクスペリエンスを起動するとします。

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Azure AD での認証時に、次のプロンプト値が渡されます。

**login:** 認証要求時に資格情報を入力することをユーザーに強制します。

**select_account:** セッション内のすべてのアカウントを一覧表示するアカウント選択エクスペリエンスをユーザーに提供します。

**consent:** ユーザーがアプリへのアクセス許可を付与できる OAuth 承認のダイアログを起動します。

**none:** ユーザーに対話型のプロンプトが表示されなくなります。 予期しない動作が発生する可能性があるため、この値を MSAL.js 内の対話型メソッドに渡すことはお勧めしません。 代わりに、`acquireTokenSilent` メソッドを使用して、サイレント呼び出しを実現します。

## <a name="next-steps"></a>次のステップ

`prompt`MSAL.js ライブラリで使用される [OAuth 2.0 の暗黙的な許可](v2-oauth2-implicit-grant-flow.md)のプロトコルに関する詳細を確認してください。
