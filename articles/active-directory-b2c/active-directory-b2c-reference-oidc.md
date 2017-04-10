---
title: Azure Active Directory B2C | Microsoft Docs
description: "Azure Active Directory で導入された OpenID Connect 認証プロトコルを利用した Web アプリケーションの構築。"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 21d420c8-3c10-4319-b681-adf2e89e7ede
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9a4ccad94520bd0d811ba9dcfd6f7cc680c89a1f


---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: OpenID Connect による Web サインイン
OpenID Connect は OAuth 2.0 を基盤として開発された認証プロトコルであり、ユーザーを Web アプリケーションに安全にサインインさせるために利用されます。  Azure Active Directory (Azure AD) B2C で導入された OpenID Connect を利用することで、Web アプリケーションのサインアップ、サインイン、その他の ID 管理を Azure AD に外注できます。 このガイドでは、言語に依存しない方法でこれを実行する方法を説明します。 オープンソース ライブラリを利用しないで、HTTP メッセージを送受信する方法について説明します。

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 の "*承認*" プロトコルを "*認証*" プロトコルとして使用できるように拡張したものです。 OpenID Connect を使うことで OAuth を使用したシングル サインオンを行うことができます。 OpenID Connect には、`id_token` の概念が導入されています。クライアントは、このセキュリティ トークンによってユーザーの本人性を確認し、そのユーザーに関する基本的なプロファイル情報を取得することができます。

