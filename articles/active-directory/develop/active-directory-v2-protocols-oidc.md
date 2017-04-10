---
title: "Azure Active Directory v2.0 と OpenID Connect プロトコル | Microsoft Docs"
description: "Azure AD の v2.0 で導入された OpenID Connect 認証プロトコルを利用し、Web アプリケーションを構築します。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3d5ad974c01e0ee3954da4f990da87338b2d1756
ms.openlocfilehash: 63b7e8bda46858d92dc128befdeb189cab53680f
ms.lasthandoff: 02/23/2017


---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Azure Active Directory v2.0 と OpenID Connect プロトコル
OpenID Connect は OAuth 2.0 を基盤として開発された認証プロトコルであり、ユーザーを Web アプリケーションに安全にサインインさせるために利用できます。 v2.0 エンドポイントによる OpenID Connect の実装を使用すると、サインインおよび API アクセスを Web ベースのアプリに追加できます。 この記事では、言語に依存しないでこの操作を行う方法を説明します。 Microsoft のオープンソース ライブラリを利用しないで、HTTP メッセージを送受信する方法について説明します。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。 v2.0 エンドポイントを使用するかどうかを判断するには、[v2.0 の制限](active-directory-v2-limitations.md)に関する記事を参照してください。
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 *承認*プロトコルを*認証*プロトコルとして使用できるように拡張したものです。これにより、OAuth を使用したシングル サインオンを行うことができます。 OpenID Connect には、*ID トークン*の概念が導入されています。ID トークンとは、クライアントがユーザーの本人性を確認できるセキュリティ トークンです。 ID トークンは、ユーザーに関する基本的なプロファイル情報も取得します。 OpenID Connect は OAuth 2.0 を拡張したものであるため、アプリは[承認サーバー](active-directory-v2-protocols.md#the-basics)で保護されるリソースにアクセスするための*アクセス トークン*を安全に取得できます。 サーバーでホストされ、ブラウザーでアクセスされる [Web アプリケーション](active-directory-v2-flows.md#web-apps)を構築している場合に、OpenID Connect を使用することをお勧めします。

## <a name="protocol-diagram-sign-in"></a>プロトコルのダイアグラム: サインイン
最も基本的なサインイン フローの手順を、次のダイアグラムに示します。 この記事では、各手順の詳細を説明します。

![OpenID Connect プロトコル: サインイン](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>OpenID Connect のメタデータ ドキュメントを取得する
OpenID Connect はメタデータ ドキュメントについて説明するものです。メタデータ ドキュメントは、アプリがサインインを実行するために必要な情報の大半を含んでいます。 これには、使用する URL、サービスの公開署名キーの場所などの情報が含まれます。 v2.0 エンドポイントの場合、次の OpenID Connect メタデータ ドキュメントを使用する必要があります。

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

`{tenant}` は、4 つの値のいずれかを使用できます。

| 値 | 説明 |
| --- | --- |
| `common` |個人の Microsoft アカウントを持つユーザーと Azure Active Directory (Azure AD) の職場/学校アカウントを持つユーザーのどちらもアプリケーションにサインインできます。 |
| `organizations` |Azure AD の職場/学校アカウントを持つユーザーのみがアプリケーションにサインインできます。 |
| `consumers` |個人の Microsoft アカウントを持つユーザーのみがアプリケーションにサインインできます。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` または `contoso.onmicrosoft.com` |特定の Azure AD テナントの職場/学校アカウントを持つユーザーのみがアプリケーションにサインインできます。 Azure AD テナントのフレンドリ ドメイン名か、テナントの GUID 識別子のいずれかを使用できます。 |

メタデータは、単純な JavaScript Object Notation (JSON) ドキュメントです。 例については、次のスニペットを参照してください。 スニペットの内容については、[OpenID Connect の仕様](https://openid.net)に詳しく記載されています。

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

通常、このメタデータ ドキュメントを使用して OpenID Connect ライブラリまたは SDK を構成します。ライブラリは、メタデータを使用して処理を実行します。 ただし、ビルド前の OpenID Connect ライブラリを使用していない場合は、v2.0 エンドポイントを使用した Web アプリへのサインインを実行するには、この記事で後述する手順を行ってください。

## <a name="send-the-sign-in-request"></a>サインイン要求を送信する
Web アプリでユーザーを認証する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。 この要求は、[OAuth 2.0 承認コード フロー](active-directory-v2-protocols-oauth-code.md)の最初の部分に似ていますが、次の重要な違いがあります。

* 要求の `scope` パラメーターにはスコープ `openid` が含まれる必要があります。
* `response_type` パラメーターには、`id_token` が設定されている必要があります。
* 要求には `nonce` パラメーターが含まれる必要があります。

次に例を示します。

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> この要求を実行するには、次のリンクをクリックしてください。 サインインした後、ブラウザーは、アドレス バーに ID トークンが指定された https://localhost/myapp/ にリダイレクトされます。 この要求では `response_mode=query` を使用していることに注意してください (デモ用のみで使用)。 `response_mode=form_post` を使用することをお勧めします。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| テナント |必須 |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御できます。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、[プロトコルの基本](active-directory-v2-protocols.md#endpoints)に関するセクションを参照してください。 |
| client_id |必須 |[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)によってアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。 `code` などの他の `response_types` の値が含まれる場合もあります。 |
| redirect_uri |推奨 |アプリのリダイレクト URI。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかのリダイレクト URI と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。 OpenID Connect では、スコープとして `openid` を指定する必要があります。このスコープは、承認 UI で "サインイン" アクセス許可に変換されます。 同意を求めるこの要求には他のスコープが含まれていてもかまいません。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な id_token 値に要求として追加されます。 アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| response_mode |推奨 |結果として得られた承認コードをアプリに返す際に使用するメソッドを指定します。 `query`、`form_post`、`fragment` のいずれかを指定できます。 Web アプリケーションでは、トークンをアプリケーションに最も安全に転送できるように、`response_mode=form_post` を使用することをお勧めします。 |
| state |推奨 |要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](http://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類を指定します。 現時点で有効な値は `login`、`none`、`consent` だけです。 `prompt=login` 要求は、その要求においてユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。 `prompt=none` 要求は、その逆です。 この要求は、ユーザーに対して対話形式のプロンプトを表示しません。 シングル サインオンによって自動的に要求を完了できない場合、v2.0 エンドポイントはエラーを返します。 `prompt=consent` 要求は、ユーザーがサインインした後に OAuth 同意ダイアログをトリガーします。 ダイアログでは、ユーザーにアプリへのアクセス許可を付与するよう要求します。 |
| login_hint |省略可能 |このパラメーターを使用すると、ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名および電子メール アドレス フィールドに入力することができます。 多くの場合、アプリは `preferred_username` 要求を使用して以前のサインインからユーザー名を抽出しておき、再認証時にこのパラメーターを使用します。 |
| domain_hint |省略可能 |この値は、`consumers` または `organizations` です。 これが含まれる場合、v2.0 のサインイン ページでユーザーが行う電子メール ベースの検出プロセスがスキップされ、ユーザー エクスペリエンスは若干簡素化されたものになります。 多くの場合、アプリは ID トークンから `tid` 要求を抽出して、再認証時にこのパラメーターを使用します。 `tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用します。 それ以外の場合は、 `domain_hint=organizations`を指定します。 |

現時点では、ユーザーに資格情報の入力と認証が求められます。 v2.0 エンドポイントは、`scope` クエリ パラメーターで指定されたアクセス許可にユーザーが同意済みであることの確認も行います。 いずれのアクセス許可にもユーザーが同意しなかった場合、v2.0 エンドポイントは必要なアクセス許可に同意するようユーザーに求めます。 詳細については、[アクセス許可、同意、マルチテナント アプリ](active-directory-v2-scopes.md)に関する記事を参照してください。

ユーザーが認証し、同意の許可を与えると、v2.0 エンドポイントは、`response_mode` パラメーターに指定されたメソッドを使い、指定されたリダイレクト URI でアプリに応答を返します。

### <a name="successful-response"></a>成功応答
`response_mode=form_post` を使用して成功した場合の応答は次のようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| パラメーター | Description |
| --- | --- |
| id_token |アプリが要求した ID トークン。 `id_token` パラメーターを使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。 ID トークンとその内容の詳細については、[v2.0 エンドポイント トークンのリファレンス](active-directory-v2-tokens.md)を参照してください。 |
| state |要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

### <a name="error-response"></a>エラー応答
アプリ側でエラーを処理できるよう、リダイレクト URI にはエラー応答も送信される場合があります。 エラーの場合の応答は次のようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用できるエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |

### <a name="error-codes-for-authorization-endpoint-errors"></a>承認エンドポイント エラーのエラー コード
次の表で、エラー応答の `error` パラメーターで返される可能性のあるエラー コードを説明します。

| エラー コード | 説明 | クライアント側の処理 |
| --- | --- | --- |
| invalid_request |必要なパラメーターが不足しているなどのプロトコル エラーです。 |要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。 |
| unauthorized_client |クライアント アプリケーションは、認証コードを要求できません。 |これは通常、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| access_denied |リソースの所有者が同意を拒否しました。 |クライアント アプリケーションは、ユーザーが同意しないと続行できないことを、ユーザーに通知できます。 |
| unsupported_response_type |承認サーバーは要求に含まれる応答の種類をサポートしていません。 |要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。 |
| server_error |サーバーで予期しないエラーが発生しました。 |要求をやり直してください。 これらのエラーは一時的な状況によって発生します。 クライアント アプリケーションは、一時的なエラーのため応答が遅れることをユーザーに説明する場合があります。 |
| temporarily_unavailable |サーバーが一時的にビジー状態であるため、要求を処理できません。 |要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |
| invalid_resource |対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 |これは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |

## <a name="validate-the-id-token"></a>ID トークンの検証
ユーザーを認証するには、ID トークンの受信だけでは不十分です。 ID トークンの署名を検証し、アプリの要件に従って、トークンに含まれている要求を確認する必要もあります。 v2.0 エンドポイントは、[JSON Web トークン (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

クライアント コードで ID トークンを検証することもできますが、ID トークンをバックエンド サーバーに送信して検証を実行するのが一般的な方法です。 ID トークンの署名を検証した後に、いくつかの要求を確認する必要があります。 [トークンの検証](active-directory-v2-tokens.md#validating-tokens)および[署名キーのロールオーバーに関する重要な情報](active-directory-v2-tokens.md#validating-tokens)の詳細など、詳しくは [v2.0 トークンのリファレンス](active-directory-v2-tokens.md)を参照してください。 ライブラリを使用してトークンを解析し、検証することをお勧めします。 ほとんどの言語とプラットフォームで、これらのライブラリを&1; つ以上使用できます。
<!--TODO: Improve the information on this-->

シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

* ユーザーまたは組織がアプリにサインアップ済みであることを確認する。
* ユーザーに必要な承認または特権があることを確認する。
* 多要素認証など特定の強度の認証が行われたことを確認する。

ID トークンに含まれている要求の詳細については、[v2.0 エンドポイント トークン リファレンス](active-directory-v2-tokens.md)を参照してください。

ID トークンの検証を完了したら、ユーザーとのセッションを開始できます。 ID トークンに含まれている要求を使用して、アプリのユーザーに関する情報を取得します。 この情報は、表示、記録、承認などに利用することができます。

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する
ユーザーをアプリからサインアウトさせるとき、アプリの Cookie を消去する、あるいはユーザーのセッションを終了するだけでは十分ではありません。 サインアウトするには、ユーザーを v2.0 エンドポイントにリダイレクトする必要もあります。 そうしないと、ユーザーは資格情報を再入力しなくてもアプリで再認証されることがあります。v2.0 エンドポイントのシングル サインオン セッションが有効であるためです。

OpenID Connect メタデータ ドキュメントの一覧にある `end_session_endpoint` にはユーザーをリダイレクトできます。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| パラメーターが含まれる必要があります。 | 条件 | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | 推奨 | サインアウトの正常終了後にユーザーをリダイレクトする URL。 このパラメーターを含めない場合、v2.0 エンドポイントによって生成された汎用メッセージがユーザーに表示されます。 この URL は、アプリ登録ポータルのアプリケーションに対する登録済みリダイレクト URI のいずれかと一致させる必要があります。  |

## <a name="single-sign-out"></a>シングル サインアウト
ユーザーが `end_session_endpoint` にリダイレクトされると、v2.0 エンドポイントは、ユーザーのセッションをブラウザーから消去します。 ただし、ユーザーは認証に Microsoft アカウントを使用する他のアプリケーションにサインインしたままになることがあります。 ユーザーがアプリケーションから同時にサインアウトできるように、v2.0 エンドポイントは、ユーザーが現在サインインしているすべてのアプリケーションの登録済み `LogoutUrl` に HTTP GET 要求を送信します。 アプリケーションは、ユーザーを識別するすべてのセッションを消去して、`200` 応答を返すことで、この要求に応答する必要があります。  アプリケーションでシングル サインアウトをサポートする場合は、アプリケーションのコードで `LogoutUrl` などを実装する必要があります。  `LogoutUrl` は、アプリ登録ポータルから設定できます。

## <a name="protocol-diagram-token-acquisition"></a>プロトコルのダイアグラム: トークンの取得
多くの Web アプリは、ユーザーをサインインさせるだけでなく、OAuth を使用してユーザーの代わりに Web サービスにアクセスする必要もあります。 このセクションでは、OpenID Connect を使ってユーザー認証を行うと同時に、OAuth 承認コード フローを使用している場合はアクセス トークンを取得するために使用する承認コードを取得します。

OpenID Connect によるサインインとトークン取得の完全なフローは、次のダイアグラムのようになります。 この記事の以降のセクションでは、各手順を詳しく説明します。

![OpenID Connect プロトコル: トークンの取得](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>アクセス トークンを取得する
アクセス トークンを取得するには、次のようにサインイン要求を変更します。

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> この要求を実行するには、次のリンクをクリックしてください。 サインインした後、ブラウザーは、アドレス バーに ID トークンとコードが指定された https://localhost/myapp/ にリダイレクトされます。 この要求では `response_mode=query` を使用していることに注意してください (デモ用のみで使用)。 `response_mode=form_post` を使用することをお勧めします。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

アクセス許可スコープを要求に組み込み、`response_type=id_token code` を使うことによって、v2.0 エンドポイントはユーザーが `scope` クエリ パラメーターで示されているアクセス許可に同意したことを確認します。 アクセス トークンと引き換えに、認証コードをアプリに返します。

### <a name="successful-response"></a>成功応答
`response_mode=form_post` を使用した場合の成功応答は次のようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| パラメーター | Description |
| --- | --- |
| id_token |アプリが要求した ID トークン。 この ID トークンを使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。 ID トークンとその内容の詳細については、[v2.0 エンドポイント トークンのリファレンス](active-directory-v2-tokens.md)を参照してください。 |
| code |アプリが要求した承認コード。 アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。 認証コードの有効期間は非常に短時間です。 通常、認証コードは約 10 分で期限切れになります。 |
| state |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

### <a name="error-response"></a>エラー応答
アプリ側でエラーを適切に処理できるよう、リダイレクト URI にはエラー応答も送信される場合があります。 エラーの場合の応答は次のようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用できるエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |

想定されるエラー コードと推奨されるクライアントの応答については、「[承認エンドポイント エラーのエラー コード](#error-codes-for-authorization-endpoint-errors)」を参照してください。

承認コードと ID トークンがある場合は、ユーザーをサインインさせ、代わりにアクセス トークンを取得できます。 ユーザーをサインインさせるには、[説明したとおり](#validate-the-id-token)に ID トークンを検証する必要があります。 アクセス トークンは、[OAuth プロトコルのドキュメント](active-directory-v2-protocols-oauth-code.md#request-an-access-token)に記載されている手順に従って取得できます。

