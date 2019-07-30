---
title: Microsoft ID プラットフォームと OAuth 認証コード フロー | Azure
description: Microsoft ID プラットフォームで導入された OAuth 2.0 認証プロトコルを利用し、Web アプリケーションを構築します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8244707ec44b9afc51d5f2c21c3e95ff0692cfb
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482357"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft ID プラットフォームと OAuth 2.0 認証コード フロー

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

デバイスにインストールされているアプリに、Web API など、保護されているリソースにアクセスする権利を与えるために OAuth 2.0 認証コード付与を利用できます。 Microsoft ID プラットフォームによる OAuth 2.0 の実装を使用すると、サインインおよび API アクセスをモバイル アプリやデスクトップ アプリに追加できます。 このガイドでは、[Azure オープンソース認証ライブラリ](reference-v2-libraries.md)を利用せず、HTTP メッセージを送受信する方法について説明します。本ガイドは言語非依存です。

> [!NOTE]
> Microsoft ID プラットフォーム エンドポイントでは、Azure Active Directory のすべてのシナリオや機能がサポートされているわけではありません。 Microsoft ID プラットフォーム エンドポイントを使用すべきかどうかを判定するには、[Microsoft ID プラットフォームの制限](active-directory-v2-limitations.md)に関するページを参照してください。

