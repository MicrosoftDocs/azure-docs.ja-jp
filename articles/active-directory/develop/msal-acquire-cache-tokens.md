---
title: Microsoft Authentication Library (MSAL) を使用してトークンを取得しキャッシュする | Azure
titleSuffix: Microsoft identity platform
description: MSAL を使用したトークンの取得とキャッシュについて説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 98ae81626db637f5b0bd6bfe9e294c32293d09e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755074"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) を使用してトークンを取得し、キャッシュする

[アクセス トークン](access-tokens.md)により、クライアントは Azure によって保護された Web API を安全に呼び出すことができます。 Microsoft Authentication Library (MSAL) を使用してトークンを取得するには、いくつかの方法があります。 Web ブラウザーを使用したユーザー操作が必要なものもあれば、ユーザーの操作を必要としないものもあります。 一般に、トークンを取得するために使用される方法は、アプリケーションがパブリック クライアント アプリケーション (デスクトップ アプリまたはモバイル アプリ) か、機密クライアント アプリケーション (Web アプリ、Web API、またはデーモン アプリケーション) かによって異なります。

MSAL では、トークンは取得された後でキャッシュされます。 アプリケーション コードでは、他の手段でトークンの取得を試行する前に、まず、キャッシュからのトークンの自動的な取得を試みる必要があります。

また、トークン キャッシュをクリアすることもできます。これは、キャッシュからアカウントを削除することによって行います。 ただし、これによってブラウザーにあるセッション Cookie が削除されることはありません。

## <a name="scopes-when-acquiring-tokens"></a>トークンを取得するときのスコープ

[スコープ](v2-permissions-and-consent.md)は、クライアント アプリケーションがアクセスを要求できる、Web API によって公開されているアクセス許可です。 クライアント アプリケーションでは、Web API にアクセスするためのトークンを取得するために認証要求を行うとき、これらのスコープに対するユーザーの同意を要求します。 MSAL では、開発者向け Azure AD (v1.0) と Microsoft ID プラットフォームの API にアクセスするためのトークンを取得できます。 v2.0 プロトコルでは、リソースではなくスコープが要求で使用されます。 詳細については、[v1.0 と v2.0 の比較](../azuread-dev/azure-ad-endpoint-comparison.md)に関する記事を参照してください。 受け付けるトークンのバージョンに関する Web API の構成に基づいて、v2.0 エンドポイントから MSAL にアクセス トークンが返されます。

いくつかの MSAL のトークン取得方法には、`scopes` パラメーターが必要です。 `scopes` パラメーターは、要求された必要とするアクセス許可とリソースを宣言する文字列のリストです。 よく知られたスコープとしては、[Microsoft Graph アクセス許可](/graph/permissions-reference)があります。

MSAL で v1.0 のリソースにアクセスすることもできます。 詳細については、[v1.0 アプリケーションのスコープ](msal-v1-app-scopes.md)に関する記事を参照してください。

### <a name="request-scopes-for-a-web-api"></a>Web API のスコープを要求する

アプリケーションでリソース API に対する特定のアクセス許可を備えたアクセス トークンを要求する必要がある場合、API のアプリ ID URI を含むスコープを、`<app ID URI>/<scope>` 形式で渡します。

さまざまなリソースのスコープ値の例を次に示します。

- Microsoft Graph API: `https://graph.microsoft.com/User.Read`
- カスタム Web API: `api://11111111-1111-1111-1111-111111111111/api.read`

スコープ値の形式は、アクセス トークンを受け取るリソース (API) と、それが受け入れる `aud` 要求の値によって異なります。

Microsoft Graph の場合のみ、`user.read` スコープは `https://graph.microsoft.com/User.Read` にマップされ、両方のスコープ形式を同じ意味で使用できます。

