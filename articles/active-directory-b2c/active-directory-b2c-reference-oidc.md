---
title: OpenID Connect での Web サインイン - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory で導入された OpenID Connect 認証プロトコルを利用した Web アプリケーションの構築。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: bd7ecf273d4e842909d88eeaa3683203d8d9e841
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429167"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Azure Active Directory B2C での OpenID Connect による Web サインイン

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) は、ユーザーを Web アプリケーションに安全にサインインさせるために使用できる、OAuth 2.0 に基づいて構築された認証プロトコルです。 OpenID Connect の Azure Active Directory B2C (Azure AD B2C) 実装を使用することにより、Web アプリケーションでのサインアップ、サインイン、その他の ID 管理エクスペリエンスを Azure Active Directory (Azure AD) にアウトソーシングできます。 このガイドでは、これを言語に依存しない方法で実行する方法について説明します。 オープンソース ライブラリを利用しないで、HTTP メッセージを送受信する方法について説明します。

OpenID Connect は、OAuth 2.0 の "*認可*" プロトコルを "*認証*" プロトコルとして使用できるように拡張したものです。 OpenID Connect を使うことで OAuth を使用したシングル サインオンを行うことができます。 ここでは、クライアントがユーザーの ID を検証したり、ユーザーに関する基本的なプロファイル情報を取得したりできるようにするセキュリティ トークンである *ID トークン*の概念が導入されています。

