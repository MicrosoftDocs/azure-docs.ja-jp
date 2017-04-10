---
title: Azure Active Directory B2C | Microsoft Docs
description: "暗黙的フローを使用して直接シングルページ アプリを作成する方法。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: f2eb1f67c5687ac9bd8b94f6480617e6dc87ac9f
ms.openlocfilehash: ace35adc1cd2745ea11cb60429a1e7892d1f66d3


---
# <a name="azure-active-directory-b2c-sign-in-for-single-page-apps-using-oauth-20-implicit-flow"></a>Azure Active Directory B2C: OAuth 2.0 暗黙的フローを使用してシングルページ アプリにサインインする

> [!NOTE]
> この機能はプレビュー段階にあります。
> 

最新アプリの多くには、主に JavaScript で記述されたシングル ページ アプリのフロントエンドがあります。 AngularJS、Ember.js、または Durandal.js などのフレームワークを使用して記述されていることも少なくありません。 シングル ページ アプリなどの主にブラウザーで実行される JavaScript アプリには、認証に関して重要な課題があります。

* これらのアプリのセキュリティ特性は、従来のサーバーベースの Web アプリケーションとは大きく異なります。
* 多くの承認サーバーや ID プロバイダーでは、CORS 要求をサポートしていません。
* アプリからブラウザーにフル ページがリダイレクトされると、ユーザー エクスペリエンスに大きな悪影響が及びます。

