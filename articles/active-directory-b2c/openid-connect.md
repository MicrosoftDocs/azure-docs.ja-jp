---
title: OpenID Connect による Web サインイン - Azure Active Directory B2C
description: Azure Active Directory B2C の OpenID Connect 認証プロトコルを使用して Web アプリケーションを構築します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 87415fc98bbcc9331ae4ff6282a65c85b570042d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579775"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Azure Active Directory B2C での OpenID Connect による Web サインイン

OpenID Connect は、ユーザーを Web アプリケーションに安全にサインインさせるために使用できる、OAuth 2.0 に基づいて構築された認証プロトコルです。 OpenID Connect の Azure Active Directory B2C (Azure AD B2C) 実装を使用することにより、Web アプリケーションでのサインアップ、サインイン、その他の ID 管理エクスペリエンスを Azure Active Directory (Azure AD) にアウトソーシングできます。 このガイドでは、これを言語に依存しない方法で実行する方法について説明します。 オープンソース ライブラリを利用しないで、HTTP メッセージを送受信する方法について説明します。

> [!NOTE]
> オープンソース認証ライブラリのほとんどで、アプリケーションの JWT トークンの取得と検証が行われます。 独自のコードを実装するより、オープンソース ライブラリを試してみることをお勧めします。 詳しくは、「[Microsoft Authentication Library (MSAL) の概要](../active-directory/develop/msal-overview.md)」と「[Microsoft Identity Web 認証ライブラリ](../active-directory/develop/microsoft-identity-web.md)」を参照してください。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 の "*認可*" プロトコルを "*認証*" プロトコルとして使用できるように拡張したものです。 この認証プロトコルでは、シングル サインオンを実行できます。 ここでは、クライアントがユーザーの ID を検証したり、ユーザーに関する基本的なプロファイル情報を取得したりできるようにする "*ID トークン*" の概念が導入されています。

これは OAuth 2.0 の拡張であるため、アプリケーションが *アクセス トークン* を安全に取得することも可能になります。 アクセス トークンを使用すると、[認可サーバー](protocols-overview.md)によってセキュリティ保護されたリソースにアクセスできます。 サーバー上でホストされ、ブラウザー経由でアクセスされる Web アプリケーションを構築する場合は、OpenID Connect をお勧めします。 トークンの詳細については、「[Overview of tokens in Azure Active Directory B2C (Azure Active Directory B2C でのトークンの概要)](tokens-overview.md)」を参照してください。

Azure AD B2C は、単純な認証と権限付与以上のことができるように標準の OpenID Connect プロトコルを拡張したものです。 これには、OpenID Connect を使用してサインアップ、サインイン、プロファイル管理などのユーザー エクスペリエンスをご利用のアプリに追加できるようにする[ユーザー フロー パラメーター](user-flow-overview.md)が導入されています。

## <a name="send-authentication-requests"></a>認証要求を送信する

ご利用の Web アプリケーションでユーザーを認証し、ユーザー フローを実行する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせることができます。 ユーザーはユーザー フローに応じてアクションを実行します。

