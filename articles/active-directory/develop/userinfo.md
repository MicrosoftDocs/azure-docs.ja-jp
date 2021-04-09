---
title: Microsoft ID プラットフォームの UserInfo エンドポイント | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームの UserInfo エンドポイントについて説明します。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752728"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft ID プラットフォームの UserInfo エンドポイント

UserInfo エンドポイントは、[OpenID Connect 標準](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) の一部で、認証されたユーザーに関する要求を返すためのものです。  Microsoft ID プラットフォームでは、Microsoft Graph (https://graph.microsoft.com/oidc/userinfo) ) で UserInfo エンドポイントがホストされています。 

## <a name="find-the-well-known-configuration-endpoint"></a>.well-known 構成エンドポイントを探す

UserInfo エンドポイントは、プログラムを使って検出することができます。具体的には、`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` にある OpenID Connect 探索ドキュメントを使用します。 エンドポイントは `userinfo_endpoint` フィールドにあります。このパターンはクラウドをまたいで利用できるので、正しいエンドポイントを参照するうえで役立ちます。  アプリで UserInfo エンドポイントをハードコーディングすることはお勧めしません。代わりに OIDC 探索ドキュメントを使用して、実行時にこのエンドポイントを検索するようにしてください。

UserInfo エンドポイントはよく、ユーザーに関する情報を取得するために [OIDC 準拠のライブラリ](https://openid.net/developers/certified/)から OpenID Connect の仕様の一環として自動的に呼び出されます。  このようなエンドポイントをホストしていないと、Microsoft ID プラットフォームが標準に準拠せず、一部のライブラリにエラーが発生します。  [OIDC 標準で指定されている要求の一覧](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims)のうち、Microsoft が発行するのは name 関連の各種要求、sub 要求、および email 要求です (ただし、利用可能かつ同意が得られている場合に限ります)。  

## <a name="consider-use-an-id-token-instead"></a>以下を検討してください。代わりに ID トークンを使用する

アプリが受信できる ID トークンで利用できる情報は、UserInfo エンドポイントから取得できる情報の上位集合です。  UserInfo エンドポイントを呼び出すトークンを取得すると、同時に ID トークンを取得できます。そのため、ユーザーに関する情報を取得するときは、UserInfo エンドポイントを呼び出す代わりに、その ID トークンを使用することをお勧めします。  ID トークンを使用すると、アプリケーションの起動時のネットワーク要求が 1 から 2 件少なくなるため、アプリケーションの待ち時間を短縮できます。

オフィスの電話番号や役職など、ユーザーについてさらに詳しい情報が必要な場合は、[Microsoft Graph の `/user` API](/graph/api/user-get) を呼び出す必要があります。   このほか、[オプションの要求](active-directory-optional-claims.md)を使用して、ID とアクセス トークンに追加のユーザー情報を含めることもできます。

## <a name="calling-the-userinfo-endpoint"></a>UserInfo エンドポイントを呼び出す

UserInfo は、標準的な OAuth ベアラー トークン API であり、Microsoft Graph のトークンを取得するときに受け取ったアクセス トークンを使用して、他の Microsoft Graph API と同様に呼び出されます。 これにより、ユーザーに関する要求が含まれる JSON 応答が返されます。

### <a name="permissions"></a>アクセス許可

UserInfo API の呼び出しには、[OIDC アクセス許可](v2-permissions-and-consent.md#openid-connect-scopes)を使用します。 `openid` は必須です。`profile` と `email` は、応答内に情報を追加するためのスコープです。

|アクセス許可の種類      | アクセス許可    |
|:--------------------|:---------------------------------------------------------|
|委任済み (職場または学校アカウント) | openid (必須)、profile、email |
|委任済み (個人用 Microsoft アカウント) | openid (必須)、profile、email |
|Application | 適用なし |

> [!TIP]
> UserInfo エンドポイントのトークンと [ID トークン](id-tokens.md)を取得するには、ブラウザーにこの URL をコピーします。その際、クライアント ID とリダイレクト URI は自身のものに置き換えてください。 これは OpenID または Graph のスコープのみを要求するものであり、それ以外は何も要求しません。  同じトークン要求で 2 つの異なるリソースに対するアクセス許可を要求することはできないため、これを必ず使用する必要があります。
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> このアクセス トークンは、次のセクションで使用できます。

他の Microsoft Graph トークンと同様、ここで受け取るトークンは、JWT であるとは限りません。 Microsoft アカウント ユーザーにサインインした場合は、暗号化されたトークン形式になります。 これは、Microsoft Graph には特殊なトークン発行パターンがあるためです。 これは、アクセス トークンを使用して UserInfo エンドポイントを呼び出す機能には影響しません。

### <a name="calling-the-api"></a>API の呼び出し

UserInfo API は、OIDC の仕様に従い、GET と POST の両方をサポートしています。

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>UserInfo の応答

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

ここに記載されている要求は、UserInfo エンドポイントが返すことができるすべての要求です。  これらは、アプリに対して発行された [ID トークン](id-tokens.md)でアプリが参照するものと同じです。  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>UserInfo エンドポイントに関する注意事項

* この UserInfo エンドポイントを呼び出す場合には、v2.0 エンドポイントを使用する必要があります。  v1.0 エンドポイントを使用すると、login.microsoftonline.com でホストされている v1.0 の UserInfo エンドポイントのトークンが取得されます。  OIDC 準拠のアプリとライブラリはすべて、互換性確保のために v2.0 エンドポイントを使用することをお勧めします。
* UserInfo エンドポイントからの応答はカスタマイズできません。  要求のカスタマイズが必要な場合には、[要求マッピング]( active-directory-claims-mapping.md)を使用して、トークンで返される情報を編集してください。
* UserInfo エンドポイントからの応答に対して追加はできません。  ユーザーに関する追加の情報を取得する必要がある場合には、[オプションの要求]( active-directory-optional-claims.md)を使用してトークンに新しい要求を追加してください。

## <a name="next-steps"></a>次の手順

* [ID トークンの内容を確認する](id-tokens.md)
* [オプションの要求を使用して ID トークンの内容をカスタマイズする](active-directory-optional-claims.md)
* [OAuth2 プロトコルを使用してアクセス トークンと ID トークンを要求する](v2-protocols-oidc.md)
