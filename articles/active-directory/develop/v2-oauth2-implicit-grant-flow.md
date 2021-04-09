---
title: OAuth 2.0 暗黙的な許可のフロー - Microsoft ID プラットフォーム | Azure
description: Microsoft ID プラットフォームの暗黙的なフローを使用してシングルページ アプリをセキュリティで保護します。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226490"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft ID プラットフォームと暗黙的な許可のフロー

Microsoft ID プラットフォームでは、[OAuth 2.0 の仕様](https://tools.ietf.org/html/rfc6749#section-4.2)で説明されているように、OAuth 2.0 の暗黙的な許可フローがサポートされています。 暗黙的な許可には、トークン (ID トークンまたはアクセス トークン) が /token エンドポイントではなく /authorize エンドポイントから直接返されるという特徴があります。 これは多くの場合、"ハイブリッド フロー" と呼ばれる[承認コード フロー](v2-oauth2-auth-code-flow.md)の一部として使用され、承認コードと共に /authorize 要求で ID トークンを取得します。

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>認証コード フローを優先する

[サード パーティの Cookie をブラウザーから削除する](reference-third-party-cookies-spas.md)というプランでは、**暗黙的な許可のフローは、認証方法として適切ではありません**。  暗黙的なフローの[サイレント SSO 機能](#getting-access-tokens-silently-in-the-background)は、サード パーティの Cookie がないと機能しないため、新しいトークンを取得しようとするとアプリケーションが中断します。 新しいアプリケーションではすべて、暗黙的なフローの代わりにシングル ページ アプリをサポートする[承認コード フロー](v2-oauth2-auth-code-flow.md)を使用することを強くお勧めします。また、既存のシングル ページ アプリでも[承認コードへのフローへの移行を開始する](migrate-spa-implicit-to-auth-code.md)ことをお勧めします。

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 の暗黙的な許可の適切なシナリオ

暗黙的な許可は、[サード パーティの Cookie](reference-third-party-cookies-spas.md) がなくてもアプリケーションに影響を与えないサインイン フローの最初の対話的な部分でしか信頼できません。 つまり、アプリケーションが承認エンドポイントからのトークンとコードを要求するハイブリッド フローの一部としてのみこの許可を使用すべきです。 そうすることで、アプリケーションは更新トークンと交換可能なコードを受け取ることができるため、アプリケーションのログイン セッションは時間が経過しても有効なままになります。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

次の図は、暗黙的なサインイン フローの全体像を示しています。各手順については、この後のセクションで詳しく説明します。

![暗黙的なサインイン フローを示す図](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>サインイン要求を送信する

最初にユーザーをアプリにサインインするために、[OpenID Connect](v2-protocols-oidc.md) 認証要求を送信し、Microsoft ID プラットフォームから `id_token` を取得します。

> [!IMPORTANT]
> ID トークンおよびアクセス トークンを正しく要求するには、[Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページのアプリ登録で、 **[暗黙の付与およびハイブリッド フロー]** セクションの **[ID トークン]** および **[アクセス トークン]** を選択して、対応する暗黙的な許可フローを有効にする必要があります。 それが有効でない場合は、`unsupported_response` エラー `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'` が返されます。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 暗黙的フローを使用したサインインをテストするには、<a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. をクリックします。</a>サインイン後、ブラウザーは `https://localhost/myapp/` にリダイレクトされ、アドレス バーに `id_token` が含まれた状態になります。
>

| パラメーター | Type | 説明 |
| --- | --- | --- |
| `tenant` | required |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| `client_id` | required | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページでアプリに割り当てられたアプリケーション (クライアント) ID。 |
| `response_type` | required |OpenID Connect サインインでは、 `id_token` を指定する必要があります。 response_type `token` が含まれる場合もあります。 ここで `token` を使用すると、アプリでは承認エンドポイントへ 2 度目の要求を行うことなく、承認エンドポイントからアクセス トークンをすぐに受け取ることができます。 `token` response_type を使用する場合、`scope` パラメーターには、トークンを発行するリソースを示すスコープを含める必要があります (たとえば、Microsoft Graph では user.read)。 また、[承認コード フロー](v2-oauth2-auth-code-flow.md)で使用するため、承認コードを提供するのに `token` の代わりに `code` を含めることもできます。 この id_token+code 応答は、ハイブリッド フローと呼ばれることもあります。  |
| `redirect_uri` | 推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| `scope` | required |[スコープ](v2-permissions-and-consent.md)のスペース区切りリスト。 OpenID Connect (id_tokens) では、スコープとして `openid` を指定する必要があります。このスコープは、承認 UI で "サインイン" アクセス許可に変換されます。 必要に応じて、その他のユーザー データにアクセスするために `email` および `profile` スコープを含めることも可能です。 アクセス トークンを要求する場合、さまざまなリソースに対する同意を求めるこの要求に、他のスコープが含まれていてもかまいません。 |
| `response_mode` | 省略可能 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。 既定値は、アクセス トークンだけのクエリ (ただし、要求に id_token が含まれている場合はフラグメント) です。 |
| `state` | 推奨 |要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](https://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| `nonce` | required |要求に含まれる、アプリによって生成される値。この値は、結果の id_token に要求として含まれます。 アプリはこの値を確認することにより、トークン リプレイ攻撃を緩和できます。 通常、この値はランダム化された一意の文字列であり、要求の送信元を特定する際に使用できます。 Id_token が要求された場合のみ必須です。 |
| `prompt` | 省略可能 |ユーザーとの必要な対話の種類を指定します。 現時点で有効な値は 'login'、'none'、'select_account'、'consent' だけです。 `prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。 `prompt=none` はその反対であり、ユーザーにどのような対話型プロンプトも表示されないようにします。 シングル サインオンで確認なしで要求を完了できない場合は、Microsoft ID プラットフォームからエラーが返されます。 `prompt=select_account` は、ユーザーを、セッションで記憶されているすべてのアカウントが表示されるアカウント ピッカーに送ります。 `prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| `login_hint`  |省略可能 |ユーザー名が事前にわかっている場合に、これを使用して、ユーザーに代わってサインイン ページのユーザー名/電子メール アドレス フィールドに事前入力できます。 アプリはしばしば前回のサインインから `preferred_username` 要求を抽出して再認証時にこのパラメーターを使用します。|
| `domain_hint` | 省略可能 |これが含まれていると、ユーザーがサインイン ページで実行する電子メール ベースの検出プロセスがスキップされ、多少効率化されたユーザー エクスペリエンスが提供されます。 このパラメーターは、1 つのテナントで動作する基幹業務アプリで一般的に使用され、アプリでは特定のテナント内のドメイン名が提供されます。これにより、ユーザーがそのテナントのフェデレーション プロバイダーに転送されます。  このヒントは、ゲストがこのアプリケーションにサインインできないようにし、FIDO などのクラウド資格情報の使用を制限することに注意してください。  |

現時点では、ユーザーに資格情報の入力と認証が求められます。 また、Microsoft ID プラットフォームでは、ユーザーが `scope` クエリ パラメーターに示されたアクセス許可に同意していることも確認されます。 ユーザーが同意したアクセス許可がこれらの中に **ない** 場合、必要なアクセス許可に同意するようユーザーに求めます。 詳細については、[アクセス許可、同意、およびマルチ テナント アプリ](v2-permissions-and-consent.md)に関するページを参照してください。

ユーザーが認証され、同意すると、Microsoft ID プラットフォームは `response_mode` パラメーターで指定されたメソッドを使用して、指定された `redirect_uri` でアプリに応答を返します。

#### <a name="successful-response"></a>成功応答

`response_mode=fragment` と `response_type=id_token+code` を使用した成功応答は、次のようになります (読みやすいように改行してあります)。

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| パラメーター | 説明 |
| --- | --- |
| `code` | `response_type` に `code` が含まれる場合に含まれます。 これは、[承認コード フロー](v2-oauth2-auth-code-flow.md)での使用に適した承認コードです。  |
| `access_token` |`response_type` に `token` が含まれる場合に含まれます。 アプリが要求したアクセス トークン。 アクセス トークンはデコードしないようにする必要があります。そうしないと、検証した場合に不透明な文字列として扱われます。 |
| `token_type` |`response_type` に `token` が含まれる場合に含まれます。 常に `Bearer` になります。 |
| `expires_in`|`response_type` に `token` が含まれる場合に含まれます。 キャッシュ用に有効なトークンの秒数を示します。 |
| `scope` |`response_type` に `token` が含まれる場合に含まれます。 access_token が有効なスコープを示します。 要求されたスコープをユーザーに適用できなかった場合 (ログインのために個人アカウントが使用されているときに Azure AD のみのスコープが要求された場合)、必ずしもすべてのスコープが含まれないことがあります。 |
| `id_token` | 署名付き JSON Web トークン (JWT)。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 id_token の詳細については、[`id_token reference`](id-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求され、`response_type` に `id_tokens` が含まれる場合のみ提供されます。 |
| `state` |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### <a name="error-response"></a>エラー応答

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| --- | --- |
| `error` |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="getting-access-tokens-silently-in-the-background"></a>バックグラウンドでサイレントにアクセス トークンを取得する

> [!Important]
> 暗黙のフローのこの部分は、[既定でサード パーティの Cookie が 削除される](reference-third-party-cookies-spas.md)ため、異なるブラウザーをまたいでアプリケーションを使用すると、そのアプリケーションでは機能しない可能性があります。  現在でも Incognito で使用されていない Chromium ベースのブラウザーでは機能しますが、開発者はフローのこの部分を使用することを再検討する必要があります。 サード パーティの Cookie をサポートしていないブラウザーでは、ログイン ページのセッション Cookie がブラウザーによって削除されるため、ユーザーがサインインしていないことを示すエラーが表示されます。 

これでユーザーをシングルページ アプリにサインインさせたので、[Microsoft Graph](https://developer.microsoft.com/graph) などの Microsoft ID プラットフォームによってセキュリティ保護された Web API を呼び出すためのアクセス トークンをサイレントに取得できます。 このメソッドを使用すると、`token` response_type を使用してトークンを既に取得している場合でも、再度サインインするためにユーザーをリダイレクトする必要なく、その他のリソースのトークンを取得できます。

通常の OpenID Connect/OAuth フローでは、これは Microsoft ID プラットフォームの `/token` エンドポイントに要求を発行することによって行います。 非表示の iframe でこの要求を実行し、他の Web API 用の新しいトークンを取得できます。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

URL のクエリ パラメーターの詳細については、「[サインイン要求を送信する](#send-the-sign-in-request)」を参照してください。

> [!TIP]
> 以下の要求をコピーしてブラウザー タブに貼り付けてみてください (`login_hint` の値をユーザーの正しい値に置き換えてください)。
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> これは、iframe 内で開くのではなく、ブラウザー バーに直接入力するため、サード パーティの Cookie がサポートされていないブラウザーでも機能します。 

`prompt=none` パラメーターに応じて、要求はすぐに成功または失敗し、アプリケーションに戻ります。 `response_mode` パラメーターで指定された方法を使用して、指定された `redirect_uri` でアプリに応答が送信されます。

#### <a name="successful-response"></a>成功応答

`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| パラメーター | 説明 |
| --- | --- |
| `access_token` |`response_type` に `token` が含まれる場合に含まれます。 この場合は、Microsoft Graph 用にアプリケーションが要求したアクセス トークンです。 アクセス トークンはデコードしないようにする必要があります。そうしないと、検証した場合に不透明な文字列として扱われます。 |
| `token_type` | 常に `Bearer` になります。 |
| `expires_in` | キャッシュ用に有効なトークンの秒数を示します。 |
| `scope` | access_token が有効なスコープを示します。 要求されたスコープをユーザーに適用できなかった場合 (ログインのために個人アカウントが使用されているときに Azure AD のみのスコープが要求された場合)、必ずしもすべてのスコープが含まれないことがあります。 |
| `id_token` | 署名付き JSON Web トークン (JWT)。 `response_type` に `id_token` が含まれる場合に含まれます。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 id_token の詳細については、[`id_token` のリファレンス](id-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求された場合のみ提供されます。 |
| `state` |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### <a name="error-response"></a>エラー応答

アプリ側で適切に処理できるように、 `redirect_uri` にエラーの応答が送信される場合もあります。 `prompt=none`の場合、予期されるエラーは次のようになります。

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| パラメーター | 説明 |
| --- | --- |
| `error` |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

Iframe 要求でこのエラーを受信した場合、ユーザーは対話形式でもう一度サインインして新しいトークンを取得する必要があります。 この場合は、アプリケーションに適した任意の方法で処理できます。

## <a name="refreshing-tokens"></a>トークンを更新する

暗黙的な付与では、更新トークンが与えられません。 `id_token` と `access_token` はどちらも短時間で期限切れになるため、トークンを定期的に更新するようにアプリを準備しておく必要があります。 どちらの種類のトークンを更新する場合も、ID プラットフォームの動作を制御するための `prompt=none` パラメーターを使用して、上と同じ非表示の iframe 要求を実行できます。 新しい `id_token` を受け取りたい場合は、`response_type` の `id_token` と `scope=openid` に加えて、`nonce` パラメーターも使用してください。

サード パーティの Cookie をサポートしていないブラウザーでは、ユーザーがサインインしていないことを示すエラーが発生します。 

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する

OpenID Connect `end_session_endpoint` を使用すると、ユーザーのセッションを終了させ、Microsoft ID プラットフォームによって設定された Cookie をクリアする要求がアプリから Microsoft ID プラットフォームへ送信されます。 ユーザーが Web アプリケーションから完全にサインアウトするには、アプリがユーザーとのセッションを終了し (通常、トークン キャッシュをクリアするか Cookie を切断する)、ブラウザーを以下にリダイレクトする必要があります。

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| パラメーター | Type | 説明 |
| --- | --- | --- |
| `tenant` |required |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| `post_logout_redirect_uri` | 推奨 | ログアウト完了後にユーザーが戻る URL。 この値は、アプリケーションに登録されているリダイレクト URI のいずれかと一致する必要があります。 含まれていない場合、Microsoft ID プラットフォームにより汎用メッセージが表示されます。 |

## <a name="next-steps"></a>次のステップ

* [MSAL JS のサンプル](sample-v2-code.md)を見直して、コーディング作業を開始します。
* 暗黙的な許可の代わりとなる、新しいより優れた手段として[承認コード フロー](v2-oauth2-auth-code-flow.md)を見直します。 
