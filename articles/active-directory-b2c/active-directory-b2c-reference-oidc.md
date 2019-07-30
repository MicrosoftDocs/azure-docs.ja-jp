---
title: OpenID Connect での Web サインイン - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C の OpenID Connect 認証プロトコルを使用して Web アプリケーションを構築します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0c855a3e0280e1fadf2362f2d8959beff2f5d00a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271975"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Azure Active Directory B2C での OpenID Connect による Web サインイン

OpenID Connect は、ユーザーを Web アプリケーションに安全にサインインさせるために使用できる、OAuth 2.0 に基づいて構築された認証プロトコルです。 OpenID Connect の Azure Active Directory B2C (Azure AD B2C) 実装を使用することにより、Web アプリケーションでのサインアップ、サインイン、その他の ID 管理エクスペリエンスを Azure Active Directory (Azure AD) にアウトソーシングできます。 このガイドでは、これを言語に依存しない方法で実行する方法について説明します。 オープンソース ライブラリを利用しないで、HTTP メッセージを送受信する方法について説明します。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 の "*認可*" プロトコルを "*認証*" プロトコルとして使用できるように拡張したものです。 この認証プロトコルでは、シングル サインオンを実行できます。 ここでは、クライアントがユーザーの ID を検証したり、ユーザーに関する基本的なプロファイル情報を取得したりできるようにする "*ID トークン*" の概念が導入されています。

これは OAuth 2.0 の拡張であるため、アプリケーションが*アクセス トークン*を安全に取得することも可能になります。 アクセス トークンを使用すると、[認可サーバー](active-directory-b2c-reference-protocols.md)によってセキュリティ保護されたリソースにアクセスできます。 サーバー上でホストされ、ブラウザー経由でアクセスされる Web アプリケーションを構築する場合は、OpenID Connect をお勧めします。 Azure AD B2C を利用し、モバイルまたはデスクトップ アプリケーションに ID 管理を追加する場合は、OpenID Connect ではなく、 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) をご利用ください。 トークンの詳細については、「[Overview of tokens in Azure Active Directory B2C (Azure Active Directory B2C でのトークンの概要)](active-directory-b2c-reference-tokens.md)」を参照してください。

Azure AD B2C は、単純な認証と権限付与以上のことができるように標準の OpenID Connect プロトコルを拡張したものです。 これには、OpenID Connect を使用してサインアップ、サインイン、プロファイル管理などのユーザー エクスペリエンスをご利用のアプリに追加できるようにする[ユーザー フロー パラメーター](active-directory-b2c-reference-policies.md)が導入されています。

## <a name="send-authentication-requests"></a>認証要求を送信する

ご利用の Web アプリケーションでユーザーを認証し、ユーザー フローを実行する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせることができます。 ユーザーはユーザー フローに応じてアクションを実行します。

この要求では、クライアントによって、ユーザーから取得する必要があるアクセス許可が `scope` パラメーターで示され、実行するユーザー フローが `p` パラメーターで示されます。 次のセクションでは、それぞれ異なるユーザー フローを使用する 3 つの例が (読みやすいように改行して) 示されています。 各要求の動作を感覚的に理解するために、要求をブラウザーに貼り付け、実行してみてください。 実際のテナントがあり、既にユーザー フローを作成済みである場合は、`fabrikamb2c` を実際のテナントの名前に置き換えてください。