OAuth 2.0 承認コード フローは、 [OAuth 2.0 仕様のセクション 4.1](https://tools.ietf.org/html/rfc6749)で規定されています。 [Web アプリ](v2-app-types.md#web-apps)や[ネイティブにインストールされるアプリ](v2-app-types.md#mobile-and-native-apps)を含め、大半のアプリ タイプで認証と承認を行う際にこのフローが使用されます。 アプリは、このフローによって安全に access_tokens を取得し、Microsoft ID プラットフォーム エンドポイントを使用して保護されているリソースにアクセスすることができます。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

まとめると、ネイティブ/モバイル アプリケーションの全体的な認証フローは次のようになります。

![OAuth Auth Code Flow](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>承認コードを要求する

承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。 この要求で、クライアントは、ユーザーから取得する必要のあるアクセス許可を指定します。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> この要求を実行するには、以下のリンクをクリックしてください。 サインイン後、ブラウザーは `https://localhost/myapp/` にリダイレクトされ、アドレス バーに `code` が含まれた状態になります。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| パラメーター    | 必須/省略可能 | 説明 |
|--------------|-------------|--------------|
| `tenant`    | 必須    | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。  |
| `client_id`   | 必須    | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた**アプリケーション (クライアント) ID**。  |
| `response_type` | 必須    | 承認コード フローでは `code` を指定する必要があります。       |
| `redirect_uri`  | 必須 | アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 ネイティブ アプリとモバイル アプリでは、`https://login.microsoftonline.com/common/oauth2/nativeclient` の既定値を使用します。   |
| `scope`  | 必須    | ユーザーに同意を求める [スコープ](v2-permissions-and-consent.md) の、スペースで区切られたリスト。  要求の `/authorize` の段階では、これに複数のリソースを含めることができ、ご利用のアプリが呼び出す必要がある複数の Web API に対して同意を取得することを許可します。 |
| `response_mode`   | 推奨 | 結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。 以下のいずれかを指定できます。<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` はリダイレクト URI でクエリ文字列パラメーターとしてコードを提供します。 暗黙的フローを使って ID トークンを要求する場合、[OpenID 仕様](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)で規定された `query` を使用することはできません。コードのみを要求する場合は、`query`、`fragment`、`form_post` のいずれかを使用できます。 `form_post` は、リダイレクト URI に対するコードを含んだ POST を実行します。 詳細については、[OpenID Connect プロトコル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)に関するページを参照してください。  |
| `state`                 | 推奨 | 要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](https://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この値を使用すると、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードすることもできます。 |
| `prompt`  | 省略可能    | ユーザーとの必要な対話の種類を指定します。 現時点で有効な値は `login`、`none`、`consent` だけです。<br/><br/>- `prompt=login` は、その要求でユーザーに資格情報の入力を強制し、シングル サインオンを無効にします。<br/>- `prompt=none` はその反対であり、ユーザーにどのような対話型プロンプトも表示されないようにします。 シングル サインオンで確認なしで要求を完了できない場合は、Microsoft ID プラットフォーム エンドポイントから `interaction_required` エラーが返されます。<br/>- `prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| `login_hint`  | 省略可能    | ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。 アプリはしばしば前回のサインインから `preferred_username` 要求を抽出して再認証時にこのパラメーターを使用します。   |
| `domain_hint`  | 省略可能    | `consumers` か `organizations` のいずれかを指定できます。<br/><br/>これが含まれていると、ユーザーがサインイン ページで実行する電子メール ベースの検出プロセスがスキップされ、多少効率化されたユーザー エクスペリエンスが提供されます。 多くの場合、アプリでは、前回のサインインから `tid` を抽出することで再認証時にこのパラメーターを使用します。 `tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用する必要があります。 それ以外の場合は、 `domain_hint=organizations`を指定します。  |
| `code_challenge_method` | 省略可能    | `code_challenge` パラメーターの `code_verifier` をエンコードするために使用されるメソッド。 次のいずれかの値を指定できます。<br/><br/>- `plain` <br/>- `S256`<br/><br/>除外されていると、`code_challenge` が含まれている場合、`code_challenge` はプレーンテキストであると見なされます。 Microsoft ID プラットフォームは `plain` と `S256` の両方をサポートします。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 |
| `code_challenge`  | 省略可能 | ネイティブ クライアントからの PKCE (Proof Key for Code Exchange) を使用して承認コード付与をセキュリティ保護するために使用されます。 `code_challenge_method` が含まれている場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 |

現時点では、ユーザーに資格情報の入力と認証が求められます。 Microsoft ID プラットフォーム エンドポイントではまた、ユーザーが `scope` クエリ パラメーターに示されたアクセス許可に同意していることも確認されます。 いずれのアクセス許可にもユーザーが同意しなかった場合、必要なアクセス許可に同意するようユーザーに求めます。 アクセス許可、同意、マルチテナント アプリの詳細については、 [こちら](v2-permissions-and-consent.md)を参照してください。

ユーザーが認証し、同意を許可すると、Microsoft ID プラットフォーム エンドポイントは `response_mode` パラメーターで指定された方法を使用して、アプリの指定された `redirect_uri` に応答を返します。

#### <a name="successful-response"></a>成功応答

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| パラメーター | 説明  |
|-----------|--------------|
| `code` | アプリが要求した authorization_code。 アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。 承認コードは有効期間が短く、通常 10 分後には期限切れとなります。 |
| `state` | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### <a name="error-response"></a>エラー応答

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明  |
|----------|------------------|
| `error`  | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>承認エンドポイント エラーのエラー コード

次の表で、エラー応答の `error` パラメーターで返される可能性のあるさまざまなエラー コードを説明します。

| エラー コード  | 説明    | クライアント側の処理   |
|-------------|----------------|-----------------|
| `invalid_request` | 必要なパラメーターが不足しているなどのプロトコル エラーです。 | 要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。 |
| `unauthorized_client` | クライアント アプリケーションは、承認コードの要求を許可されていません。 | 通常、このエラーは、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| `access_denied`  | リソースの所有者が同意を拒否しました。  | クライアント アプリケーションは、ユーザーが同意しないと続行できないことを、ユーザーに通知できます。 |
| `unsupported_response_type` | 承認サーバーは要求に含まれる応答の種類をサポートしていません。 | 要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。  |
| `server_error`  | サーバーで予期しないエラーが発生しました。| 要求をやり直してください。 これらのエラーは一時的な状況によって発生します。 クライアント アプリケーションは、一時的なエラーのため応答が遅れることをユーザーに説明する場合があります。 |
| `temporarily_unavailable`   | サーバーが一時的にビジー状態であるため、要求を処理できません。 | 要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |
| `invalid_resource`  | 対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 | このエラーは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| `login_required` | ユーザーが多すぎるか、見つかりません | クライアントからサイレント認証が要求されましたが (`prompt=none`)、ユーザーは 1 つも見つかりませんでした。 これは、セッションで複数のユーザーがアクティブになっているか、ユーザーがないことを意味する可能性があります。 このとき、選択されたテナントが考慮されます (たとえば、Azure AD アカウントが 2 つアクティブになっているか、Microsoft アカウントが 1 つあるかが考慮されます。`consumers` が選択されている場合はサイレント認証は機能します)。 |
| `interaction_required` | 要求にユーザーの介入が必要です。 | 追加の認証手順か同意が必要になります。 `prompt=none` なしで要求を再試行してください。 |

## <a name="request-an-access-token"></a>アクセス トークンを要求する

authorization_code を取得し、ユーザーからアクセス許可を得たら、`access_token` の `code` を目的のリソースに対して使うことができます。 これを行うには、`POST` 要求を `/token` エンドポイントに送信します。

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> を必ず置き換えてください)。 (`code` を置き換えるのを忘れないでください) [![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| パラメーター  | 必須/省略可能 | 説明     |
|------------|-------------------|----------------|
| `tenant`   | 必須   | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。  |
| `client_id` | 必須  | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページでアプリに割り当てられたアプリケーション (クライアント) ID。 |
| `grant_type` | 必須   | 承認コード フローでは `authorization_code` を指定する必要があります。   |
| `scope`      | 必須   | スコープのスペース区切りリスト。 この段階で要求するスコープは、最初の段階で要求したスコープと同じか、またはそのサブセットである必要があります。 このスコープはすべて、OIDC スコープ (`profile`、`openid`、`email`) に沿って、1 つのリソースからである必要があります。 スコープの詳細については、 [アクセス許可、同意、スコープ](v2-permissions-and-consent.md)に関するページを参照してください。 |
| `code`          | 必須  | フローの最初の段階で取得した authorization_code。 |
| `redirect_uri`  | 必須  | authorization_code の取得に使用された同じ redirect_uri 値。 |
| `client_secret` | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。 client_secret をデバイスに確実に保存することはできないため、ネイティブ アプリではアプリケーションのシークレットを使用しないでください。 Web アプリや Web API では client_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。  クライアント シークレットは、送信前に URL エンコードされる必要があります。  |
| `code_verifier` | 省略可能  | authorization_code を取得するために使用されたのと同じ code_verifier。 承認コード付与要求で PKCE が使用された場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 |

### <a name="successful-response"></a>成功応答

正常なトークン応答は次のようになります。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| パラメーター     | 説明   |
|---------------|------------------------------|
| `access_token`  | 要求されたアクセス トークン。 アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。  |
| `token_type`    | トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| `expires_in`    | アクセス トークンの有効期間 (秒)。 |
| `scope`         | access_token が有効である範囲。 |
| `refresh_token` | OAuth 2.0 更新トークン。 現在のアクセス トークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のアクセス トークンを取得します。 Refresh_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 アクセス トークンの更新の詳細については、[後述のセクション](#refresh-the-access-token)を参照してください。 <br> **注:** `offline_access` スコープが要求された場合のみ提供されます。 |
| `id_token`      | JSON Web トークン (JWT)。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 id_token の詳細については、[`id_token reference`](id-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求された場合のみ提供されます。 |

### <a name="error-response"></a>エラー応答

エラー応答は次のようになります。

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| パラメーター         | 説明    |
|-------------------|----------------|
| `error`       | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| `error_codes` | 診断に役立つ STS 固有のエラー コードの一覧。  |
| `timestamp`   | エラーが発生した時刻。 |
| `trace_id`    | 診断に役立つ、要求の一意の識別子。 |
| `correlation_id` | コンポーネント間での診断に役立つ、要求の一意の識別子。 |

### <a name="error-codes-for-token-endpoint-errors"></a>トークン エンドポイント エラーのエラー コード

| エラー コード         | 説明        | クライアント側の処理    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 必要なパラメーターが不足しているなどのプロトコル エラーです。 | 要求を修正し再送信します。   |
| `invalid_grant`    | 承認コードまたは PKCE コード検証機能が無効か、有効期限切れです。 | `/authorize` エンドポイントに対する新しい要求を試し、code_verifier パラメーターが正しいことを確認します。  |
| `unauthorized_client` | 認証されたクライアントは、この承認付与の種類を使用する権限がありません。 | これは、通常、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| `invalid_client` | クライアント認証に失敗しました。  | クライアント資格情報が有効ではありません。 修正するには、アプリケーション管理者が資格情報を更新します。   |
| `unsupported_grant_type` | 承認サーバーが承認付与の種類をサポートしていません。 | 要求の付与の種類を変更します。 この種のエラーは、開発時にのみ発生し、初期テスト中に検出する必要があります。 |
| `invalid_resource` | 対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 | これは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。  |
| `interaction_required` | 要求にユーザーの介入が必要です。 たとえば、追加の認証手順が必要です。 | 同じリソースで要求を再試行します。  |
| `temporarily_unavailable` | サーバーが一時的にビジー状態であるため、要求を処理できません。 | 要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |

## <a name="use-the-access-token"></a>アクセス トークンを使用する

`access_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、Web API への要求に使用することができます。

> [!TIP]
> ヘッダーを置き換えてください)。 (まず `Authorization` ヘッダーを置き換えます) [![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>アクセス トークンを更新する

アクセス トークンは有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。 アクセス トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。  更新トークンは、クライアントが既に同意を受け取っているすべてのアクセス許可に対して有効です。そのため、`scope=mail.read` に対する要求で発行された更新トークンを使用して、`scope=api://contoso.com/api/UseResource` に対する新しいアクセス トークンを要求できます。  

更新トークンには、指定された有効期間はありません。 通常、更新トークンの有効期間は比較的長いです。 ただし、場合によっては、更新トークンの有効期限が切れる、失効する、または目的の操作のための十分な特権がないことがあります。 クライアント アプリケーションは、[トークン発行エンドポイントから返されるエラー](#error-codes-for-token-endpoint-errors)を予期して正しく処理する必要があります。 

新しいアクセス トークンを取得するために使用されたときに、更新トークンが失効していないにもかかわらず、古い更新トークンを破棄することを求められます。 [OAuth 2.0 仕様](https://tools.ietf.org/html/rfc6749#section-6)には次のようにあります。"承認サーバーで新しい更新トークンが発行される場合があります。この場合、クライアントは古い更新トークンを破棄し、新しい更新トークンに置き換える必要があります。 承認サーバーは新しい更新トークンをクライアントに発行した後に、古い更新トークンを取り消す場合があります。"  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> を必ず置き換えてください)。 (`refresh_token` を置き換えるのを忘れないでください) [![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| パラメーター     |                | 説明        |
|---------------|----------------|--------------------|
| `tenant`        | 必須     | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。   |
| `client_id`     | 必須    | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた**アプリケーション (クライアント) ID**。 |
| `grant_type`    | 必須    | この段階の承認コード フローでは `refresh_token` を指定する必要があります。 |
| `scope`         | 必須    | スコープのスペース区切りリスト。 この段階で要求するスコープは、最初の承認コード要求段階で要求したスコープと同じか、またはそのサブセットである必要があります。 この要求で指定したスコープが複数のリソース サーバーにまたがる場合、Microsoft ID プラットフォーム エンドポイントからは、最初のスコープで指定したリソースのトークンが返されます。 スコープの詳細については、 [アクセス許可、同意、スコープ](v2-permissions-and-consent.md)に関するページを参照してください。 |
| `refresh_token` | 必須    | フローの第 2 段階で取得した refresh_token。 |
| `client_secret` | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。 ネイティブ アプリでは使用しないでください。デバイスに client_secret を確実に保存することができません。 Web アプリや Web API では client_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。 |

#### <a name="successful-response"></a>成功応答

正常なトークン応答は次のようになります。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| パラメーター     | 説明         |
|---------------|-------------------------------------------------------------|
| `access_token`  | 要求されたアクセス トークン。 アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| `token_type`    | トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| `expires_in`    | アクセス トークンの有効期間 (秒)。   |
| `scope`         | access_token が有効である範囲。    |
| `refresh_token` | 新しい OAuth 2.0 更新トークン。 できるだけ長い時間、更新トークンを有効な状態に維持するためには、この新しく取得した更新トークンで古い更新トークンを置き換える必要があります。 <br> **注:** `offline_access` スコープが要求された場合のみ提供されます。|
| `id_token`      | 無署名の JSON Web トークン (JWT)。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 id_token の詳細については、[`id_token reference`](id-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求された場合のみ提供されます。 |

#### <a name="error-response"></a>エラー応答

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| パラメーター         | 説明                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。           |
| `error_codes` |診断に役立つ STS 固有のエラー コードの一覧。 |
| `timestamp` | エラーが発生した時刻。 |
| `trace_id` | 診断に役立つ、要求の一意の識別子。 |
| `correlation_id` | コンポーネント間での診断に役立つ、要求の一意の識別子。 |

エラー コードとクライアントに推奨される対処法については、「[トークン エンドポイント エラーのエラー コード](#error-codes-for-token-endpoint-errors)」を参照してください。