OAuth 2.0 を拡張したものであるため、アプリで安全に **access_tokens** を取得することもできます。 access_token を使用すると、[承認サーバー](active-directory-b2c-reference-protocols.md#the-basics)によってセキュリティ保護されたリソースにアクセスできます。 サーバーでホストされ、ブラウザーでアクセスされる Web アプリケーションを構築する場合は、OpenID Connect をお勧めします。 Azure AD B2C を利用し、モバイルまたはデスクトップ アプリケーションに ID 管理を追加する場合は、OpenID Connect ではなく、 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) をご利用ください。

Azure AD B2C は、単純な認証と権限付与以上のことができるように標準の OpenID Connect プロトコルを拡張したものです。 [**ポリシー パラメーター**](active-directory-b2c-reference-policies.md)を導入しており、このパラメーターにより、OpenID Connect を利用し、サインアップ、サインイン、プロファイル管理などのユーザー操作をアプリに追加できます。 ここでは、OpenID Connect とポリシーを使用して、Web アプリケーションに各種のユーザー操作を導入する方法について説明します。 Web API にアクセスするための access_token を取得する方法についても説明します。

下記の HTTP 要求例では、サンプル B2C ディレクトリの **fabrikamb2c.onmicrosoft.com**、サンプル アプリケーションの **https://aadb2cplayground.azurewebsites.net**、ポリシーを利用します。 これらの値を利用して、要求を自由に試すことができます。または、独自の値で置換できます。
[独自の B2C テナント、アプリケーション、ポリシーの取得方法](#use-your-own-b2c-directory)について学習してください。

## <a name="send-authentication-requests"></a>認証要求を送信する
Web アプリでユーザーを認証し、ポリシーを実行する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。 これはフローの対話部分であり、実際、ユーザーはポリシーに基づいて操作します。

この要求では、クライアントはユーザーから取得する必要があるアクセス許可を `scope` パラメーターで示し、実行するポリシーを `p` パラメーターで示します。 3 つの例を以下に示します (読みやすいように改行してあります)。それぞれ異なるポリシーが使用されています。 各要求の動作を感覚的に理解するために、要求をブラウザーに貼り付け、実行してみてください。

#### <a name="use-a-sign-in-policy"></a>サインイン ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>サインアップ ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>プロファイル編集ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| パラメーター | 必須 | Description |
| --- | --- | --- |
| client_id |必須 |[Azure Portal](https://portal.azure.com/) によってアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |応答の種類。OpenID Connect の `id_token` を含む必要があります。 Web アプリで Web API を呼び出すためのトークンも必要になる場合、ここで行ったように、`code+id_token` を利用できます。 |
| redirect_uri |推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、**id_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します (これについては後で詳しく説明します)。 Web アプリの場合、 `offline_access` スコープは任意です。 リソースに長期アクセスするためにアプリは **refresh_token** を必要とすることを示します。 |
| response_mode |推奨 |結果として得られた authorization_code をアプリに返すときに使用するメソッド。 'query'、'form_post'、'fragment' のいずれかを指定できます。  セキュリティを保持するためには、'form_post' をお勧めします。 |
| state |推奨 |要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な id_token に要求として追加されます。 アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| p |必須 |実行されるポリシー。 B2C テナントに作成されたポリシーの名前です。 ポリシー名の値は、"b2c\_1\_" で始まっている必要があります。 ポリシーの詳細については、「 [拡張ポリシー フレームワーク](active-directory-b2c-reference-policies.md)」を参照してください。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類。 現時点で有効な値は 'login' のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。 シングル サインオンは作用しません。 |

この時点で、ユーザーにポリシーのワークフローの完了が求められます。 たとえば、ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりする必要があります。他にも、ポリシーの定義に基づき、多数の手順があります。

ユーザーがポリシーを完了すると、Azure AD は `response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。 実行されたポリシーに関係なく、上記のすべての例で応答はまったく同じになります。

`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | Description |
| --- | --- |
| id_token |アプリが要求した id_token。 この id_token を使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。 id_token とその内容の詳細については、「[Azure AD B2C: トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。 |
| code |`response_type=code+id_token` を使用した場合、アプリが要求した authorization_code。 アプリは承認コードを使用して、対象リソースの access_token を要求します。 authorization_code は非常に短命です。 通常、約 10 分で期限が切れます。 |
| state |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| state |前の表の詳しい説明を参照してください。 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

## <a name="validate-the-idtoken"></a>id_token を検証する
単に id_token を受け取るだけでは、ユーザーを認証するには不十分です。id_token の署名を検証し、そのトークンに含まれる要求をアプリの要件に従って確認する必要があります。 Azure AD B2C は、[JSON Web トークン (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

優先言語にもよりますが、JWT を検証するためのさまざまなオープンソース ライブラリを入手できます。 独自の検証ロジックを実装するより、オープンソース ライブラリを試してみることをお勧めします。 ライブラリを正しく利用する方法を模索するとき、ここにある情報が役に立つでしょう。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあって、アプリは、このエンドポイントを使用することで、Azure AD B2C に関する情報を実行時にフェッチすることができます。 この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。 B2C テナントにはポリシー別の JSON メタデータ ドキュメントがあります。 たとえば、`fabrikamb2c.onmicrosoft.com` の `b2c_1_sign_in` ポリシーのメタデータ ドキュメントは次の場所にあります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

この構成ドキュメントのプロパティの 1 つに `jwks_uri` があります。この値は同じポリシーで次のようになります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

id_token の署名で使用されたポリシー (とメタデータのフェッチ場所) は 2 つの方法で判断できます。 最初の方法です。ポリシー名が id_token の `acr` 要求に含まれています。 id_token の要求を解析する方法については、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。 もう 1 つの方法です。要求を発行するとき、`state` パラメーターの値に含まれるポリシーを符号化し、それから復号化して使用されたポリシーを判断します。 いずれの方法も完全に有効です。

OpenID Connect メタデータ エンドポイントからメタデータ ドキュメントを取得したら、このエンドポイントにある RSA 256 公開鍵を利用し、id_token の署名を検証できます。 このエンドポイントには常時、複数のキーがリストされており、それぞれのキーは `kid`で識別されます。 id_token のヘッダーにはさらに `kid` 要求が格納されていて、複数のキーのうち、どのキーが id_token の署名に使用されたかが、この要求によって示されます。 [トークンの検証](active-directory-b2c-reference-tokens.md#token-validation)を含む詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。
<!--TODO: Improve the information on this-->

id_token の署名を検証した後に、確認の必要な要求がいくつか存在します。たとえば、次のようなものです。

* トークン再生攻撃を防止するために、`nonce` 要求を検証する必要があります。 その値が、サインイン要求で指定した値と一致していることが必要です。
* id_token が確実に自分のアプリに対して発行されたものであることを確認するために、`aud` 要求を検証する必要があります。 その値がアプリのアプリケーション ID と一致している必要があります。
* id_token の有効期限が切れていないことを確認するために、`iat` 要求と `exp` 要求を検証する必要があります。

他にも実行する必要がある検証がいくつかあります。これについては、[OpenID Connect Core の仕様](http://openid.net/specs/openid-connect-core-1_0.html)で詳しく説明されています。  シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

* ユーザー/組織がアプリにサインアップ済みであることを確認する。
* 適切な承認/特権がユーザーにあることを確認する。
* Azure Multi-Factor Authentication など特定の強度の認証が行われたことを確認する。

id_token に含まれる要求の詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。

id_token を十分検証したら、ユーザーとのセッションを開始し、id_token に含まれる要求を使ってそのユーザーに関する情報をアプリの中で取得することができます。 取得した情報は、表示、記録、承認などに利用することができます。

## <a name="get-a-token"></a>トークンを取得する
Web アプリに必要なことがポリシーの実行だけであれば、以降のセクションを一部省略できます。 以降のセクションは、Web アプリで Web API を認証呼び出しする必要があり、その呼び出しも Azure AD B2C で保護する場合にのみ適用されます。

`POST` 要求を `/token` エンドポイントに送信することで、(`response_type=code+id_token` を利用し) トークンに取得した authorization_code を任意のリソースに適用できます。 現在、トークンを要求できる唯一のリソースはアプリの独自のバックエンド Web API です。 自身のトークンを要求するには、アプリのクライアント ID をスコープとして使用します。

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| パラメーター | 必須 | Description |
| --- | --- | --- |
| p |必須 |認証コードの取得に使用されたポリシー。 この要求に別のポリシーを使用することはできません。 このパラメーターは、POST 本文ではなく、" **クエリ文字列**" に追加することに注意してください。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com/) によってアプリに割り当てられたアプリケーション ID。 |
| grant_type |必須 |許可の種類。承認コード フローでは `authorization_code` を指定する必要があります。 |
| scope |推奨 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、**id_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します。 クライアントと同じアプリケーション ID で表され、アプリの独自のバックエンド Web API にトークンを届けるために利用できます。 `offline_access` スコープは、リソースに長期アクセスするためにアプリは **refresh_token** を必要とすることを示します。 |
| code |必須 |フローの最初の段階で取得した authorization_code。 |
| redirect_uri |必須 |authorization_code を受け取った、アプリケーションの redirect_uri。 |
| client_secret |必須 |[Azure Portal](https://portal.azure.com/) で生成したアプリケーション シークレット。 このアプリケーション シークレットは、重要なセキュリティ アーティファクトです。 サーバーに安全に保管する必要があります。 また、慎重を期して、このクライアント シークレットを定期的に変更してください。 |

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
| パラメーター | Description |
| --- | --- |
| not_before |トークンが有効と見なされる時間 (エポック時間)。 |
| token_type |トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| access_token |要求した署名付きの JWT トークン。 |
| scope |トークンの有効スコープ。後の利用のためにトークンのキャッシュに利用できます。 |
| expires_in |access_token が有効な時間の長さ (秒単位)。 |
| refresh_token |OAuth 2.0 の refresh_token。 現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。  refresh_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 詳細については、 [B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 refresh_token を受け取るには、承認要求とトークン要求の両方でスコープ `offline_access` を使用していなければなりません。 |

エラー応答は次のようになります。

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="use-the-token"></a>トークンを使用する
`access_token` を正常に取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、バックエンド Web API への要求に使用することができます。

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>トークンを更新する
id_token は短命です。 リソースに引き続きアクセスできるようにするには、有効期限が切れた後で更新する必要があります。 トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。 このとき、`code` の代わりに `refresh_token` を指定します。

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| パラメーター | 必須 | Description |
| --- | --- | --- |
| p |必須 |元の refresh_token の取得に使用されたポリシー。 この要求に別のポリシーを使用することはできません。 このパラメーターは、POST 本文ではなく、" **クエリ文字列**" に追加することに注意してください。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com/) によってアプリに割り当てられたアプリケーション ID。 |
| grant_type |必須 |許可の種類。承認コード フローのこのレッグの `refresh_token` を指定する必要があります。 |
| scope |推奨 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、**id_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します。 クライアントと同じアプリケーション ID で表され、アプリの独自のバックエンド Web API にトークンを届けるために利用できます。 `offline_access` スコープは、リソースに長期アクセスするためにアプリは **refresh_token** を必要とすることを示します。 |
| redirect_uri |推奨 |authorization_code を受け取った、アプリケーションの redirect_uri。 |
| refresh_token |必須 |フローの第 2 段階で取得した元の refresh_token。 refresh_token を受け取るには、承認要求とトークン要求の両方でスコープ `offline_access` を使用していなければなりません。 |
| client_secret |必須 |[Azure Portal](https://portal.azure.com/) で生成したアプリケーション シークレット。 このアプリケーション シークレットは、重要なセキュリティ アーティファクトです。 サーバーに安全に保管する必要があります。 また、慎重を期して、このクライアント シークレットを定期的に変更してください。 |

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
| パラメーター | Description |
| --- | --- |
| not_before |トークンが有効と見なされる時間 (エポック時間)。 |
| token_type |トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| access_token |要求した署名付きの JWT トークン。 |
| scope |トークンの有効スコープ。後の利用のためにトークンのキャッシュに利用できます。 |
| expires_in |access_token が有効な時間の長さ (秒単位)。 |
| refresh_token |OAuth 2.0 の refresh_token。 現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。  refresh_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 詳細については、 [B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 |

エラー応答は次のようになります。

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する
ユーザーをアプリからサインアウトさせるとき、アプリの Cookie を消去する、あるいはユーザーとのセッションを終了するだけでは十分ではありません。 サインアウトするには、ユーザーを Azure AD にリダイレクトする必要もあります。 そうしない場合、ユーザーは資格情報を再入力しなくてもアプリで再認証されることがあります。 これは、Azure AD のシングル サインオン セッションが有効であるためです。

前述の「id_token を検証する」セクションで説明したものと同じ OpenID Connect メタデータ ドキュメントの一覧にある `end_session_endpoint` にユーザーをリダイレクトできます。

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| パラメーター | 必須 | Description |
| --- | --- | --- |
| p |必須 |ユーザーをアプリケーションからサインアウトさせるために使用するポリシー。 |
| post_logout_redirect_uri |推奨 |サインアウトの正常終了後にユーザーをリダイレクトする URL。 指定しない場合、Azure AD B2C はユーザーに汎用メッセージを表示します。 |

> [!NOTE]
> ユーザーを `end_session_endpoint` にリダイレクトすると、Azure AD B2C のユーザーのシングル サインオン状態が一部消去されますが、ユーザーがソーシャル ID プロバイダー (IDP) セッションからサインアウトされるわけではありません。 ユーザーがその後のサインインで同じ IDP を選択した場合は、資格情報を入力しなくても再認証されます。 ユーザーが B2C アプリケーションからサインアウトするとき、Facebook アカウントからの完全なサインアウトを望んでいるとは限りません。 ただし、ローカル アカウントの場合、ユーザーのセッションを適切に終了できなければなりません。
> 
> 

## <a name="use-your-own-b2c-tenant"></a>独自の B2C テナントを使用する
これらの要求を自分で試す場合、最初に次の 3 つの手順を実行し、上記のサンプルの値を独自の値に置換する必要があります。

* [B2C テナントを作成し](active-directory-b2c-get-started.md)、要求でテナントの名前を使用します。
* [アプリケーションを作成し](active-directory-b2c-app-registration.md)、アプリケーション ID と redirect_uri を取得します。 アプリに **web app/web api** を追加し、必要に応じて、**アプリケーション シークレット**を作成します。
* [ポリシーを作成し](active-directory-b2c-reference-policies.md) 、ポリシー名を取得します。




<!--HONumber=Dec16_HO5-->