この要求では、クライアントによって、ユーザーから取得する必要があるアクセス許可が `scope` パラメーターで示され、実行するユーザー フローが指定されます。 各要求の動作を感覚的に理解するために、要求をブラウザーに貼り付け、実行してみてください。 `{tenant}` をテナントの名前に置き換えます。 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` を、テナントに登録済みのアプリケーションのアプリ ID に置き換えます。 また、ポリシー名 (`{policy}`) を、テナント内のポリシー名に変更します (例: `b2c_1_sign_in`)。

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| {tenant} | はい | Azure AD B2C テナントの名前。 |
| {policy} | はい | 実行するユーザー フロー。 Azure AD B2C テナントに作成したユーザー フローの名前を指定します。 例: `b2c_1_sign_in`、`b2c_1_sign_up`、または`b2c_1_edit_profile`。 |
| client_id | はい | [Azure portal](https://portal.azure.com/) によってアプリケーションに割り当てられたアプリケーション ID。 |
| nonce | はい | 要求に追加する (アプリケーションによって生成された) 値。この値が、最終的な ID トークンに要求として追加されます。 アプリケーションでこの値を確認することにより、トークン再生攻撃を緩和することができます。 この値は通常、要求の送信元を識別するために使用できるランダム化された一意の文字列です。 |
| response_type | はい | OpenID Connect の ID トークンが含まれている必要があります。 Web アプリケーションが Web API を呼び出すためのトークンも必要とする場合は、`code+id_token` を使用します。 |
| scope | はい | スコープのスペース区切りリスト。 `openid` スコープは、ユーザーをサインインさせ、ID トークンの形式でユーザーに関するデータを取得するためのアクセス許可を示します。 Web アプリケーションの場合、`offline_access` スコープは任意です。 これは、リソースへのアクセス期間を延長するには、アプリケーションで "*更新トークン*" が必要であることを示します。 |
| prompt | いいえ | 必要とされている、ユーザーとの対話の種類。 現時点で有効な値は `login` だけです。これはユーザーに、その要求に関する資格情報を強制的に入力させます。 |
| redirect_uri | いいえ | アプリケーションの `redirect_uri` パラメーター。これにより、アプリケーションは認証応答を送受信できます。 これは、URL でエンコードされている必要がある点を除き、Azure portal で登録した `redirect_uri` パラメーターのいずれかに正確に一致している必要があります。 |
| response_mode | いいえ | 結果として得られた承認コードをアプリケーションに送り返すために使用される方法。 これは `query`、`form_post`、`fragment` のいずれかにできます。  最高のセキュリティを得るには、`form_post` 応答モードをお勧めします。 |
| state | いいえ | 要求に含まれ、トークンの応答としても返される値。 任意の文字列を指定することができます。 クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリケーション内でユーザーの状態 (表示中のページなど) に関する情報をエンコードする目的にも使用されます。 |
| login_hint | いいえ| サインインページのサインイン名フィールドに事前に入力するために使用できます。 詳細については、「[サインイン名を事前入力する](direct-signin.md#prepopulate-the-sign-in-name)」を参照してください。  |
| domain_hint | いいえ| サインインに使用する必要があるソーシャル ID プロバイダーに関するヒントを Azure AD B2C に提供します。 有効な値が含まれている場合、ユーザーは直接 ID プロバイダーのサインイン ページに移動します。  詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 |
| カスタム パラメーター | いいえ| [カスタムポリシー](custom-policy-overview.md)で使用できるカスタム パラメーター。 例: [動的なカスタム ページ コンテンツ URI](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)、または[キー値要求リゾルバー](claim-resolver-overview.md#oauth2-key-value-parameters)。 |

この時点で、ユーザーはワークフローを完了するよう求められます。 ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりすることが必要な場合があります。 ユーザー フローの定義方法によっては、これ以外にもいくつかの手順が必要になる場合があります。

ユーザーがユーザー フローを完了すると、`response_mode` パラメーターで指定された方法を使用して、`redirect_uri` パラメーターで示された場所にあるアプリケーションに応答が返されます。 ユーザー フローには関係なく、応答は前の各ケースにおいて同じです。

`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```http
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

```http
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

