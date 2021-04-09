---
title: Microsoft ID プラットフォームと OAuth 2.0 認証コード フロー | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームによる OAuth 2.0 認証プロトコルの実装を使用して、Web アプリケーションを構築します。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: aeed031025b9c494b35886861c273e2a7f9d2ac4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653730"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft ID プラットフォームと OAuth 2.0 認証コード フロー

デバイスにインストールされているアプリに、Web API など、保護されているリソースにアクセスする権利を与えるために OAuth 2.0 認証コード付与を利用できます。 Microsoft ID プラットフォームによる OAuth 2.0 の実装を使用すると、サインインおよび API アクセスをモバイル アプリやデスクトップ アプリに追加できます。

この記事では、任意の言語を使用して、アプリケーションでプロトコルに対して直接プログラミングする方法について説明します。  可能な場合は、[トークンを取得してセキュリティで保護された Web API を呼び出す](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)代わりに、サポートされている Microsoft 認証ライブラリ (MSAL) を使用することをお勧めします。  また、[MSAL を使用するサンプル アプリ](sample-v2-code.md)も参照してください。

OAuth 2.0 承認コード フローは、 [OAuth 2.0 仕様のセクション 4.1](https://tools.ietf.org/html/rfc6749)で規定されています。 これは、[シングル ページ アプリ](v2-app-types.md#single-page-apps-javascript)、[Web アプリ](v2-app-types.md#web-apps)、[ネイティブにインストールされるアプリ](v2-app-types.md#mobile-and-native-apps)など、大半のアプリ タイプで認証と承認を行う際に使用されます。 このフローにより、アプリは、Microsoft ID プラットフォームによって保護されたリソースにアクセスするために使用できる access_token を安全に取得でき、また、追加の access_token を取得するための更新トークンや、サインインしたユーザーの ID トークンも安全に取得できます。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

まとめると、アプリケーションの全体的な認証フローは次のようになります。

![OAuth Auth Code Flow](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>シングル ページ アプリに必要なリダイレクト URI セットアップ

シングル ページ アプリケーションの承認コード フローでは、追加のセットアップが必要です。  [シングルページ アプリケーションの作成](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow)手順に従って、CORS に対して有効としてリダイレクト URI を正しくマークします。 CORS を有効にするために既存のリダイレクト URI を更新するには、マニフェスト エディターを開き、`replyUrlsWithType` セクションでリダイレクト URI の `type` フィールドを `spa` に設定します。 [認証] タブの [Web] セクションでリダイレクト URI をクリックし、承認コード フローを使用して、移行先の URI を選択することもできます。

`spa` のリダイレクトの種類は、暗黙的なフローと下位互換性があります。 トークンを取得するために暗黙的なフローを現在使用しているアプリは、問題なく `spa` のリダイレクト URI の種類に移行し、暗黙的なフローを引き続き使用することができます。

承認コード フローを使用しようとして、次のエラーが表示された場合:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

この場合、アプリの登録にアクセスし、アプリのリダイレクト URI を `spa` 型に更新します。

## <a name="request-an-authorization-code"></a>承認コードを要求する

承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。 この要求では、クライアントは、ユーザーからの `openid`、`offline_access`、および `https://graph.microsoft.com/mail.read ` のアクセス許可を要求します。  `Directory.ReadWrite.All` を使用した組織のディレクトリへのデータの書き込みなど、一部のアクセス許可は管理者によって制限されます。 アプリケーションが組織のユーザーにこれらのアクセス許可のいずれかへのアクセスを要求すると、ユーザーは、アプリのアクセス許可に同意する権限がないという内容のエラー メッセージを受け取ります。 管理者によって制限されるスコープへのアクセスを要求するには、全体管理者から直接要求する必要があります。  詳細については、「[管理者によって制限されるアクセス許可](v2-permissions-and-consent.md#admin-restricted-permissions)」を参照してください。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> この要求を実行するには、以下のリンクをクリックしてください。 サインイン後、ブラウザーは `https://localhost/myapp/` にリダイレクトされ、アドレス バーに `code` が含まれた状態になります。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| パラメーター    | 必須/省略可能 | 説明 |
|--------------|-------------|--------------|
| `tenant`    | required    | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。  |
| `client_id`   | required    | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた **アプリケーション (クライアント) ID**。  |
| `response_type` | required    | 承認コード フローでは `code` を指定する必要があります。 [ハイブリッド フロー](#request-an-id-token-as-well-hybrid-flow)を使用する場合は、`id_token` または `token` を含めることもできます。 |
| `redirect_uri`  | 必須 | アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 ネイティブ アプリやモバイル アプリの場合は、推奨される値 (埋め込みのブラウザーを使用するアプリの場合は `https://login.microsoftonline.com/common/oauth2/nativeclient`、システム ブラウザーを使用するアプリの場合は `http://localhost`) のいずれかを使用してください。 |
| `scope`  | required    | ユーザーに同意を求める [スコープ](v2-permissions-and-consent.md) の、スペースで区切られたリスト。  要求の `/authorize` の段階では、これに複数のリソースを含めることができ、ご利用のアプリが呼び出す必要がある複数の Web API に対して同意を取得することを許可します。 |
| `response_mode`   | 推奨 | 結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。 以下のいずれかを指定できます。<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` はリダイレクト URI でクエリ文字列パラメーターとしてコードを提供します。 暗黙的フローを使って ID トークンを要求する場合、[OpenID 仕様](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)で規定された `query` を使用することはできません。コードのみを要求する場合は、`query`、`fragment`、`form_post` のいずれかを使用できます。 `form_post` は、リダイレクト URI に対するコードを含んだ POST を実行します。 |
| `state`                 | 推奨 | 要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](https://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この値を使用すると、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードすることもできます。 |
| `prompt`  | 省略可能    | ユーザーとの必要な対話の種類を指定します。 現時点で有効な値は `login`、`none`、`consent` だけです。<br/><br/>- `prompt=login` は、その要求でユーザーに資格情報の入力を強制し、シングル サインオンを無効にします。<br/>- `prompt=none` はその反対であり、ユーザーにどのような対話型プロンプトも表示されないようにします。 シングル サインオンで確認なしで要求を完了できない場合は、Microsoft ID プラットフォームから `interaction_required` エラーが返されます。<br/>- `prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。<br/>- `prompt=select_account` では、シングル サインオンに割り込み、まったく別のアカウントの使用を選択するためのオプションとして、セッション内または記憶されているアカウント内のいずれかにある全アカウントを一覧表示するアカウント選択エクスペリエンスを提供します。<br/> |
| `login_hint`  | 省略可能    | ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。 アプリはしばしば前回のサインインから `preferred_username` 要求を抽出して再認証時にこのパラメーターを使用します。   |
| `domain_hint`  | 省略可能    | これが含まれていると、ユーザーがサインイン ページで実行する電子メール ベースの検出プロセスがスキップされ、多少効率化されたユーザー エクスペリエンスが提供されます (たとえば、フェデレーション ID プロバイダーへの送信など)。 多くの場合、アプリでは、前回のサインインから `tid` を抽出することで再認証時にこのパラメーターを使用します。 `tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用する必要があります。 それ以外の場合は、 `domain_hint=organizations`を指定します。  |
| `code_challenge`  | 推奨/必須 | PKCE (Proof Key for Code Exchange) を使用して承認コード付与をセキュリティ保護するために使用されます。 `code_challenge_method` が含まれている場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 これは、すべての種類のアプリケーション (パブリック クライアントと機密クライアントの両方) で推奨されるようになり、Microsoft ID プラットフォームでは[認可コード フローを使用するシングル ページ アプリ](reference-third-party-cookies-spas.md)で必須となりました。 |
| `code_challenge_method` | 推奨/必須 | `code_challenge` パラメーターの `code_verifier` をエンコードするために使用されるメソッド。 これは `S256` である *べき* ですが、何らかの理由によってクライアントで SHA256 がサポートされない場合、仕様では `plain` の使用が許可されています。 <br/><br/>除外されていると、`code_challenge` が含まれている場合、`code_challenge` はプレーンテキストであると見なされます。 Microsoft ID プラットフォームは `plain` と `S256` の両方をサポートします。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 これは、[承認コード フローを使用するシングル ページ アプリ](reference-third-party-cookies-spas.md)には必須です。|


現時点では、ユーザーに資格情報の入力と認証が求められます。 また、Microsoft ID プラットフォームでは、ユーザーが `scope` クエリ パラメーターに示されたアクセス許可に同意していることも確認されます。 いずれのアクセス許可にもユーザーが同意しなかった場合、必要なアクセス許可に同意するようユーザーに求めます。 アクセス許可、同意、マルチテナント アプリの詳細については、 [こちら](v2-permissions-and-consent.md)を参照してください。

ユーザーが認証され、同意すると、Microsoft ID プラットフォームは `response_mode` パラメーターで指定された方法を使用して、指定された `redirect_uri` でアプリに応答を返します。

#### <a name="successful-response"></a>成功応答

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```HTTP
GET http://localhost?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| パラメーター | 説明  |
|-----------|--------------|
| `code` | アプリが要求した authorization_code。 アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。 承認コードは有効期間が短く、通常 10 分後には期限切れとなります。 |
| `state` | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

また、ID トークンを要求し、アプリケーションの登録で暗黙的な許可が有効になっている場合には、その ID トークンを受け取ることができます。  これは ["ハイブリッド フロー"](#request-an-id-token-as-well-hybrid-flow) と呼ばれることもあり、ASP.NET のようなフレームワークで使用されます。

#### <a name="error-response"></a>エラー応答

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```HTTP
GET http://localhost?
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
| `unsupported_response_type` | 承認サーバーは要求に含まれる応答の種類をサポートしていません。 | 要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。 [ハイブリッド フロー](#request-an-id-token-as-well-hybrid-flow)で表示された場合、クライアント アプリの登録で ID トークンの暗黙的な許可設定を有効にする必要があることを示します。 |
| `server_error`  | サーバーで予期しないエラーが発生しました。| 要求をやり直してください。 これらのエラーは一時的な状況によって発生します。 クライアント アプリケーションは、一時的なエラーのため応答が遅れることをユーザーに説明する場合があります。 |
| `temporarily_unavailable`   | サーバーが一時的にビジー状態であるため、要求を処理できません。 | 要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |
| `invalid_resource`  | 対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 | このエラーは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| `login_required` | ユーザーが多すぎるか、見つかりません | クライアントからサイレント認証が要求されましたが (`prompt=none`)、ユーザーは 1 つも見つかりませんでした。 これは、セッションで複数のユーザーがアクティブになっているか、ユーザーがないことを意味する可能性があります。 このとき、選択されたテナントが考慮されます (たとえば、Azure AD アカウントが 2 つアクティブになっているか、Microsoft アカウントが 1 つあるかが考慮されます。`consumers` が選択されている場合はサイレント認証は機能します)。 |
| `interaction_required` | 要求にユーザーの介入が必要です。 | 追加の認証手順か同意が必要になります。 `prompt=none` なしで要求を再試行してください。 |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>ID トークンも要求する (ハイブリッド フロー)

認可コードの引き換え前にユーザーが誰であるかを確認するには、一般的に、アプリケーションで認可コードを要求するときに ID トークンも要求します。 これは、暗黙的な許可と認可コード フローが混在するため、"*ハイブリッド フロー*" と呼ばれます。 ハイブリッド フローは、コードの引き換え時にブロックなしでユーザーにページをレンダリングする Web アプリで一般的に使用されます (特に [ASP.NET](quickstart-v2-aspnet-core-webapp.md))。 シングルページ アプリも従来の Web アプリも、このモデルの待機時間短縮のメリットを得ることができます。

ハイブリッド フローは、前述の認可コード フローと同じですが、3 つの追加要素として新しいスコープ、新しい response_type、新しい `nonce` クエリ パラメーターがあり、これらはすべて ID トークンを要求するために必要です。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| 更新対象パラメーター | 必須/省略可能 | 説明 |
|---------------|-------------|--------------|
|`response_type`| 必須 | `id_token` の追加により、アプリケーションが `/authorize` エンドポイントからの応答で ID トークンを要求していることがサーバーに示されます。  |
|`scope`| 必須 | ID トークン用であり、更新して ID トークン スコープ (`openid` とオプションの `profile` および `email`) を含める必要があります。 |
|`nonce`| 必須|     要求に含まれる、アプリによって生成される値。この値は、結果の id_token に要求として含まれます。 アプリはこの値を確認することにより、トークン リプレイ攻撃を緩和できます。 通常、この値はランダム化された一意の文字列であり、要求の送信元を特定する際に使用できます。 |
|`response_mode`| 推奨 | 結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。 既定では、認可コードのみの場合は `query` に設定されますが、要求に id_token `response_type` を含める場合は `fragment` に設定します。  ただし、特に `http:/localhost` をリダイレクト URI として使用している場合は、アプリで `form_post` を使用することをお勧めします。 |

応答モードとして `fragment` を使用すると、ブラウザーでフラグメントが Web サーバーに渡されないため、リダイレクトからコードを読み取る Web アプリでは問題が発生する可能性があります。  このような状況では、すべてのデータがサーバーに送信されるようにするために、アプリで `form_post` 応答モードを使用してください。 

#### <a name="successful-response"></a>成功応答

`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| パラメーター | 説明  |
|-----------|--------------|
| `code` | アプリが要求した承認コード。 アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。 認可コードは有効期間が短く、通常 10 分後には期限切れとなります。 |
| `id_token` | "*暗黙的な許可*" によって発行されたユーザーの ID トークン。 同じ要求に `code` のハッシュである特別な `c_hash` 要求が含まれます。 |
| `state` | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の state 値が同じであることを検証します。 |

## <a name="request-an-access-token"></a>アクセス トークンを要求する

authorization_code を取得し、ユーザーからアクセス許可を得たら、`access_token` の `code` を目的のリソースに対して使うことができます。 これを行うには、`POST` 要求を `/token` エンドポイントに送信します。

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> を必ず置き換えてください)。 (`code` を置き換えるのを忘れないでください) [![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| パラメーター  | 必須/省略可能 | 説明     |
|------------|-------------------|----------------|
| `tenant`   | required   | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。  |
| `client_id` | required  | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページでアプリに割り当てられたアプリケーション (クライアント) ID。 |
| `grant_type` | required   | 承認コード フローでは `authorization_code` を指定する必要があります。   |
| `scope`      | オプション   | スペースで区切られたスコープのリスト。 このスコープはすべて、OIDC スコープ (`profile`、`openid`、`email`) に沿って、1 つのリソースからである必要があります。 スコープの詳細については、 [アクセス許可、同意、スコープ](v2-permissions-and-consent.md)に関するページを参照してください。 これは、承認コード フローの Microsoft 拡張機能であり、トークンの引き換え時にトークンが必要なリソースをアプリによって宣言できるようにするためのものです。|
| `code`          | required  | フローの最初の段階で取得した authorization_code。 |
| `redirect_uri`  | required  | authorization_code の取得に使用された同じ redirect_uri 値。 |
| `client_secret` | 機密 Web アプリには必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。 client_secret をデバイスや Web ページに確実に保存することはできないため、ネイティブ アプリやシングル ページ アプリではアプリケーションのシークレットを使用しないでください。 Web アプリや Web API では client_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。  ここで説明されているすべてのパラメーターと同様に、クライアント シークレットは、送信前に URL エンコードする必要があります。これは、通常、SDK によって実行される手順です。 URI エンコードの詳細については、[URI の一般構文の仕様](https://tools.ietf.org/html/rfc3986#page-12)に関する記事を参照してください。 |
| `code_verifier` | 推奨  | authorization_code を取得するために使用されたのと同じ code_verifier。 承認コード付与要求で PKCE が使用された場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 |

### <a name="successful-response"></a>成功応答

正常なトークン応答は次のようになります。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| パラメーター     | 説明   |
|---------------|------------------------------|
| `access_token`  | 要求されたアクセス トークン。 アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。  |
| `token_type`    | トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| `expires_in`    | アクセス トークンの有効期間 (秒)。 |
| `scope`         | access_token が有効である範囲。 省略可能 - これは非標準です。省略した場合、トークンはフローの最初の区間で要求されたスコープ用になります。 |
| `refresh_token` | OAuth 2.0 更新トークン。 現在のアクセス トークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のアクセス トークンを取得します。 Refresh_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 アクセス トークンの更新の詳細については、[後述のセクション](#refresh-the-access-token)を参照してください。 <br> **注:** `offline_access` スコープが要求された場合のみ提供されます。 |
| `id_token`      | JSON Web トークン (JWT)。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 アプリでは値をキャッシュして表示できます。また、機密クライアントではこの値を使用して承認を行うことができます。 id_token の詳細については、[`id_token reference`](id-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求された場合のみ提供されます。 |

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
| `invalid_request`  | 必要なパラメーターが不足しているなどのプロトコル エラーです。 | 要求またはアプリの登録を修正し、要求を再送信します。   |
| `invalid_grant`    | 承認コードまたは PKCE コード検証機能が無効か、有効期限切れです。 | `/authorize` エンドポイントに対する新しい要求を試し、code_verifier パラメーターが正しいことを確認します。  |
| `unauthorized_client` | 認証されたクライアントは、この承認付与の種類を使用する権限がありません。 | これは、通常、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| `invalid_client` | クライアント認証に失敗しました。  | クライアント資格情報が有効ではありません。 修正するには、アプリケーション管理者が資格情報を更新します。   |
| `unsupported_grant_type` | 承認サーバーが承認付与の種類をサポートしていません。 | 要求の付与の種類を変更します。 この種のエラーは、開発時にのみ発生し、初期テスト中に検出する必要があります。 |
| `invalid_resource` | 対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 | これは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。  |
| `interaction_required` | これは、OIDC 仕様では `/authorize` エンドポイントでのみ呼び出されるため、非標準です。要求にはユーザーの操作が必要です。 たとえば、追加の認証手順が必要です。 | 同じスコープで `/authorize` 要求を再試行します。 |
| `temporarily_unavailable` | サーバーが一時的にビジー状態であるため、要求を処理できません。 | 短い遅延後に要求を再試行します。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |
|`consent_required` | 要求にはユーザーの同意が必要です。 このエラーは、通常、OIDC 仕様に従って `/authorize` エンドポイントでのみ返されるため、非標準です。 要求するアクセス許可がクライアント アプリにないことを示すコード引き換えフローで `scope` パラメーターが使用された場合に返されます。  | クライアントでは、同意をトリガーするために、正しいスコープの `/authorize` エンドポイントにユーザーを返信する必要があります。 |
|`invalid_scope` | アプリによって要求されたスコープが無効です。  | 認証要求のスコープ パラメーターの値を有効な値に更新します。 |

> [!NOTE]
> シングル ページ アプリで `invalid_request` エラーが発生することがあります。これは、クロスオリジン トークンの使用が、"シングル ページ アプリケーション" クライアント タイプにしか許可されないことを示します。  これは、トークンを要求するために使用されるリダイレクト URI が `spa` リダイレクト URI としてマークされていないことを示します。  このフローを有効にする方法については、[アプリケーションの登録手順](#redirect-uri-setup-required-for-single-page-apps)に関する記事を参照してください。

## <a name="use-the-access-token"></a>アクセス トークンを使用する

`access_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、Web API への要求に使用することができます。

> [!TIP]
> ヘッダーを置き換えてください)。 (まず `Authorization` ヘッダーを置き換えます) [![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>アクセス トークンを更新する

アクセス トークンは有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。 アクセス トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。  更新トークンは、クライアントが既に同意を受け取っているすべてのアクセス許可に対して有効です。そのため、`scope=mail.read` に対する要求で発行された更新トークンを使用して、`scope=api://contoso.com/api/UseResource` に対する新しいアクセス トークンを要求できます。

Web アプリとネイティブ アプリの更新トークンには、指定の有効期間はありません。 通常、更新トークンの有効期間は比較的長いです。 ただし、場合によっては、更新トークンの有効期限が切れる、失効する、または目的の操作のための十分な特権がないことがあります。 クライアント アプリケーションは、[トークン発行エンドポイントから返されるエラー](#error-codes-for-token-endpoint-errors)を予期して正しく処理する必要があります。 ただし、シングル ページ アプリでは、24 時間の有効期限を持つトークンが取得され、毎日新しい認証が必要になります。  これは、サード パーティ Cookie が有効になっている場合は iframe でサイレントに実行できますが、Safari などのサード パーティ Cookie がないブラウザーでは、トップ レベルのフレーム (ページ全体のナビゲーションまたはポップアップ) で実行する必要があります。

新しいアクセス トークンを取得するために使用されたときに、更新トークンが失効していないにもかかわらず、古い更新トークンを破棄することを求められます。 [OAuth 2.0 仕様](https://tools.ietf.org/html/rfc6749#section-6)には次のようにあります。"承認サーバーで新しい更新トークンが発行される場合があります。この場合、クライアントは古い更新トークンを破棄し、新しい更新トークンに置き換える必要があります。 承認サーバーは新しい更新トークンをクライアントに発行した後に、古い更新トークンを取り消す場合があります。"

>[!IMPORTANT]
> `spa` として登録されたリダイレクト URI に送信される更新トークンの場合、更新トークンは 24 時間後に期限切れになります。 初期更新トークンを使用して取得された追加の更新トークンは、その有効期限を引き継ぎます。そのため、24 時間ごとに新しい更新トークンを取得するために、対話型認証を使用して承認コード フローを再実行するようにアプリを準備する必要があります。 ユーザーは自分の資格情報を入力する必要はなく、通常は UX も表示されず、アプリケーションをリロードするだけです。しかし、ブラウザーでは、ログイン セッションを表示するために、トップ レベル フレームのログイン ページにアクセスする必要があります。  これは、[サード パーティ Cookie をブロックするブラウザーのプライバシー機能](reference-third-party-cookies-spas.md)のためです。

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> を必ず置き換えてください)。 (`refresh_token` を置き換えるのを忘れないでください) [![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| パラメーター     | Type           | 説明        |
|---------------|----------------|--------------------|
| `tenant`        | required     | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。   |
| `client_id`     | required    | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた **アプリケーション (クライアント) ID**。 |
| `grant_type`    | required    | この段階の承認コード フローでは `refresh_token` を指定する必要があります。 |
| `scope`         | required    | スコープのスペース区切りリスト。 この段階で要求するスコープは、最初の承認コード要求段階で要求したスコープと同じか、またはそのサブセットである必要があります。 この要求で指定したスコープが複数のリソース サーバーにまたがる場合、Microsoft ID プラットフォームからは、最初のスコープで指定したリソースのトークンが返されます。 スコープの詳細については、 [アクセス許可、同意、スコープ](v2-permissions-and-consent.md)に関するページを参照してください。 |
| `refresh_token` | required    | フローの第 2 段階で取得した refresh_token。 |
| `client_secret` | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。 ネイティブ アプリでは使用しないでください。デバイスに client_secret を確実に保存することができません。 Web アプリや Web API では client_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。 このシークレットは URL エンコードする必要があります。 詳細については、[URI の一般構文の仕様](https://tools.ietf.org/html/rfc3986#page-12)に関する記事を参照してください。 |

#### <a name="successful-response"></a>成功応答

正常なトークン応答は次のようになります。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
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
