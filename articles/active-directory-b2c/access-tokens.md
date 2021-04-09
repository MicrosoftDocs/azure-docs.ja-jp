---
title: アクセス トークンの要求 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C にアクセス トークンを要求する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5168d5e5e3935da267fb26f38735a88bdfd7837
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654478"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でのアクセス トークンの要求

*アクセス トークン* には、Azure Active Directory B2C (Azure AD B2C) で API に付与されているアクセス許可を識別するために使用できる要求が含まれています。 リソース サーバーを呼び出すときは、HTTP 要求でアクセス トークンを提示する必要があります。 アクセス トークンは、Azure AD B2C からの応答で、**access_token** として示されます。

この記事では、Web アプリケーションと Web API に対してアクセス トークンを要求する方法を示します。 Azure AD B2C でのトークンの詳細については、「[Overview of tokens in Azure Active Directory B2C (Azure Active Directory B2C でのトークンの概要)](tokens-overview.md)」を参照してください。

> [!NOTE]
> **Web API チェーン (On-Behalf-Of) は Azure AD B2C でサポートされていません。** 多くのアーキテクチャには、別のダウンストリーム Web API を呼び出す必要がある Web API が含まれており、どちらも、Azure AD B2C によってセキュリティ保護されています。 このシナリオは、Web API バック エンドのある (つまり、別のサービスを呼び出す) クライアントで一般的なものです。 このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 JWT Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートできます。 ただし、現時点では、Azure AD B2C に On-Behalf-Of フローは実装されていません。

## <a name="prerequisites"></a>前提条件

- [ユーザー フローを作成](tutorial-create-user-flows.md)して、ユーザーがアプリケーションにサインアップおよびサインインできるようにします。
- まだそうしていない場合は、[Azure Active Directory B2C テナントに Web API アプリケーションを追加](add-web-api-application.md)します。

## <a name="scopes"></a>スコープ

スコープを使用すると、保護されたリソースへのアクセス許可を管理できます。 アクセス トークンが要求されると、クライアント アプリケーションでは、必要なアクセス許可を要求の **scope** パラメーターに指定する必要があります。 たとえば、`https://contoso.onmicrosoft.com/api` の **[アプリケーション ID/URI]** を備える API に **スコープ値**`read` を指定する場合、スコープは `https://contoso.onmicrosoft.com/api/read` になります。

スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば Web API のユーザーが、読み取りと書き込みの両方のアクセス権限を持つ場合もあれば、読み取りアクセス権限しか持たない場合もあります。 同じ要求で複数のアクセス許可を取得するには、スペースで区切って複数のエントリを要求の 1 つの **scope** パラメーターに追加します。

次の例は、URL 内のデコードされたスコープを示しています。

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

次の例は、URL 内のエンコードされたスコープを示しています。

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

クライアント アプリケーションに対して許可されているよりも多くのスコープを要求した場合、少なくとも 1 つのアクセス許可が与えられれば、呼び出しは成功します。 返されるアクセス トークンの **scp** 要求には、正常に付与されたアクセス許可だけが設定されます。 

### <a name="openid-connect-scopes"></a>OpenID Connect のスコープ

OpenID Connect 標準では、いくつかの特別な scope 値が指定されます。 以下のスコープは、ユーザーのプロファイルにアクセスするためのアクセス許可を表します。

- **openid** - ID トークンを要求します。
- **offline_access** - [認証コード フロー](authorization-code-flow.md)を使用して更新トークンを要求します。
- **00000000-0000-0000-0000-000000000000** - クライアント ID をスコープとして使用することは、同じクライアント ID で表される、独自のサービスまたは Web API に対して使用できるアクセス トークンをアプリが必要とすることを示します。

`/authorize` 要求の **response_type** パラメーターに `token` が含まれる場合、**scope** パラメーターには、付与されるリソース スコープを `openid` と `offline_access` 以外に少なくとも 1 つ含める必要があります。 そうしないと、`/authorize` 要求は失敗します。

## <a name="request-a-token"></a>トークンの要求

アクセス トークンを要求するには、承認コードが必要です。 `/authorize` エンドポイントに対する承認コード要求の例を次に示します。 カスタム ドメインとアクセス トークンの併用はサポートされていません。 要求 URL には、tenant-name.onmicrosoft.com ドメインを使用します。

次の例では、これらの値を置き換えます。

- `<tenant-name>` - Azure AD B2C テナントの名前。
- `<policy-name>` - カスタム ポリシーまたはユーザー フローの名前。
- `<application-ID>` - ユーザー フローをサポートするために登録した Web アプリケーションのアプリケーション識別子。
- `<redirect-uri>` - クライアント アプリケーションを登録したときに入力した **リダイレクト URI**。

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

承認コードを含む応答は、次の例のようになります。

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

承認コードを正常に受信したら、それを使用してアクセス トークンを要求できます。

```http
POST <tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

次の応答に似た内容が表示されます。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

https://jwt.ms を使用して、返されたアクセス トークンを調べると、次の例に似た内容が表示されるはずです。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C でトークンを構成する](configure-tokens.md)方法について学習します
