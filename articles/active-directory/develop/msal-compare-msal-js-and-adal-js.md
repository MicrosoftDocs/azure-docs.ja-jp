---
title: MSAL.js と ADAL.js の相違点 |Azure
description: Microsoft Authentication Library for JavaScript (MSAL.js) と Azure AD Authentication Library for JavaScript (ADAL.js) の違いと、どちらを使用するかを選択する方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 328db116cebda5eb288f04cc89e2c85550c38083
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421195"
---
# <a name="differences-between-msal-js-and-adal-js"></a>MSAL JS と ADAL JS の相違点

Azure AD エンティティを認証し、Azure AD からのトークンを要求する場合、Microsoft Authentication Library for JavaScript (MSAL.js) と Azure AD Authentication Library for JavaScript (ADAL.js) の両方が使用されます。 これまで、ほとんどの開発者は、開発者用の Azure AD (v1.0) で、ADAL を使用してトークンを要求することで、Azure AD ID (職場と学校のアカウント) を認証していました。 現在では、MSAL.js を使用して、より広範な Microsoft ID (Azure AD の ID と Microsoft アカウント、および Azure AD B2C 経由のソーシャル アカウントとローカル アカウント) を Microsoft ID プラットフォーム (v2.0) を介して認証することができます。

この記事では、Microsoft Authentication Library for JavaScript (MSAL.js) と Azure AD Authentication Library for JavaScript (ADAL.js) のいずれかを選択する方法について説明し、2 つのライブラリを比較します。

## <a name="choosing-between-adaljs-and-msaljs"></a>ADAL.js と MSAL.js の選択

ほとんどの場合、Microsoft ID プラットフォームと MSAL.js (最新世代の Microsoft 認証ライブラリ) を使用します。 MSAL.js を使用して、Azure AD (職場と学校のアカウント)、Microsoft (個人用) アカウント (MSA)、または Azure AD B2C でアプリケーションにサインインしているユーザーのためにトークンを取得します。

v1.0 エンドポイント (および ADAL.js) を既に使い慣れている場合は、[v2.0 エンドポイントの変更点](active-directory-v2-compare.md)に関するページをお読みください。

ただし、アプリケーションで、以前のバージョンの [Active Directory フェデレーション サービス (ADFS)](/windows-server/identity/active-directory-federation-services) を使用してユーザーをサインインさせる必要がある場合は、引き続き ADAL.js を使用する必要があります。

## <a name="key-differences-in-authentication-with-msaljs"></a>MSAL.js を使用した認証での主な相違点

### <a name="core-api"></a>Core API

* ADAL.js は、機関 URL を介したアプリケーションから認証サーバーまたは ID プロバイダーへの接続のインスタンスの表現として、[AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) を使用します。 反対に、MSAL.js API は、ユーザー エージェント クライアント アプリケーションを中心に設計されています (Web ブラウザーなどのユーザー エージェントでクライアント コードが実行されるパブリック クライアント アプリケーションの形式)。 承認サーバーで、アプリケーションの認証コンテキストのインスタンスを表す `UserAgentApplication` クラスを提供します。 詳細については、[MSAL.js を使用した初期化](msal-js-initializing-client-applications.md)に関するページを参照してください。

* ADAL.js では、トークンを取得するメソッドは、`AuthenticationContext` で設定された単一の機関と関連付けられます。 MSAL.js では、取得トークン要求は、`UserAgentApplication` で設定されている値とは異なる機関の値を取る場合があります。 これにより、MSAL.js は、同じアプリケーション内の複数のテナントとユーザー アカウントに対して別々にトークンを取得してキャッシュできます。

* ユーザーにプロンプトを表示することなく、自動的にトークンを取得して更新するメソッドには、ADAL.js で `acquireToken` という名前が付けられています。 MSAL.js では、このメソッドには、この機能を説明した `acquireTokenSilent` という名前が付けられています。

### <a name="authority-value-common"></a>機関の値 `common`

V1.0 では、`https://login.microsoftonline.com/common` 機関を使用すると、ユーザーは Azure AD アカウント (任意の組織用) でサインインできるようになります。

v2.0 では、`https://login.microsoftonline.com/common` 機関を使用すると、ユーザーは Azure AD 組織アカウントや Microsoft の個人用アカウント (MSA) でサインインできるようになります。 サインインを Azure AD アカウント (ADAL.js の場合と同じ動作) だけに制限するには、`https://login.microsoftonline.com/organizations` を使用する必要があります。 詳細については、[MSAL.js を使用した初期化](msal-js-initializing-client-applications.md)に関するページの `authority` config オプションを参照してください。

### <a name="scopes-for-acquiring-tokens"></a>トークンを取得するためのスコープ
* トークンを取得するための認証要求におけるリソース パラメーターに代わるスコープ

    v2.0 プロトコルでは、リソースではなくスコープが要求で使用されます。 つまり、アプリケーションが、MS Graph などのリソースのアクセス許可でトークンを要求する必要がある場合、ライブラリ メソッドに渡される値における差異は次のとおりです。

    v1.0: resource = https\://graph.microsoft.com

    v2.0: scope = https\://graph.microsoft.com/User.Read

    任意のリソース API のスコープを要求するには、この API の URI を appidURI/scope の形式 (例: https:\//mytenant.onmicrosoft.com/myapi/api.read) で使用します

    MS Graph API の場合のみ、スコープの値 `user.read` は https://graph.microsoft.com/User.Read にマップされ、区別なく使用できます。

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* 増分同意のための動的スコープ。

    v1.0 を使用してアプリケーションを構築するときは、ユーザーがログイン時に同意するよう、アプリケーションで必要なアクセス許可 (静的スコープ) の完全なセットを登録する必要がありました。 V2.0 では、スコープ パラメーターを使用して、必要なときにアクセス許可を要求できます。 これらを動的スコープといいます。 これにより、ユーザーは、スコープに増分同意を提供できます。 最初ユーザーにはアプリケーションへのサインインだけを行わせ、どのような種類のアクセスも必要としない場合、そうすることができます。 その後、ユーザーの予定表を読み取る機能が必要になった場合は、acquireToken メソッドで予定表のスコープを要求してユーザーの同意を得ることができます。 例:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* V1.0 API 用のスコープ

    MSAL.js を使用して、V1.0 API のトークンを取得する場合は、スコープとして `.default` を API のアプリケーション ID URI に付加することで、API に登録されているすべての静的スコープを要求できます。 例:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>次の手順
詳細については、[v1.0 と v2.0 の比較](active-directory-v2-compare.md)に関するページを参照してください。