ユーザーを認証するには、ID トークンを受信するだけでは不十分です。 ID トークンの署名を検証し、アプリケーションの要件ごとにトークン内の要求を確認します。 Azure AD B2C は、[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。 

> [!NOTE]
> オープンソース認証ライブラリのほとんどで、アプリケーションの JWT トークンの検証が行われます。 独自の検証ロジックを実装するより、オープンソース ライブラリを試してみることをお勧めします。 詳しくは、「[Microsoft Authentication Library (MSAL) の概要](../active-directory/develop/msal-overview.md)」と「[Microsoft Identity Web 認証ライブラリ](../active-directory/develop/microsoft-identity-web.md)」を参照してください。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあって、アプリケーションは、このエンドポイントを使用することで、Azure AD B2C に関する情報を実行時に取得することができます。 この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。 ご利用の B2C テナントにはユーザー フロー別の JSON メタデータ ドキュメントがあります。 たとえば、`fabrikamb2c.onmicrosoft.com` 内の `b2c_1_sign_in` ユーザー フローのメタデータ ドキュメントは次の場所にあります。

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

この構成ドキュメントのプロパティの 1 つに `jwks_uri` があります。同じユーザー フローに対するこの値は次のようになります。

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

ID トークンの署名でどのユーザー フローが使用されたか (また、メタデータをどこから取得するか) を判定するには、2 つの選択肢があります。 1 つめは、ユーザー フロー名が ID トークン内の `acr` 要求に含まれています。 もう 1 つの選択肢では、要求を発行するときに `state` パラメーターの値に含まれているユーザー フローをエンコードして、それをデコードして使用されたユーザー フローを判断します。 どちらの方法も有効です。

OpenID Connect メタデータ エンドポイントからメタデータ ドキュメントを取得したら、RSA 256 公開キーを利用し、ID トークンの署名を検証できます。 このエンドポイントには、それぞれが `kid` 要求によって識別されるキーが複数存在すると表示される場合があります。 また、ID トークンのヘッダーには、これらのキーのうちのどれが ID トークンの署名に使用されたかを示す `kid` 要求も含まれています。

Azure AD B2C からのトークンを確認するには、指数 (e) と剰余 (n) を使用して公開キーを生成する必要があります。 各プログラミング言語でこれを行う方法をそれぞれ決定する必要があります。 RSA プロトコルを使用した公開キーの生成に関する公式ドキュメントについては、 https://tools.ietf.org/html/rfc3447#section-3.1 を参照してください。

ID トークンの署名を検証した後、確認する必要のあるいくつかの要求が存在します。 次に例を示します。

- トークン再生攻撃を防止するために、`nonce` 要求を検証します。 その値が、サインイン要求で指定した値と一致していることが必要です。
- ID トークンが自分のアプリケーションのために発行されたことを確認するために、`aud` 要求を検証します。 その値がアプリケーションのアプリケーション ID と一致している必要があります。
- ID トークンの有効期限が切れていないことを確認するために、`iat` 要求と `exp` 要求を検証します。

また、実行する必要のあるその他の検証もいつくか存在します。 検証の詳細については、[OpenID Connect コアの仕様](https://openid.net/specs/openid-connect-core-1_0.html)に関するページを参照してください。シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

- ユーザー/組織がアプリケーションにサインアップ済みであることを確認する。
- 適切な承認/特権がユーザーにあることを確認する。
- Azure AD Multi-Factor Authentication など特定の強度の認証が行われたことを確認する。

ID トークンが検証された後、ユーザーとのセッションを開始できます。 ID トークン内の要求を使用して、アプリケーションでユーザーに関する情報を取得できます。 この情報の使用には、表示、記録、および承認が含まれます。

## <a name="get-a-token"></a>トークンを取得する

Web アプリケーションがユーザー フローの実行にのみ必要な場合は、次のいくつかのセクションを省略できます。 これらのセクションは、Web API への認証された呼び出しを行う必要があり、かつそれ自体も Azure AD B2C によって保護されている Web アプリケーションにのみ適用できます。

トークン用に (`response_type=code+id_token` を使用して) 取得した承認コードは、`POST` 要求を `/token` エンドポイントに送信することによって目的のリソースに利用できます。 Azure AD B2C では、対応するスコープを要求の中で指定することによって、いつものように[他の API のアクセス トークンを要求](access-tokens.md#request-a-token)できます。

さらに、要求スコープとしてアプリのクライアント ID を使用するという慣例によって、アプリ独自のバックエンド Web API 用アクセス トークンを要求することもできます (その場合、そのクライアント ID を "audience" として持つアクセス トークンが得られます)。

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| {tenant} | はい | Azure AD B2C テナントの名前。 |
| {policy} | はい | 認証コードの取得に使用されたユーザー フロー。 この要求に別のユーザー フローを使用することはできません。 このパラメーターを POST 本文ではなく、クエリ文字列に追加します。 |
| client_id | はい | [Azure portal](https://portal.azure.com/) によってアプリケーションに割り当てられたアプリケーション ID。 |
| client_secret | はい (Web アプリの場合) | [Azure portal](https://portal.azure.com/) で生成されたアプリケーション シークレット。 クライアントが安全にクライアント シークレットを格納できる Web アプリのシナリオでは、このフローでクライアント シークレットが使用されます。 ネイティブ アプリ (パブリック クライアント) のシナリオでは、クライアント シークレットを安全に保存できないため、このフローでは使用されません。 クライアント シークレットを使用する場合は、定期的に変更してください。 |
| code | はい | ユーザー フローの開始時に取得した承認コード。 |
| grant_type | はい | 許可の種類。承認コード フローでは `authorization_code` を指定する必要があります。 |
| redirect_uri | はい | 承認コードを受信したアプリケーションの `redirect_uri` パラメーター。 |
| scope | いいえ | スコープのスペース区切りリスト。 `openid` スコープは、ユーザーをサインインさせ、ユーザーに関するデータを id_token パラメーターの形式で取得するためのアクセス許可を示します。 これを使用すると、クライアントと同じアプリケーション ID で表される、アプリケーションの独自のバックエンド Web API にトークンを送信できます。 `offline_access` スコープは、リソースへのアクセス期間を延長するには、アプリケーションで更新トークンが必要であることを示します。 |

正常なトークン応答は次のようになります。

```json
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

```json
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

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>トークンを更新する

ID トークンは、短時間で期限が切れます。 引き続きリソースにアクセスできるようにするには、期限が切れた後、トークンを更新します。 トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。 今回は、`code` パラメーターの代わりに `refresh_token` パラメーターを指定します。

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| {tenant} | はい | Azure AD B2C テナントの名前。 |
| {policy} | はい | 元の更新トークンの取得に使用されたユーザー フロー。 この要求に別のユーザー フローを使用することはできません。 このパラメーターを POST 本文ではなく、クエリ文字列に追加します。 |
| client_id | はい | [Azure portal](https://portal.azure.com/) によってアプリケーションに割り当てられたアプリケーション ID。 |
| client_secret | はい (Web アプリの場合) | [Azure portal](https://portal.azure.com/) で生成されたアプリケーション シークレット。 クライアントが安全にクライアント シークレットを格納できる Web アプリのシナリオでは、このフローでクライアント シークレットが使用されます。 ネイティブ アプリ (パブリック クライアント) のシナリオでは、クライアント シークレットを安全に保存できないため、この呼び出しでは使用されません。 クライアント シークレットを使用する場合は、定期的に変更してください。 |
| grant_type | はい | 許可の種類。承認コード フローのこの部分の `refresh_token` を指定する必要があります。 |
| refresh_token | はい | フローの第 2 のパートで取得された元の更新トークン。 更新トークンを受信するには、承認要求とトークン要求の両方でスコープ `offline_access` を使用する必要があります。 |
| redirect_uri | いいえ | 承認コードを受信したアプリケーションの `redirect_uri` パラメーター。 |
| scope | いいえ | スコープのスペース区切りリスト。 `openid` スコープは、ユーザーをサインインさせ、ID トークンの形式でユーザーに関するデータを取得するためのアクセス許可を示します。 これを使用すると、クライアントと同じアプリケーション ID で表される、アプリケーションの独自のバックエンド Web API にトークンを送信できます。 `offline_access` スコープは、リソースへのアクセス期間を延長するには、アプリケーションで更新トークンが必要であることを示します。 |

正常なトークン応答は次のようになります。

```json
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

```json
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

ユーザーをアプリケーションからサインアウトさせるときは、アプリケーションの Cookie を消去する、あるいはユーザーとのセッションを終了するだけでは十分ではありません。 サインアウトさせるには、ユーザーを Azure AD B2C にリダイレクトします。そうしない場合、ユーザーは資格情報を再入力しなくてもアプリケーションに対して再認証できることがあります。 詳しくは、「[Azure AD B2C のセッション](session-behavior.md)」をご覧ください。

ユーザーをサインアウトするには、前述した OpenID Connect メタデータ ドキュメントに示されている `end_session` エンドポイントにユーザーをリダイレクトします。

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| {tenant} | はい | Azure AD B2C テナントの名前。 |
| {policy} | はい | ご利用のアプリケーションからユーザーをサインアウトさせるために使用するユーザー フロー。 |
| id_token_hint| いいえ | エンド ユーザーのクライアントとの現在の認証済みセッションに関するヒントとしてログアウトエンド ポイントに渡される発行済みの ID トークン。 `id_token_hint` によって、`post_logout_redirect_uri` が Azure AD B2C アプリケーション設定に登録済みの応答 URL であることが確認されます。 詳細については、「[ログアウトのリダイレクトをセキュリティで保護する](#secure-your-logout-redirect)」を参照してください。 |
| client_id | いいえ* | [Azure portal](https://portal.azure.com/) によってアプリケーションに割り当てられたアプリケーション ID。<br><br>\**これは、`Application` の分離の SSO 構成を使用し、 _[ログアウト要求に ID トークンが必要]_ が `No` に設定されている場合に必要となります。* |
| post_logout_redirect_uri | いいえ | サインアウトの正常終了後にユーザーをリダイレクトする URL。これが含まれていない場合、Azure AD B2C では、ユーザーに対して一般的なメッセージが表示されます。 `id_token_hint` を指定しない限り、この URL を Azure AD B2C アプリケーション設定に応答 URL として登録することはできません。 |
| state | いいえ | 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリケーションでは、要求と応答の `state` 値が同一であることを検証する必要があります。 |

### <a name="secure-your-logout-redirect"></a>ログアウトのリダイレクトをセキュリティで保護する

ログアウト後、ユーザーは、アプリケーションに対して指定されている応答 URL に関係なく、`post_logout_redirect_uri` パラメーターに指定された URI にリダイレクトされます。 ただし、有効な `id_token_hint` が渡され、 **[ログアウト要求に ID トークンが必要]** が有効になっている場合、Azure AD B2C では、リダイレクトの実行前に、`post_logout_redirect_uri` の値がいずれかのアプリケーションの構成済みのリダイレクト URL と一致するかどうかが検証されます。 一致する応答 URL がアプリケーションで構成されていない場合は、エラー メッセージが表示され、ユーザーはリダイレクトされません。

ログアウト要求に必要な ID トークンを設定するには、「[Azure Active Directory B2C でセッションの動作を構成する](session-behavior.md#secure-your-logout-redirect)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C セッション](session-behavior.md)の詳細について学習します。