Azure Resource Manager API (https://management.core.windows.net/) などの特定の Web API では、アクセス トークンのオーディエンス クレーム (`aud` ) で末尾のスラッシュ ('/') が必要です。 この場合は、二重スラッシュ ('//') を含めて、スコープを `https://management.core.windows.net//user_impersonation` として渡します。

その他の API では、スコープ値に *スキームやホストが含まれない* ことが必要になる場合があり、アプリ ID (GUID) とスコープ名のみを想定する場合もあります。次に例を示します。

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> ダウンストリーム リソースが制御下にない場合、アクセス トークンをリソースに渡すときに `401` またはその他のエラーが発生した場合は、異なるスコープ値の形式 (たとえば、スキームとホストを含めたり省略したりする) を試すことが必要な場合もあります。

### <a name="request-dynamic-scopes-for-incremental-consent"></a>増分同意のために動的スコープを要求する

アプリケーションによって提供される機能やその要件が変更されると、スコープ パラメーターを使用して、必要に応じて追加のアクセス許可を要求できます。 このような *動的スコープ* を使用すると、ユーザーはスコープに対する増分の同意を行うことができ ます。

たとえば、ユーザーをサインインさせますが、最初はすべてのリソースへのアクセスを拒否します。 その後、トークン取得メソッドで予定表のスコープを要求して、そうすることへのユーザーの同意を取得することにより、ユーザーの予定表を表示する機能を提供できます。 たとえば、`https://graph.microsoft.com/User.Read` と `https://graph.microsoft.com/Calendar.Read` のスコープを要求して行います。

## <a name="acquiring-tokens-silently-from-the-cache"></a>(キャッシュからの) トークンの自動的な取得

MSAL は、1 つのトークン キャッシュ (または、機密クライアント アプリケーションの場合は 2 つのキャッシュ) を保持しており、取得した後のトークンをキャッシュします。 多くの場合、トークンを自動的に取得しようとすると、キャッシュ内のトークンに基づいて、より多くのスコープを備える別のトークンが取得されます。 また、期限切れが近いトークンを更新することもできます (トークン キャッシュには更新トークンも含まれるため)。

### <a name="recommended-call-pattern-for-public-client-applications"></a>パブリック クライアント アプリケーションの推奨される呼び出しパターン

アプリケーション コードでは、まず、キャッシュからのトークンの自動的な取得を試みる必要があります。 メソッドの呼び出しで "UI が必要" エラーまたは例外が返される場合、他の手段でトークンの取得を試みます。

ただし、次の 2 つのフローについては、それより前にトークンの自動的な取得を試みる **べきではありません**。

- [クライアント資格情報フロー](msal-authentication-flows.md#client-credentials)では、ユーザー トークン キャッシュは使用されず、アプリケーション トークン キャッシュが使用されます。 この方法では、セキュリティ トークン サービス (STS) に要求を送信する前に、このアプリケーション トークン キャッシュの確認が行われます。
- Web アプリの[承認コード フロー](msal-authentication-flows.md#authorization-code)では、ユーザーをサインインさせることでアプリケーションが取得したコードを引き換えて、より多くのスコープに同意させます。 (アカウントでなく) コードがパラメーターとして渡されるため、メソッドはコードを引き換える前にキャッシュを参照することができません。そのため、サービスの呼び出しを起動します。

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>承認コード フローが使用される Web アプリで推奨される呼び出しパターン

[OpenID Connect 承認コード フロー](v2-protocols-oidc.md)が使用される Web アプリケーションの場合、コントローラーで推奨されるパターンは次のとおりです。

- カスタマイズされたシリアル化を使用してトークン キャッシュで機密クライアント アプリケーションをインスタンス化します。
- 承認コード フローを使用してトークンを取得します

## <a name="acquiring-tokens"></a>トークンの取得

一般に、トークンを取得する方法は、アプリケーションがパブリック クライアントか機密クライアントかによって決まります。

### <a name="public-client-applications"></a>パブリック クライアント アプリケーション

パブリック クライアント アプリケーション (デスクトップまたはモバイルのアプリ) の場合は、次のようにします。

- 多くの場合、UI またはポップアップ ウィンドウを使用してユーザーをサインインさせ、対話形式でトークンを取得します。
- ドメインまたは Azure に参加済みの Windows コンピューターでデスクトップ アプリケーションが実行されている場合、統合 Windows 認証 (IWA および Kerberos) を使用して、[サインインしたユーザーのトークンを自動的に取得](msal-authentication-flows.md#integrated-windows-authentication)できます。
- .NET Framework デスクトップ クライアント アプリケーションでは、[ユーザー名とパスワードを使ってトークンを取得](msal-authentication-flows.md#usernamepassword)できます (推奨されません)。 機密クライアント アプリケーションでは、ユーザー名とパスワードを使わないでください。
- Web ブラウザーがないデバイスで実行されているアプリケーションでは、[デバイス コード フロー](msal-authentication-flows.md#device-code)によってトークンを取得できます。 ユーザーは URL とコードを提供された後、別のデバイスの Web ブラウザーに移動し、コードを入力してサインインします。 その後、Azure AD はブラウザーのないデバイスにトークンを送り返します。

### <a name="confidential-client-applications"></a>機密クライアント アプリケーション

機密クライアント アプリケーション (Web アプリ、Web API、または Windows サービスなどのデーモン アプリケーション) の場合は、次のようにします。

- [クライアント資格情報フロー](msal-authentication-flows.md#client-credentials)を使用して、ユーザーではなく **アプリケーション自体に対する** トークンを取得します。 この手法は、同期ツールに対して、または特定のユーザーではなくユーザー一般を処理するツールに対して、使用できます。
- ユーザーに代わって API を呼び出す Web API に対しては、[On-Behalf-Of フロー](msal-authentication-flows.md#on-behalf-of)を使用します。 ユーザー アサーションに基づいてトークンを取得するため、アプリケーションはクライアントの資格情報で識別されます (たとえば、SAML または JWT トークン)。 このフローは、サービス間呼び出しで特定のユーザーのリソースにアクセスする必要があるアプリケーションによって使用されます。
- Web アプリでは、ユーザーが承認要求 URL でサインインした後、[承認コード フロー](msal-authentication-flows.md#authorization-code)を使用してトークンを取得します。 OpenID Connect アプリケーションでは、通常、このメカニズム (ユーザーは OpenID Connect を使用してサインイン可能) を使用してから、ユーザーに代わって Web API にアクセスします。

## <a name="authentication-results"></a>認証の結果

クライアントがアクセス トークンを要求すると、Azure AD はアクセス トークンに関するメタデータを含む認証結果も返します。 この情報には、アクセス トークンの有効期限や、それが有効なスコープが含まれます。 このデータを使用すると、アプリはアクセス トークン自体を解析しなくても、そのアクセス トークンのインテリジェントなキャッシュを実行できます。 認証結果では以下が公開されます。

- Web API がリソースにアクセスするための[アクセス トークン](access-tokens.md)。 この文字列は、通常は Base64 でエンコードされた JWT ですが、クライアントがアクセス トークン内を参照することはありません。 この形式が変わらないことは保証されておらず、リソース用に暗号化できます。 クライアント上のアクセス トークンのコンテンツに応じてコードを記述している人は、エラーとクライアント ロジックの中断を起こす最も一般的な原因の 1 つです。
- ユーザーの [ID トークン](id-tokens.md) (JWT)。
- トークンの有効期限。トークンの有効期限が切れる日付と時刻を示します。
- テナント ID には、ユーザーが見つかったテナントが含まれています。 ゲスト ユーザー (Azure AD B2B のシナリオ) の場合、テナント ID は一意のテナントではなく、ゲスト テナントです。 トークンがユーザーの名前で提供されると、認証結果にはこのユーザーに関する情報も含まれます。 (アプリケーションの) ユーザーなしでトークンが要求される機密クライアント フローの場合、このユーザー情報は null です。
- トークンが発行されたスコープ。
- ユーザーの一意の ID。

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>(上級) バックグラウンドのアプリやサービスで、キャッシュされたユーザーのトークンにアクセスする

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>次のステップ

MSAL でサポートされるプラットフォームの中には、そのプラットフォームのライブラリのドキュメントに追加のトークン キャッシュ関連情報が含まれているものがあります。 例:
- [MSAL.NET を使用してトークン キャッシュからトークンを取得する](msal-net-acquire-token-silently.md)
- [MSAL.js でのシングル サインオン](msal-js-sso.md)
- [MSAL for Python でのカスタム トークン キャッシュのシリアル化](msal-python-token-cache-serialization.md)
- [MSAL for Java でのカスタム トークン キャッシュのシリアル化](msal-java-token-cache-serialization.md)
