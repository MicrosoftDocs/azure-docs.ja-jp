---
title: トークンの管理 (Microsoft Authentication Library) | Azure
description: Microsoft Authentication Library (MSAL) を使用したトークンの取得とキャッシュについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f7914744073f82d8a35d3679a1c65459e10b2f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532908"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>MSAL を使用したトークンの取得とキャッシュ
[アクセス トークン](access-tokens.md)により、クライアントは Azure によって保護された Web API を安全に呼び出すことができます。 Microsoft Authentication Library (MSAL) を使用してトークンを取得する方法はたくさんあります。 一部の方法では、Web ブラウザーを通じたユーザー操作が必要です。 ユーザー操作の必要がない方法もあります。 一般に、トークンを取得する方法は、アプリケーションがパブリック クライアント アプリケーション (デスクトップ アプリまたはモバイル アプリ) か、機密クライアント アプリケーション (Web アプリ、Web API、または Windows サービスのようなデーモン アプリケーション) かによって異なります。

MSAL では、トークンは取得された後でキャッシュされます。  アプリケーション コードでは、他の手段でトークンを取得する前に、まず、(キャッシュからの) トークンの自動的な取得を試みる必要があります。

また、トークン キャッシュをクリアすることもできます。これは、キャッシュからアカウントを削除することによって行います。 ただし、これによってブラウザーにあるセッション Cookie が削除されることはありません。

## <a name="scopes-when-acquiring-tokens"></a>トークンを取得するときのスコープ
[スコープ](v2-permissions-and-consent.md)は、アクセスを要求するクライアント アプリケーションのために Web API によって公開されているアクセス許可です。 クライアント アプリケーションでは、Web API にアクセスするためのトークンを取得するために認証要求を行うとき、これらのスコープに対するユーザーの同意を要求します。 MSAL では、開発者向け Azure AD (v1.0) と Microsoft ID プラットフォーム (v2.0) の API にアクセスするためのトークンを取得できます。 v2.0 プロトコルでは、リソースではなくスコープが要求で使用されます。 詳細については、[v1.0 と v2.0 の比較](active-directory-v2-compare.md)に関する記事を参照してください。 受け付けるトークンのバージョンに関する Web API の構成に基づいて、v2.0 エンドポイントから MSAL にアクセス トークンが返されます。

MSAL のトークン取得メソッドの多くでは、*scopes* パラメーターが必要です。 このパラメーターは、要求される必要なアクセス許可とリソースを宣言する文字列のシンプルなリストです。 よく知られたスコープとしては、[Microsoft Graph アクセス許可](/graph/permissions-reference)があります。

MSAL で v1.0 のリソースにアクセスすることもできます。 詳細については、[Scopes for a v1.0 application (v1.0 アプリケーションのスコープ)](msal-v1-app-scopes.md) に関する記事を参照してください。

### <a name="request-specific-scopes-for-a-web-api"></a>Web API に対して特定のスコープを要求する
アプリケーションでリソース API に対する特定のアクセス許可を備えたトークンを要求する必要がある場合、API のアプリ ID URI を含むスコープを、 *&lt;アプリ ID URI&gt;/&lt;スコープ&gt;* という形式で渡すことが必要になります

たとえば、Microsoft Graph API のスコープは `https://graph.microsoft.com/User.Read` です

または、たとえばカスタム Web API のスコープは `api://abscdefgh-1234-abcd-efgh-1234567890/api.read` です

Microsoft Graph API の場合のみ、スコープの値 `user.read` は `https://graph.microsoft.com/User.Read` という形式にマップされ、区別なく使用できます。