これは OAuth 2.0 の拡張であるため、アプリが*アクセス トークン*を安全に取得することも可能になります。 アクセス トークンを使用すると、[認可サーバー](active-directory-b2c-reference-protocols.md#the-basics)によってセキュリティ保護されたリソースにアクセスできます。 サーバーでホストされ、ブラウザーでアクセスされる Web アプリケーションを構築する場合は、OpenID Connect をお勧めします。 Azure AD B2C を利用し、モバイルまたはデスクトップ アプリケーションに ID 管理を追加する場合は、OpenID Connect ではなく、 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) をご利用ください。

Azure AD B2C は、単純な認証と権限付与以上のことができるように標準の OpenID Connect プロトコルを拡張したものです。 これには、OpenID Connect を使用してサインアップ、サインイン、プロファイル管理などのユーザー エクスペリエンスをご利用のアプリに追加できるようにする[ユーザー フロー パラメーター](active-directory-b2c-reference-policies.md)が導入されています。 OpenID Connect プロトコルを使用する ID プロバイダーとしては、[Microsoft アカウント](active-directory-b2c-setup-msa-app.md)や他の [OpenID Connect プロバイダー](active-directory-b2c-setup-oidc-idp.md)があります。

次のセクションにある HTTP 要求例では、サンプル B2C ディレクトリ fabrikamb2c.onmicrosoft.com のほか、サンプル アプリケーション https://aadb2cplayground.azurewebsites.net およびユーザー フローを使用します。 これらの値を利用して、要求を自由に試すことができます。または、独自の値で置換できます。
[独自の B2C テナント、アプリケーション、ポリシーの取得方法](#use-your-own-b2c-tenant)について説明します。

## <a name="send-authentication-requests"></a>認証要求を送信する
ご利用の Web アプリでユーザーを認証し、ユーザー フローを実行する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。 これはフローのインタラクティブな部分であり、ユーザーはここでユーザー フローに応じてアクションを実行します。

この要求では、クライアントによって、ユーザーから取得する必要があるアクセス許可が `scope` パラメーターで示され、実行するユーザー フローが `p` パラメーターで示されます。 次のセクションでは、それぞれ異なるユーザー フローを使用する 3 つの例が (読みやすいように改行して) 示されています。 各要求の動作を感覚的に理解するために、要求をブラウザーに貼り付け、実行してみてください。

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
| --- | --- | --- |
| client_id |必須 |[Azure Portal](https://portal.azure.com/) によってアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |応答の種類。これには、OpenID Connect の ID トークンが含まれている必要があります。 Web アプリが Web API を呼び出すためのトークンも必要とする場合は、ここで行なったように `code+id_token` を使用できます。 |
| redirect_uri |推奨 |アプリの `redirect_uri` パラメーター。これにより、アプリは認証応答を送受信できます。 これは、URL でエンコードされている必要がある点を除き、ポータルで登録した `redirect_uri` パラメーターのいずれかに正確に一致している必要があります。 |
| scope |必須 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、ユーザーに関するデータを ID トークンの形式で取得するためのアクセス許可を示します (これについては後で詳細に説明します)。 Web アプリの場合、 `offline_access` スコープは任意です。 これは、アプリがリソースに長時間アクセスするには*更新トークン*が必要になることを示します。 |
| response_mode |推奨 |結果として得られた承認コードをアプリに送り返すために使用すべき方法。 これは `query`、`form_post`、`fragment` のいずれかにできます。  最高のセキュリティを得るには、`form_post` 応答モードをお勧めします。 |
| state |推奨 |要求に含まれ、トークンの応答として返される値。 任意の文字列を指定することができます。 クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce |必須 |結果として得られる ID トークンに要求として含まれる、要求に含まれている (アプリによって生成された) 値。 アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 この値は通常、要求の送信元を識別するために使用できるランダム化された一意の文字列です。 |
| p |必須 |実行されるユーザー フローです。 ご利用の B2C テナントに作成されたユーザー フローの名前です。 ユーザー フロー名の値は `b2c\_1\_` で始める必要があります。 ユーザー フローおよび[拡張ユーザー フロー フレームワーク](active-directory-b2c-reference-policies.md)の詳細について説明します。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類。 現時点で有効な値は `login` だけです。これはユーザーに、その要求に関する資格情報を強制的に入力させます。 シングル サインオンは作用しません。 |

この時点で、ユーザーはユーザー フローのワークフローを完了するよう求められます。 これにより、ユーザーは、ユーザー フローの定義方法に応じて、ユーザー名とパスワードの入力、ソーシャル ID でのサインイン、ディレクトリへのサインアップ、またはその他の複数の手順を行うことが必要になる場合があります。

ユーザーがユーザー フローを完了した後、Azure AD からは、`response_mode` パラメーターで指定された方法を使用して、指示されている `redirect_uri` パラメーターでご利用のアプリに応答が返されます。 実行されているユーザー フローには関係なく、応答は前の各ケースにおいて同じです。

`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| --- | --- |
| id_token |アプリが要求した ID トークン。 この ID トークンを使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。 ID トークンとその内容に関する詳細は、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」に含まれています。 |
| code |アプリが要求した承認コード (`response_type=code+id_token` を使用した場合)。 アプリは、承認コードを使用してターゲット リソースのアクセス トークンを要求できます。 承認コードの有効期間は非常に短時間です。 通常、約 10 分で期限が切れます。 |
| state |要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の中の `state` 値が同一であることを検証する必要があります。 |

アプリがエラーを適切に処理できるように、`redirect_uri` パラメーターにはエラー応答も送信されます。

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| --- | --- |
| error |発生するエラーの種類を分類したり、エラーに対応したりするために使用できるエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| state |詳細についてはこのセクションの最初の表を参照してください。 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。 |

## <a name="validate-the-id-token"></a>ID トークンの検証
ユーザーを認証するには、ID トークンを受信するだけでは不十分です。 ID トークンの署名を検証し、アプリの要件ごとにトークン内の要求を確認する必要があります。 Azure AD B2C は、[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

優先言語にもよりますが、JWT を検証するためのさまざまなオープンソース ライブラリを入手できます。 独自の検証ロジックを実装するより、オープンソース ライブラリを試してみることをお勧めします。 ライブラリを正しく利用する方法を模索するとき、ここにある情報が役に立つでしょう。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあって、アプリは、このエンドポイントを使用することで、Azure AD B2C に関する情報を実行時にフェッチすることができます。 この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。 ご利用の B2C テナントにはユーザー フロー別の JSON メタデータ ドキュメントがあります。 たとえば、`fabrikamb2c.onmicrosoft.com` 内の `b2c_1_sign_in` ユーザー フローのメタデータ ドキュメントは次の場所にあります。

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

この構成ドキュメントのプロパティの 1 つに `jwks_uri` があります。同じユーザー フローに対するこの値は次のようになります。

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

ID トークンの署名でどのユーザー フローが使用されたか (また、メタデータをどこからフェッチするか) を判定するには、2 つの選択肢があります。 1 つめは、ユーザー フロー名が ID トークン内の `acr` 要求に含まれています。 ID トークンの要求を解析する方法については、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。 もう 1 つの選択肢では、要求を発行するときに `state` パラメーターの値に含まれているユーザー フローをエンコードして、それをデコードして使用されたユーザー フローを判断します。 どちらの方法も有効です。

OpenID Connect メタデータ エンドポイントからメタデータ ドキュメントを取得した後、このエンドポイントにある RSA 256 公開キーを使用して ID トークンの署名を検証できます。 このエンドポイントには、特定の時点で (それぞれ `kid` 要求によって識別される) 複数のキーが一覧表示されている可能性があります。 また、ID トークンのヘッダーには、これらのキーのうちのどれが ID トークンの署名に使用されたかを示す `kid` 要求も含まれています。 詳細については、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」(特に、[トークンの検証](active-directory-b2c-reference-tokens.md#token-validation)に関するセクション) を参照してください。
<!--TODO: Improve the information on this-->

ID トークンの署名を検証した後、確認する必要のあるいくつかの要求が存在します。 次に例を示します。

* トークン再生攻撃を防止するために、`nonce` 要求を検証する必要があります。 その値が、サインイン要求で指定した値と一致していることが必要です。
* ID トークンが自分のアプリに対して発行されたことを確認するために、`aud` 要求を検証する必要があります。 その値がアプリのアプリケーション ID と一致している必要があります。
* ID トークンが期限切れになっていないことを確認するために、`iat` および `exp` 要求を検証する必要があります。

また、実行する必要のあるその他の検証もいつくか存在します。 これらは、「[OpenID Connect Core Spec (OpenID Connect コアの仕様)](https://openid.net/specs/openid-connect-core-1_0.html)」で詳細に説明されています。シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

* ユーザー/組織がアプリにサインアップ済みであることを確認する。
* 適切な承認/特権がユーザーにあることを確認する。
* Azure Multi-Factor Authentication など特定の強度の認証が行われたことを確認する。

ID トークン内の要求の詳細については、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。

ID トークンを検証した後、ユーザーとのセッションを開始できます。 ID トークン内の要求を使用して、アプリでユーザーに関する情報を取得できます。 この情報の使用には、表示、記録、および承認が含まれます。

## <a name="get-a-token"></a>トークンを取得する
Web アプリがユーザー フローの実行にのみ必要な場合は、次のいくつかのセクションを省略できます。 これらのセクションは、Web API への認証された呼び出しを行う必要があり、かつそれ自体も Azure AD B2C によって保護されている Web アプリにのみ適用できます。

トークン用に (`response_type=code+id_token` を使用して) 取得した承認コードは、`POST` 要求を `/token` エンドポイントに送信することによって目的のリソースに利用できます。 現在、トークンを要求できる唯一のリソースは、アプリの独自のバックエンド Web API です。 自身のトークンを要求するには、アプリのクライアント ID をスコープとして使用します。

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
| p |必須 |認証コードの取得に使用されたユーザー フロー。 この要求に別のユーザー フローを使用することはできません。 このパラメーターは `POST` 本文ではなく、クエリ文字列に追加することに注意してください。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com/) によってアプリに割り当てられたアプリケーション ID。 |
| grant_type |必須 |許可の種類。承認コード フローでは `authorization_code` を指定する必要があります。 |
| scope |推奨 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、ユーザーに関するデータを id_token パラメーターの形式で取得するためのアクセス許可を示します。 これを使用すると、クライアントと同じアプリケーション ID で表される、アプリの独自のバックエンド Web API にトークンを送信できます。 `offline_access` スコープは、アプリがリソースに長時間アクセスするには更新トークンが必要になることを示します。 |
| code |必須 |フローの最初の段階で取得した承認コード。 |
| redirect_uri |必須 |承認コードを受信したアプリケーションの `redirect_uri` パラメーター。 |
| client_secret |必須 |[Azure Portal](https://portal.azure.com/) で生成したアプリケーション シークレット。 このアプリケーション シークレットは、重要なセキュリティ アーティファクトです。 サーバーに安全に保管する必要があります。 また、このクライアント シークレットを定期的にローテーションすることも必要です。 |

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
| --- | --- |
| not_before |トークンが有効と見なされる時間 (エポック時間)。 |
| token_type |トークン タイプ値。 Azure AD でサポートされるのは `Bearer` のみです。 |
| access_token |要求した署名付きの JWT トークン。 |
| scope |トークンが有効なスコープ。 これらは、後で使用するためにトークンをキャッシュするときに使用できます。 |
| expires_in |アクセス トークンが有効な時間の長さ (秒単位)。 |
| refresh_token |OAuth 2.0 更新トークン。 現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。 更新トークンは有効期間が長いため、リソースへのアクセスを長時間にわたって保持するために使用できます。 詳細については、「[B2C token reference (B2C トークン リファレンス)](active-directory-b2c-reference-tokens.md)」を参照してください。 更新トークンを受信するには、承認要求とトークン要求の両方でスコープ `offline_access` を使用している必要があることに注意してください。 |

エラー応答は次のようになります。

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| --- | --- |
| error |発生するエラーの種類を分類したり、エラーに対応したりするために使用できるエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="use-the-token"></a>トークンを使用する
これでアクセス トークンを正常に取得できたので、そのトークンを `Authorization` ヘッダー内に含めることによって、それをバックエンド Web API への要求で使用できます。

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>トークンを更新する
ID トークンの有効期間は短時間です。 リソースに引き続きアクセスできるようにするには、有効期限が切れた後で更新する必要があります。 トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。 今回は、`code` パラメーターの代わりに `refresh_token` パラメーターを指定します。

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
| p |必須 |元の更新トークンの取得に使用されたユーザー フロー。 この要求に別のユーザー フローを使用することはできません。 このパラメーターは POST 本文ではなく、クエリ文字列に追加することに注意してください。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com/) によってアプリに割り当てられたアプリケーション ID。 |
| grant_type |必須 |付与の種類。承認コード フローのこの段階では、これは更新トークンである必要があります。 |
| scope |推奨 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、ユーザーに関するデータを ID トークンの形式で取得するためのアクセス許可を示します。 これを使用すると、クライアントと同じアプリケーション ID で表される、アプリの独自のバックエンド Web API にトークンを送信できます。 `offline_access` スコープは、アプリがリソースに長時間アクセスするには更新トークンが必要になることを示します。 |
| redirect_uri |推奨 |承認コードを受信したアプリケーションの `redirect_uri` パラメーター。 |
| refresh_token |必須 |フローの第 2 段階で取得した元の更新トークン。 更新トークンを受信するには、承認要求とトークン要求の両方でスコープ `offline_access` を使用している必要があることに注意してください。 |
| client_secret |必須 |[Azure Portal](https://portal.azure.com/) で生成したアプリケーション シークレット。 このアプリケーション シークレットは、重要なセキュリティ アーティファクトです。 サーバーに安全に保管する必要があります。 また、このクライアント シークレットを定期的にローテーションすることも必要です。 |

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
| --- | --- |
| not_before |トークンが有効と見なされる時間 (エポック時間)。 |
| token_type |トークン タイプ値。 Azure AD でサポートされるのは `Bearer` のみです。 |
| access_token |要求した署名付きの JWT トークン。 |
| scope |トークンが有効なスコープ。これは、後で使用するためにトークンをキャッシュするときに使用できます。 |
| expires_in |アクセス トークンが有効な時間の長さ (秒単位)。 |
| refresh_token |OAuth 2.0 更新トークン。 現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。  更新トークンは有効期間が長いため、リソースへのアクセスを長時間にわたって保持するために使用できます。 詳細については、 [B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 |

エラー応答は次のようになります。

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| --- | --- |
| error |発生するエラーの種類を分類したり、エラーに対応したりするために使用できるエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する
ユーザーをアプリからサインアウトさせるとき、アプリの Cookie を消去する、あるいはユーザーとのセッションを終了するだけでは十分ではありません。 サインアウトするには、ユーザーを Azure AD にリダイレクトする必要もあります。そうしない場合、ユーザーは資格情報を再入力しなくてもアプリで再認証されることがあります。 これは、Azure AD のシングル サインオン セッションが有効であるためです。

前に「ID トークンの検証」のセクションで説明した OpenID Connect メタデータ ドキュメントに一覧表示されている `end_session` エンドポイントに単純にユーザーをリダイレクトできます。

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
| p |必須 |ご利用のアプリケーションからユーザーをサインアウトさせるために使用するユーザー フロー。 |
| post_logout_redirect_uri |推奨 |サインアウトの正常終了後にユーザーをリダイレクトする URL。これが含まれていない場合、Azure AD B2C はユーザーに一般的なメッセージを表示します。 |

> [!NOTE]
> ユーザーを `end_session` エンドポイントにリダイレクトすると、そのユーザーの Azure AD B2C でのシングル サインオン状態の一部がクリアされますが、そのユーザーが自分のソーシャル ID プロバイダー (IDP) セッションからサインアウトされるわけではありません。 ユーザーがその後のサインインで同じ IDP を選択した場合は、資格情報を入力しなくても再認証されます。 ユーザーが B2C アプリケーションからサインアウトしようとする場合、そのユーザーは必ずしも自分の Facebook アカウントからサインアウトしようとしているとは限りません。 ただし、ローカル アカウントの場合、ユーザーのセッションを適切に終了できなければなりません。
> 
> 

## <a name="use-your-own-b2c-tenant"></a>独自の B2C テナントを使用する
これらの要求を自分で試してみようとする場合は、最初に次の 3 つの手順を実行した後、前に説明した例の値を独自の値に置き換える必要があります。

1. [B2C テナントを作成し](active-directory-b2c-get-started.md)、要求でテナントの名前を使用します。
2. [アプリケーションを作成](active-directory-b2c-app-registration.md)して、アプリケーション ID を取得します。 アプリ内に Web アプリ/Web API を含めます。 必要に応じて、アプリケーション シークレットを作成します。
3. [使用するユーザー フローを作成](active-directory-b2c-reference-policies.md)して、そのユーザー フロー名を取得します。