これらのアプリケーションをサポートするために、Azure AD B2C は OAuth 2.0 暗黙的フローを使用します。  OAuth 2.0 承認の暗黙的許可フローは、[OAuth 2.0 仕様のセクション 4.2](http://tools.ietf.org/html/rfc6749) で説明されています。  このフローでは、アプリは Azure AD 承認エンドポイントから直接トークンを受け取るため、サーバー間の交換は実行されません。 すべての認証ロジックとセッション処理は、余分なページのリダイレクトなしに、JavaScript クライアント内ですべて行うことができます。

Azure AD B2C は、単純な認証と承認以上のことができるように標準の OAuth 2.0 暗黙的フローを拡張したものです。 [**ポリシー パラメーター**](active-directory-b2c-reference-policies.md)を導入しており、このパラメーターにより、OAuth 2.0 を利用し、サインアップ、サインイン、プロファイル管理などのユーザー操作をアプリに追加できます。 ここでは、暗黙的フローと Azure AD を使用して、シングルページ アプリケーションにこれらの各エクスペリエンスを実装する方法を説明します。  作業の開始にあたって、[Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) または [.NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) のサンプルを参照することもできます。

下の HTTP 要求例では、サンプル B2C ディレクトリの **fabrikamb2c.onmicrosoft.com**、サンプル アプリケーション、ポリシーを利用します。 これらの値を利用して、要求を自由に試すことができます。または、独自の値で置換できます。
[独自の B2C ディレクトリ、アプリケーション、ポリシーの取得方法](#use-your-own-b2c-tenant)について学習してください。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム
暗黙的なサインイン フローの全体像は次のようになります。各手順についてはこの後詳しく説明します。

![OpenId Connect Swimlanes](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>認証要求を送信する
Web アプリでユーザーを認証し、ポリシーを実行する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。 これはフローの対話部分であり、ユーザーは実際にポリシーに基づいて操作して、Azure AD エンドポイントから `id_token` を取得します。

この要求では、クライアントはユーザーから取得する必要があるアクセス許可を `scope` パラメーターで示し、実行するポリシーを `p` パラメーターで示します。 3 つの例を以下に示します (読みやすいように改行してあります)。それぞれ異なるポリシーが使用されています。 各要求の動作を感覚的に理解するために、要求をブラウザーに貼り付け、実行してみてください。

#### <a name="use-a-sign-in-policy"></a>サインイン ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>サインアップ ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>プロファイル編集ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| パラメーター |  | Description |
| --- | --- | --- |
| client_id |必須 |[Azure Portal](https://portal.azure.com/) によってアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。  response_type `token` が含まれる場合もあります。 ここで `token` を使用すると、アプリでは承認エンドポイントへ&2; 度目の要求を行うことなく、承認エンドポイントからアクセス トークンをすぐに受け取ることができます。  `token` response_type を使用する場合、`scope` パラメーターには、トークンを発行するリソースを示すスコープを含める必要があります。 |
| redirect_uri |推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| response_mode |推奨 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。  暗黙的フローでは `fragment` を指定する必要があります。 |
| scope |必須 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、**id_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します (これについては後で詳しく説明します)。 Web アプリの場合、 `offline_access` スコープは任意です。 リソースに長期アクセスするためにアプリは **refresh_token** を必要とすることを示します。 |
| state |推奨 |要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な id_token に要求として追加されます。 アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| p |必須 |実行されるポリシー。 B2C テナントに作成されたポリシーの名前です。 ポリシー名の値は、"b2c\_1\_" で始まっている必要があります。 ポリシーの詳細については、「 [拡張ポリシー フレームワーク](active-directory-b2c-reference-policies.md)」を参照してください。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類。 現時点で有効な値は 'login' のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。 シングル サインオンは作用しません。 |

この時点で、ユーザーにポリシーのワークフローの完了が求められます。 たとえば、ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりする必要があります。他にも、ポリシーの定義に基づき、多数の手順があります。

ユーザーがポリシーを完了すると、Azure AD は `response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。 実行されたポリシーに関係なく、上記のすべての例で応答はまったく同じになります。

#### <a name="successful-response"></a>成功応答
`response_mode=fragment` と `response_type=id_token+token` を使用した成功応答は、次のようになります (読みやすいように改行してあります)。

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| パラメーター | Description |
| --- | --- |
| access_token |アプリが要求したアクセス トークン。  アクセス トークンはデコードしないようにする必要があります。検証した場合、不明瞭な文字列として扱われることがあります。 |
| token_type |トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| expires_in |access_token が有効な時間の長さ (秒単位)。 |
| scope |トークンが有効であるスコープ。後の利用のために、トークンのキャッシュに利用できます。 |
| id_token |アプリが要求した id_token。 この id_token を使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。 id_token とその内容の詳細については、「[Azure AD B2C: トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。 |
| state |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### <a name="error-response"></a>エラー応答
アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーターが含まれる必要があります。 | Description |
| --- | --- |
| error |発生したエラーの種類を分類するために使用されるエラー コード文字列。 エラー コードは、エラー処理に使用できます。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| state |前の表の詳しい説明を参照してください。 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。|

## <a name="validate-the-idtoken"></a>id_token を検証する
単に id_token を受け取るだけでは、ユーザーを認証するには不十分です。id_token の署名を検証し、そのトークンに含まれる要求をアプリの要件に従って確認する必要があります。 Azure AD B2C は、[JSON Web トークン (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

優先言語にもよりますが、JWT を検証するためのさまざまなオープンソース ライブラリを入手できます。 独自の検証ロジックを実装するより、オープンソース ライブラリを試してみることを検討してください。 ライブラリを正しく利用する方法を模索するとき、ここにある情報が役に立つでしょう。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあって、アプリは、このエンドポイントを使用することで、Azure AD B2C に関する情報を実行時にフェッチすることができます。 この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。 B2C テナントにはポリシー別の JSON メタデータ ドキュメントがあります。 たとえば、`fabrikamb2c.onmicrosoft.com` の `b2c_1_sign_in` ポリシーのメタデータ ドキュメントは次の場所にあります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

この構成ドキュメントのプロパティの&1; つに `jwks_uri` があります。この値は同じポリシーで次のようになります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

id_token の署名で使用されたポリシー (とメタデータのフェッチ場所) は&2; つの方法で判断できます。 最初の方法です。ポリシー名が id_token の `acr` 要求に含まれています。 id_token の要求を解析する方法については、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。 もう&1; つの方法です。要求を発行するとき、`state` パラメーターの値に含まれるポリシーを符号化し、それから復号化して使用されたポリシーを判断します。 いずれの方法も完全に有効です。

OpenID Connect メタデータ エンドポイントからメタデータ ドキュメントを取得したら、このエンドポイントにある RSA 256 公開鍵を利用し、id_token の署名を検証できます。 このエンドポイントには常時、複数のキーがリストされており、それぞれのキーは `kid`で識別されます。 id_token のヘッダーにはさらに `kid` 要求が格納されていて、複数のキーのうち、どのキーが id_token の署名に使用されたかが、この要求によって示されます。 [トークンの検証](active-directory-b2c-reference-tokens.md#token-validation)を含む詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。
<!--TODO: Improve the information on this-->

id_token の署名を検証した後、次のようないくつかの要求でも検証が必要になります。

* トークン再生攻撃を防止するために、`nonce` 要求を検証します。 その値が、サインイン要求で指定した値と一致していることが必要です。
* id_token が確実に自分のアプリに対して発行されたものであることを確認するために、`aud` 要求を検証します。 その値がアプリのアプリケーション ID と一致している必要があります。
* id_token の有効期限が切れていないことを確認するために、`iat` 要求と `exp` 要求を検証します。

他にも実行する必要がある検証がいくつかあります。これについては、[OpenID Connect Core の仕様](http://openid.net/specs/openid-connect-core-1_0.html)で詳しく説明されています。  シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

* ユーザー/組織がアプリにサインアップ済みであることを確認する。
* 適切な承認/特権がユーザーにあることを確認する。
* Azure Multi-Factor Authentication など特定の強度の認証が行われたことを確認する。

id_token に含まれる要求の詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。

id_token を十分検証したら、ユーザーとのセッションを開始し、id_token に含まれる要求を使ってそのユーザーに関する情報をアプリの中で取得することができます。 取得した情報は、表示、記録、承認などに利用することができます。

## <a name="get-access-tokens"></a>アクセス トークンを取得する
Web アプリに必要なことがポリシーの実行のみであれば、以降のいくつかのセクションを省略できます。 以降のセクションは、Web アプリで Web API を認証呼び出しする必要があり、その呼び出しも Azure AD B2C で保護する場合にのみ適用されます。

ユーザーをシングル ページ アプリにサインインさせたので、Azure AD によってセキュリティ保護された Web API を呼び出すためのアクセス トークンを取得できます。  このメソッドを使用すると、`token` response_type を使用してトークンを既に取得している場合でも、再度サインインするためにユーザーをリダイレクトする必要なく、その他のリソースのトークンを取得できます。

通常の Web アプリ フローでは、この操作として、`/token` エンドポイントへの要求を作成します。  ただし、エンドポイントは CORS 要求をサポートしていないため、AJAX 呼び出しでトークンの取得や更新を実行することはできません。  代わりに、非表示の iframe で暗黙的フローを使用して、他の Web API 用の新しいトークンを取得できます。

```
// Line breaks for legibility only

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| パラメーター |  | Description |
| --- | --- | --- |
| client_id |必須 |[Azure Portal](https://portal.azure.com/) によってアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。  response_type `token` が含まれる場合もあります。 ここで `token` を使用すると、アプリでは承認エンドポイントへ&2; 度目の要求を行うことなく、承認エンドポイントからアクセス トークンをすぐに受け取ることができます。  `token` response_type を使用する場合、`scope` パラメーターには、トークンを発行するリソースを示すスコープを含める必要があります。 |
| redirect_uri |推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。  ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。  トークンを取得するには、該当リソースに必要なすべてのスコープを含めてください。 |
| response_mode |推奨 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。  `query`、`form_post`、`fragment` のいずれかを指定できます。 |
| state |推奨 |要求に含まれ、トークンの応答として返される値。  任意の文字列を指定することができます。  クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。  この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な id_token に要求として追加されます。  アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。  通常この値は、要求の送信元を特定する、ランダム化された一意の文字列です。 |
| prompt |必須 |非表示の iframe でトークンを更新および取得するには、iframe がサインイン ページでハングせずにすぐに応答できるように、`prompt=none` を使用します。 |
| login_hint |必須 |非表示の iframe のトークンを更新および取得するには、ある時点でのユーザーの複数のセッションを区別できるよう、このヒントにユーザーのユーザー名を含める必要があります。 `preferred_username` 要求を使用すると、前回のサインインからユーザー名を抽出できます。 |
| domain_hint |必須 |`consumers` か `organizations` のいずれかを指定できます。  非表示の iframe のトークンを更新および取得するには、要求に domain_hint を含める必要があります。  使用する値を決定するには、前回サインイン時の id_token から `tid` 要求を抽出します。  `tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用する必要があります。  それ以外の場合は、 `domain_hint=organizations`を指定します。 |

`prompt=none` パラメーターを設定することによって、要求はすぐに成功または失敗し、アプリケーションに戻ります。  成功すると、`response_mode` パラメーターで指定された方法を使用して、指定された `redirect_uri` でアプリに応答が送信されます。

#### <a name="successful-response"></a>成功応答
`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| パラメーターが含まれる必要があります。 | Description |
| --- | --- |
| access_token |アプリが要求したトークン。 |
| token_type |常に `Bearer` になります。 |
| state |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |
| expires_in |アクセス トークンの有効期間 (秒)。 |
| scope |アクセス トークンが有効である範囲。 |

#### <a name="error-response"></a>エラー応答
アプリ側で適切に処理できるように、 `redirect_uri` にエラーの応答が送信される場合もあります。  `prompt=none`の場合、予期されるエラーは次のようになります。

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

Iframe 要求でこのエラーを受信した場合、ユーザーは対話形式でもう一度サインインして新しいトークンを取得する必要があります。  この場合は、アプリケーションに適した任意の方法で処理できます。

## <a name="refreshing-tokens"></a>トークンを更新する
`id_token` と `access_token` はどちらも短時間で期限切れになるため、トークンを定期的に更新するようにアプリを準備しておく必要があります。  どちらの種類のトークンを更新する場合も、Azure AD の動作を制御する `prompt=none` パラメーターを使用して、上記と同じ非表示の iframe 要求を実行します。  新しい `id_token` を取得するには、`nonce` パラメーターに加えて、必ず `response_type=id_token` と `scope=openid` を使用してください。

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する
ユーザーをアプリからサインアウトさせる場合は、サインアウトする Azure AD にユーザーをリダイレクトします。 そうしない場合、ユーザーは資格情報を再入力しなくてもアプリで再認証されることがあります。 これは、Azure AD のシングル サインオン セッションが有効であるためです。

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
これらの要求を自分で試す場合、最初に次の&3; つの手順を実行し、上記のサンプルの値を独自の値に置換する必要があります。

* [B2C テナントを作成し](active-directory-b2c-get-started.md)、要求でテナントの名前を使用します。
* [アプリケーションを作成し](active-directory-b2c-app-registration.md)、アプリケーション ID と redirect_uri を取得します。 アプリに **web app/web api** を追加し、必要に応じて、**アプリケーション シークレット**を作成します。
* [ポリシーを作成し](active-directory-b2c-reference-policies.md) 、ポリシー名を取得します。

## <a name="samples"></a>サンプル

* [Node.JS を使用してシングルページ アプリを作成する](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [.NET を使用してシングルページ アプリを作成する](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)




<!--HONumber=Feb17_HO2-->