#### <a name="use-a-sign-in-user-flow"></a>サインイン ユーザー フローを使用する
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>サインアップ ユーザー フローを使用する
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>プロファイル編集ユーザー フローを使用する
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| client_id | はい | [Azure portal](https://portal.azure.com/) によってアプリケーションに割り当てられたアプリケーション ID。 |
| response_type | はい | OpenID Connect の ID トークンが含まれている必要があります。 Web アプリケーションが Web API を呼び出すためのトークンも必要とする場合は、`code+id_token` を使用します。 |
| redirect_uri | いいえ | アプリケーションの `redirect_uri` パラメーター。これにより、アプリケーションは認証応答を送受信できます。 これは、URL でエンコードされている必要がある点を除き、Azure portal で登録した `redirect_uri` パラメーターのいずれかに正確に一致している必要があります。 |
| scope | はい | スコープのスペース区切りリスト。 `openid` スコープは、ユーザーをサインインさせ、ID トークンの形式でユーザーに関するデータを取得するためのアクセス許可を示します。 Web アプリケーションの場合、`offline_access` スコープは任意です。 これは、リソースへのアクセス期間を延長するには、アプリケーションで "*更新トークン*" が必要であることを示します。 |
| response_mode | いいえ | 結果として得られた承認コードをアプリケーションに送り返すために使用される方法。 これは `query`、`form_post`、`fragment` のいずれかにできます。  最高のセキュリティを得るには、`form_post` 応答モードをお勧めします。 |
| state | いいえ | 要求に含まれ、トークンの応答としても返される値。 任意の文字列を指定することができます。 クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリケーション内でユーザーの状態 (表示中のページなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce | はい | 要求に追加する (アプリケーションによって生成された) 値。この値が、最終的な ID トークンに要求として追加されます。 アプリケーションでこの値を確認することにより、トークン再生攻撃を緩和することができます。 この値は通常、要求の送信元を識別するために使用できるランダム化された一意の文字列です。 |
| p | はい | 実行されるユーザー フローです。 Azure AD B2C テナントに作成されたユーザー フローの名前です。 ユーザー フロー名は `b2c\_1\_` で始める必要があります。 |
| prompt | いいえ | 必要とされている、ユーザーとの対話の種類。 現時点で有効な値は `login` だけです。これはユーザーに、その要求に関する資格情報を強制的に入力させます。 |

この時点で、ユーザーはワークフローを完了するよう求められます。 ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりすることが必要な場合があります。 ユーザー フローの定義方法によっては、これ以外にもいくつかの手順が必要になる場合があります。

ユーザーがユーザー フローを完了すると、`response_mode` パラメーターで指定された方法を使用して、`redirect_uri` パラメーターで示された場所にあるアプリケーションに応答が返されます。 ユーザー フローには関係なく、応答は前の各ケースにおいて同じです。

`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| --------- | ----------- |
| id_token | アプリケーションが要求した ID トークン。 この ID トークンを使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。 |
| code | アプリケーションが要求した承認コード (`response_type=code+id_token` を使用した場合)。 アプリケーションは承認コードを使用して、対象リソースのアクセス トークンを要求します。 承認コードは、通常 10 分程度で期限切れとなります。 |
| state | 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリケーションでは、要求と応答の `state` 値が同一であることを検証する必要があります。 |

アプリケーションでエラーを適切に処理できるように、`redirect_uri` パラメーターにはエラー応答も送信されます。

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| --------- | ----------- |
| error | 発生したエラーの種類を分類するために使用できるコード。 |
| error_description | 認証エラーの根本的な原因を特定するのに役立つ具体的なエラー メッセージ。 |
| state | 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリケーションでは、要求と応答の `state` 値が同一であることを検証する必要があります。 |

## <a name="validate-the-id-token"></a>ID トークンの検証

ユーザーを認証するには、ID トークンを受信するだけでは不十分です。 ID トークンの署名を検証し、アプリケーションの要件ごとにトークン内の要求を確認します。 Azure AD B2C は、[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。 優先言語にもよりますが、JWT を検証するためのさまざまなオープンソース ライブラリを入手できます。 独自の検証ロジックを実装するより、オープンソース ライブラリを試してみることをお勧めします。 

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあって、アプリケーションは、このエンドポイントを使用することで、Azure AD B2C に関する情報を実行時に取得することができます。 この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。 ご利用の B2C テナントにはユーザー フロー別の JSON メタデータ ドキュメントがあります。 たとえば、`fabrikamb2c.onmicrosoft.com` 内の `b2c_1_sign_in` ユーザー フローのメタデータ ドキュメントは次の場所にあります。

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

この構成ドキュメントのプロパティの 1 つに `jwks_uri` があります。同じユーザー フローに対するこの値は次のようになります。

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

ID トークンの署名でどのユーザー フローが使用されたか (また、メタデータをどこから取得するか) を判定するには、2 つの選択肢があります。 1 つめは、ユーザー フロー名が ID トークン内の `acr` 要求に含まれています。 もう 1 つの選択肢では、要求を発行するときに `state` パラメーターの値に含まれているユーザー フローをエンコードして、それをデコードして使用されたユーザー フローを判断します。 どちらの方法も有効です。

OpenID Connect メタデータ エンドポイントからメタデータ ドキュメントを取得したら、RSA 256 公開キーを利用し、ID トークンの署名を検証できます。 このエンドポイントには、それぞれが `kid` 要求によって識別されるキーが複数存在すると表示される場合があります。 また、ID トークンのヘッダーには、これらのキーのうちのどれが ID トークンの署名に使用されたかを示す `kid` 要求も含まれています。

ID トークンの署名を検証した後、確認する必要のあるいくつかの要求が存在します。 次に例を示します。

- トークン再生攻撃を防止するために、`nonce` 要求を検証します。 その値が、サインイン要求で指定した値と一致していることが必要です。
- ID トークンが自分のアプリケーションのために発行されたことを確認するために、`aud` 要求を検証します。 その値がアプリケーションのアプリケーション ID と一致している必要があります。
- ID トークンの有効期限が切れていないことを確認するために、`iat` 要求と `exp` 要求を検証します。

また、実行する必要のあるその他の検証もいつくか存在します。 検証の詳細については、[OpenID Connect コアの仕様](https://openid.net/specs/openid-connect-core-1_0.html)に関するページを参照してください。シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

- ユーザー/組織がアプリケーションにサインアップ済みであることを確認する。
- 適切な承認/特権がユーザーにあることを確認する。
- Azure Multi-Factor Authentication など特定の強度の認証が行われたことを確認する。

ID トークンを検証した後、ユーザーとのセッションを開始できます。 ID トークン内の要求を使用して、アプリケーションでユーザーに関する情報を取得できます。 この情報の使用には、表示、記録、および承認が含まれます。

## <a name="get-a-token"></a>トークンを取得する

Web アプリケーションがユーザー フローの実行にのみ必要な場合は、次のいくつかのセクションを省略できます。 これらのセクションは、Web API への認証された呼び出しを行う必要があり、かつそれ自体も Azure AD B2C によって保護されている Web アプリケーションにのみ適用できます。

トークン用に (`response_type=code+id_token` を使用して) 取得した承認コードは、`POST` 要求を `/token` エンドポイントに送信することによって目的のリソースに利用できます。 Azure AD B2C では、対応するスコープを要求の中で指定することによって、普通に[他の API のアクセス トークンを要求](active-directory-b2c-access-tokens.md#request-a-token)できます。

さらに、要求スコープとしてアプリのクライアント ID を使用するという慣例によって、アプリ独自のバックエンド Web API 用アクセス トークンを要求することもできます (その場合、そのクライアント ID を "audience" として持つアクセス トークンが得られます)。

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| p | はい | 認証コードの取得に使用されたユーザー フロー。 この要求に別のユーザー フローを使用することはできません。 このパラメーターを POST 本文ではなく、クエリ文字列に追加します。 |
| client_id | はい | [Azure portal](https://portal.azure.com/) によってアプリケーションに割り当てられたアプリケーション ID。 |
| grant_type | はい | 許可の種類。承認コード フローでは `authorization_code` を指定する必要があります。 |
| scope | いいえ | スコープのスペース区切りリスト。 `openid` スコープは、ユーザーをサインインさせ、ユーザーに関するデータを id_token パラメーターの形式で取得するためのアクセス許可を示します。 これを使用すると、クライアントと同じアプリケーション ID で表される、アプリケーションの独自のバックエンド Web API にトークンを送信できます。 `offline_access` スコープは、リソースへのアクセス期間を延長するには、アプリケーションで更新トークンが必要であることを示します。 |
| code | はい | ユーザー フローの開始時に取得した承認コード。 |
| redirect_uri | はい | 承認コードを受信したアプリケーションの `redirect_uri` パラメーター。 |
| client_secret | はい | [Azure portal](https://portal.azure.com/) で生成されたアプリケーション シークレット。 このアプリケーション シークレットは、重要なセキュリティ アーティファクトです。 サーバーに安全に保管する必要があります。 このクライアント シークレットは、定期的に変更します。 |

正常なトークン応答は次のようになります。

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| パラメーター | 説明 |
| --------- | ----------- |
| not_before | トークンが有効と見なされる時間 (エポック時間)。 |
| token_type | トークン タイプ値。 `Bearer` は、サポートされる唯一のタイプです。 |
| access_token | 要求した署名付きの JWT トークン。 |
| scope | トークンが有効なスコープ。 |
| expires_in | アクセス トークンが有効な時間の長さ (秒単位)。 |
| refresh_token | OAuth 2.0 更新トークン。 現在のトークンの有効期限が切れた後、アプリケーションでは、このトークンを使用して、追加のトークンが取得されます。 更新トークンを使用すると、期間を延長してリソースにアクセスし続けることができます。 更新トークンを受信するには、承認要求とトークン要求の両方でスコープ `offline_access` を使用する必要があります。 |

エラー応答は次のようになります。

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| --------- | ----------- |
| error | 発生したエラーの種類を分類するために使用できるコード。 |
| error_description | 認証エラーの根本的な原因を特定するのに役立つメッセージ。 |

## <a name="use-the-token"></a>トークンを使用する

これでアクセス トークンを正常に取得できたので、そのトークンを `Authorization` ヘッダー内に含めることによって、それをバックエンド Web API への要求で使用できます。

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>トークンを更新する

ID トークンは、短時間で期限が切れます。 引き続きリソースにアクセスできるようにするには、期限が切れた後、トークンを更新します。 トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。 今回は、`code` パラメーターの代わりに `refresh_token` パラメーターを指定します。

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| p | はい | 元の更新トークンの取得に使用されたユーザー フロー。 この要求に別のユーザー フローを使用することはできません。 このパラメーターを POST 本文ではなく、クエリ文字列に追加します。 |
| client_id | はい | [Azure portal](https://portal.azure.com/) によってアプリケーションに割り当てられたアプリケーション ID。 |
| grant_type | はい | 付与の種類。承認コード フローのこのパートでは、更新トークンである必要があります。 |
| scope | いいえ | スコープのスペース区切りリスト。 `openid` スコープは、ユーザーをサインインさせ、ID トークンの形式でユーザーに関するデータを取得するためのアクセス許可を示します。 これを使用すると、クライアントと同じアプリケーション ID で表される、アプリケーションの独自のバックエンド Web API にトークンを送信できます。 `offline_access` スコープは、リソースへのアクセス期間を延長するには、アプリケーションで更新トークンが必要であることを示します。 |
| redirect_uri | いいえ | 承認コードを受信したアプリケーションの `redirect_uri` パラメーター。 |
| refresh_token | はい | フローの第 2 のパートで取得された元の更新トークン。 更新トークンを受信するには、承認要求とトークン要求の両方でスコープ `offline_access` を使用する必要があります。 |
| client_secret | はい | [Azure portal](https://portal.azure.com/) で生成されたアプリケーション シークレット。 このアプリケーション シークレットは、重要なセキュリティ アーティファクトです。 サーバーに安全に保管する必要があります。 このクライアント シークレットは、定期的に変更します。 |

正常なトークン応答は次のようになります。

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| パラメーター | 説明 |
| --------- | ----------- |
| not_before | トークンが有効と見なされる時間 (エポック時間)。 |
| token_type | トークン タイプ値。 `Bearer` は、サポートされる唯一のタイプです。 |
| access_token | 要求された署名付きの JWT トークン。 |
| scope | トークンが有効なスコープ。 |
| expires_in | アクセス トークンが有効な時間の長さ (秒単位)。 |
| refresh_token | OAuth 2.0 更新トークン。 現在のトークンの有効期限が切れた後、アプリケーションでは、このトークンを使用して、追加のトークンが取得されます。 更新トークンを使用すると、期間を延長してリソースにアクセスし続けることができます。 |

エラー応答は次のようになります。

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| --------- | ----------- |
| error | 発生したエラーの種類を分類するために使用できるコード。 |
| error_description | 認証エラーの根本的な原因を特定するのに役立つメッセージ。 |

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する

ユーザーをアプリケーションからサインアウトさせるときは、アプリケーションの Cookie を消去する、あるいはユーザーとのセッションを終了するだけでは十分ではありません。 サインアウトさせるには、ユーザーを Azure AD B2C にリダイレクトします。そうしない場合、ユーザーは資格情報を再入力しなくてもアプリケーションに対して再認証できることがあります。

前述した OpenID Connect メタデータ ドキュメントに示されている `end_session` エンドポイントにユーザーをリダイレクトしてください。

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| p | はい | ご利用のアプリケーションからユーザーをサインアウトさせるために使用するユーザー フロー。 |
| post_logout_redirect_uri | いいえ | サインアウトの正常終了後にユーザーをリダイレクトする URL。これが含まれていない場合、Azure AD B2C では、ユーザーに対して一般的なメッセージが表示されます。 |

ユーザーを `end_session` エンドポイントにリダイレクトすると、Azure AD B2C でユーザーのシングル サインオン状態が一部消去されますが、ユーザーがソーシャル ID プロバイダー (IDP) セッションからサインアウトされるわけではありません。 ユーザーがその後のサインインで同じ IDP を選択した場合は、資格情報を入力しなくても再認証されます。 ユーザーがアプリケーションからサインアウトしようとする場合、そのユーザーは必ずしも自分の Facebook アカウントからサインアウトしようとしているとは限りません。 ただし、ローカル アカウントを使用している場合、ユーザーのセッションは正常に終了します。