> [!NOTE]
> Azure Resource Manager API (https://management.core.windows.net/) などの特定の Web API では、アクセス トークンのオーディエンス クレーム (aud) で末尾の "/" が必要です。 この場合、トークンが API で有効になるためには、スコープを https://management.core.windows.net//user_impersonation (二重スラッシュに注意) として渡すことが重要です。

### <a name="request-dynamic-scopes-for-incremental-consent"></a>増分同意のために動的スコープを要求する
v1.0 を使用してアプリケーションを構築するときは、ユーザーがログイン時に同意するよう、アプリケーションで必要なアクセス許可 (静的スコープ) の完全なセットを登録する必要がありました。 v2.0 では、scope パラメーターを使用して、必要に応じて追加のアクセス許可を要求できます。 これらは動的スコープと呼ばれ、ユーザーはスコープに増分同意を提供できます。

たとえば、最初はすべての種類のアクセスを拒否してユーザーをサインインさせることができます。 その後、トークン取得メソッドで予定表のスコープを要求してユーザーの同意を得ることにより、ユーザーの予定表を読み取る機能を提供できます。

例: `https://graph.microsoft.com/User.Read` と `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>(キャッシュからの) トークンの自動的な取得
MSAL は、1 つのトークン キャッシュ (または、機密クライアント アプリケーションの場合は 2 つのキャッシュ) を保持しており、取得した後のトークンをキャッシュします。  多くの場合、トークンを自動的に取得しようとすると、キャッシュ内のトークンに基づいて、より多くのスコープを備える別のトークンが取得されます。 また、期限切れが近いトークンを更新することもできます (トークン キャッシュには更新トークンも含まれるため)。

### <a name="recommended-call-pattern-for-public-client-applications"></a>パブリック クライアント アプリケーションの推奨される呼び出しパターン
アプリケーション コードでは、まず、(キャッシュからの) トークンの自動的な取得を試みる必要があります。  メソッドの呼び出しで "UI が必要" エラーまたは例外が返される場合、他の手段でトークンの取得を試みます。 

ただし、次の 2 つのフローについては、それより前にトークンの自動的な取得を試みる**べきではありません**。

- [クライアント資格情報フロー](msal-authentication-flows.md#client-credentials)では、ユーザー トークン キャッシュは使用されず、アプリケーション トークン キャッシュが使用されます。 この方法では、STS に要求を送信する前に、このアプリケーション トークン キャッシュの確認が行われます。
- Web アプリの[承認コード フロー](msal-authentication-flows.md#authorization-code)では、ユーザーをサインインさせることでアプリケーションが取得したコードを引き換えて、より多くのスコープに同意させます。 コードがパラメーターとして渡されるため、また、アカウントでないため、メソッドはコードを引き換える前にキャッシュを参照することができません。そのため、いずれにしてもサービスの呼び出しが必要です。

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>承認コード フローが使用される Web アプリで推奨される呼び出しパターン 
[OpenID Connect 承認コード フロー](v2-protocols-oidc.md)が使用される Web アプリケーションの場合、コントローラーで推奨されるパターンは次のとおりです。

- カスタマイズされたシリアル化を使用してトークン キャッシュで機密クライアント アプリケーションをインスタンス化します。 
- 承認コード フローを使用してトークンを取得します

## <a name="acquiring-tokens"></a>トークンの取得
一般に、トークンを取得する方法は、パブリック クライアント アプリケーションか機密クライアント アプリケーションかによって決まります。

### <a name="public-client-applications"></a>パブリック クライアント アプリケーション
パブリック クライアント アプリケーション (デスクトップまたはモバイルのアプリ) の場合は、次のようにします。
- 多くの場合、UI またはポップアップ ウィンドウを使用してユーザーをサインインさせ、対話形式でトークンを取得します。
- ドメインまたは Azure に参加済みの Windows コンピューターでデスクトップ アプリケーションが実行されている場合、統合 Windows 認証 (IWA および Kerberos) を使用して、[サインインしたユーザーのトークンを自動的に取得](msal-authentication-flows.md#integrated-windows-authentication)できます。
- .NET Framework デスクトップ クライアント アプリケーションでは、[ユーザー名とパスワードを使ってトークンを取得](msal-authentication-flows.md#usernamepassword)できますが、これは推奨されません。 機密クライアント アプリケーションでは、ユーザー名とパスワードを使わないでください。
- Web ブラウザーがないデバイスで実行されているアプリケーションでは、[デバイス コード フロー](msal-authentication-flows.md#device-code)によってトークンを取得できます。 ユーザーは URL とコードを提供された後、別のデバイスの Web ブラウザーに移動し、コードを入力してサインインします。  その後、Azure AD はブラウザーのないデバイスにトークンを送り返します。

### <a name="confidential-client-applications"></a>機密クライアント アプリケーション 
機密クライアント アプリケーション (Web アプリ、Web API、または Windows サービスなどのデーモン アプリケーション) の場合は、次のようにします。
- [クライアント資格情報フロー](msal-authentication-flows.md#client-credentials)を使用して、ユーザーではなく**アプリケーション自体に対する**トークンを取得します。 これは、同期ツールに対して、または特定のユーザーではなくユーザー一般を処理するツールに対して、使用できます。 
- ユーザーに代わって API を呼び出す Web API に対しては、[On-Behalf-Of フロー](msal-authentication-flows.md#on-behalf-of)を使用します。 ユーザー アサーションに基づいてトークンを取得するため、アプリケーションはクライアントの資格情報で識別されます (たとえば、SAML または JWT トークン)。 このフローは、サービス間呼び出しで特定のユーザーのリソースにアクセスする必要があるアプリケーションによって使用されます。
- Web アプリでは、ユーザーが承認要求 URL でサインインした後、[承認コード フロー](msal-authentication-flows.md#authorization-code)を使用してトークンを取得します。 OpenID Connect アプリケーションでは、通常、このメカニズム (ユーザーは OpenID Connect を使用してサインイン可能) を使用してから、ユーザーに代わって Web API にアクセスします。


## <a name="authentication-results"></a>認証の結果 
クライアントがアクセス トークンを要求すると、Azure AD はアクセス トークンに関するメタデータを含む認証結果も返します。 この情報には、アクセス トークンの有効期限や、それが有効なスコープが含まれます。 このデータを使用すると、アプリはアクセス トークン自体を解析しなくても、そのアクセス トークンのインテリジェントなキャッシュを実行できます。  認証結果では以下が公開されます。

- Web API がリソースにアクセスするための[アクセス トークン](access-tokens.md)。 これは文字列で、通常は base64 でエンコードされた JWT ですが、クライアントがアクセス トークン内を参照することはありません。 形式が変わらないことは保証されておらず、リソース用に暗号化できます。 クライアント上のアクセス トークンのコンテンツに応じてコードを記述している人は、エラーとクライアント ロジックの中断を起こす最大の原因の 1 つです。
- ユーザーの [ID トークン](id-tokens.md) (これは JWT です)。
- トークンの有効期限。トークンの有効期限が切れる日付と時刻を示します。
- テナント ID には、ユーザーが見つかったテナントが含まれています。 ゲスト ユーザー (Azure AD B2B のシナリオ) の場合、テナント ID は一意のテナントではなく、ゲスト テナントです。 トークンがユーザーの名前で提供されると、認証結果にはこのユーザーに関する情報も含まれます。 (アプリケーションの) ユーザーなしでトークンが要求される機密クライアント フローの場合、このユーザー情報は null です。
- トークンが発行されたスコープ。
- ユーザーの一意の ID。

## <a name="next-steps"></a>次の手順
[エラーと例外の処理](msal-handling-exceptions.md)について学習します。 
