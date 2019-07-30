---
title: Microsoft ID プラットフォームの暗黙的フローを使用してシングルページ アプリケーションをセキュリティで保護する | Azure
description: シングルページ アプリのための暗黙的フローの Microsoft ID プラットフォーム実装を使用した Web アプリケーションの構築。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e6ac72a91ae14b6f9c513c84da6f1f06508caef
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482219"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft ID プラットフォームと暗黙的な許可のフロー

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft ID プラットフォーム エンドポイントを使ったシングル ページ アプリでは、ユーザーは、Microsoft の個人アカウントと職場/学校アカウントのどちらでもサインインできます。 シングル ページ アプリなどの主にブラウザーで実行される JavaScript アプリには、認証に関して重要な課題があります。

* これらのアプリのセキュリティ特性は、従来のサーバーベースの Web アプリケーションとは大きく異なります。
* 多くの承認サーバーや ID プロバイダーでは、CORS 要求をサポートしていません。
* アプリからブラウザーにフル ページがリダイレクトされると、ユーザー エクスペリエンスに大きな悪影響が及びます。

このようなアプリケーション (AngularJS、Ember.js、React.js など) 向けに、Microsoft ID プラットフォームでは OAuth 2.0 の暗黙的な許可のフローをサポートしています。 この暗黙的フローは、[OAuth 2.0 仕様](https://tools.ietf.org/html/rfc6749#section-4.2)で規定されています。 主な利点は、バックエンド サーバーとの資格情報交換を実行しなくても、アプリが Microsoft ID プラットフォームからトークンを取得できることです。 これにより、アプリは、ユーザーのサインイン、セッションの維持、他の Web API へのトークンの取得をすべてクライアント JavaScript コード内で実行できます。 暗黙的フローを使用する際に考慮が必要なセキュリティに関する重要事項がいくつかあります。具体的には、[クライアント](https://tools.ietf.org/html/rfc6749#section-10.3)と[ユーザーの偽装](https://tools.ietf.org/html/rfc6749#section-10.3)に関する事項です。

暗黙的フローと Microsoft ID プラットフォームを使用して JavaScript アプリに認証を追加する場合は、オープン ソースの JavaScript ライブラリ [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) を使用することをお勧めします。

シングル ページ アプリケーションでライブラリを使用せずに、自分でプロトコル メッセージを送信する場合は、次の一般的な手順を実行してください。

> [!NOTE]
> Microsoft ID プラットフォーム エンドポイントでは、Azure Active Directory (Azure AD) のすべてのシナリオや機能がサポートされているわけではありません。 Microsoft ID プラットフォーム エンドポイントを使用すべきかどうかを判定するには、[Microsoft ID プラットフォームの制限](active-directory-v2-limitations.md)に関するページを参照してください。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

次の図は、暗黙的なサインイン フローの全体像を示しています。各手順については、この後のセクションで詳しく説明します。

![暗黙的なサインイン フローを示す図](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>サインイン要求を送信する

最初にユーザーをアプリにサインインするために、[OpenID Connect](v2-protocols-oidc.md) 認証要求を送信し、Microsoft ID プラットフォーム エンドポイントから `id_token` を取得します。

> [!IMPORTANT]
> ID トークンを正しく要求するには、[Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページで、 **[暗黙の付与]** セクションの **[アクセス トークン]** と **[ID トークン]** を選択して、アプリ登録の暗黙的な許可フローを適切に有効にする必要があります。 それが有効でない場合は、`unsupported_response` エラー **The provided value for the input parameter 'response_type' is not allowed for this client.Expected value is 'code' (入力パラメーター 'response_type' に入力された値はこのクライアントで許可されません。入力できる値は 'code' です。)** が返されます

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

| パラメーター |  | 説明 |
| --- | --- | --- |
| `tenant` | 必須 |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページでアプリに割り当てられたアプリケーション (クライアント) ID。 |
| `response_type` | 必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。 response_type `token` が含まれる場合もあります。 ここで `token` を使用すると、アプリでは承認エンドポイントへ 2 度目の要求を行うことなく、承認エンドポイントからアクセス トークンをすぐに受け取ることができます。 `token` response_type を使用する場合、`scope` パラメーターには、トークンを発行するリソースを示すスコープを含める必要があります。 |
| `redirect_uri` | 推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| `scope` | 必須 |[スコープ](v2-permissions-and-consent.md)のスペース区切りリスト。 OpenID Connect では、スコープとして `openid` を指定する必要があります。このスコープは、承認 UI で "サインイン" アクセス許可に変換されます。 必要に応じて、その他のユーザー データにアクセスするために `email` または `profile` スコープを含めることも可能です。 さまざまなリソースに同意を求めるこの要求には、他のスコープが含まれていてもかまいません。 |
| `response_mode` | 省略可能 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。 既定値は、アクセス トークンのクエリ (ただし、要求に id_token が含まれている場合はフラグメント) です。 |
| `state` | 推奨 |要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](https://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| `nonce` | 必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な id_token に要求として追加されます。 アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 Id_token が要求された場合のみ必須です。 |
| `prompt` | 省略可能 |ユーザーとの必要な対話の種類を指定します。 現時点で有効な値は 'login'、'none'、'select_account'、'consent' だけです。 `prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。 `prompt=none` はその反対であり、ユーザーにどのような対話型プロンプトも表示されないようにします。 シングル サインオンで確認なしで要求を完了できない場合は、Microsoft ID プラットフォーム エンドポイントからエラーが返されます。 `prompt=select_account` は、ユーザーを、セッションで記憶されているすべてのアカウントが表示されるアカウント ピッカーに送ります。 `prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| `login_hint`  |省略可能 |ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。 アプリはしばしば前回のサインインから `preferred_username` 要求を抽出して再認証時にこのパラメーターを使用します。|
| `domain_hint` | 省略可能 |`consumers` か `organizations` のいずれかを指定できます。 これが含まれていると、ユーザーがサインイン ページで実行する電子メール ベースの検出プロセスがスキップされ、多少効率化されたユーザー エクスペリエンスが提供されます。 アプリはしばしば id_token から `tid` 要求を抽出して再認証時にこのパラメーターを使用します。 要求値 `tid` が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合 (Microsoft Account コンシューマー テナント)、`domain_hint=consumers` を使用してください。 それ以外の場合は、再認証時に `domain_hint=organizations` を使用できます。 |

現時点では、ユーザーに資格情報の入力と認証が求められます。 Microsoft ID プラットフォーム エンドポイントではまた、ユーザーが `scope` クエリ パラメーターに示されたアクセス許可に同意していることも確認されます。 ユーザーが同意したアクセス許可がこれらの中に**ない**場合、必要なアクセス許可に同意するようユーザーに求めます。 詳細については、[アクセス許可、同意、およびマルチ テナント アプリ](v2-permissions-and-consent.md)に関するページを参照してください。

ユーザー本人であることを証明書し、同意の許可を与えると、Microsoft ID プラットフォーム エンドポイントは `response_mode` パラメーターで指定された方法を使用して、指定された `redirect_uri` でアプリに応答を返します。

#### <a name="successful-response"></a>成功応答

`response_mode=fragment` と `response_type=id_token+token` を使用した成功応答は、次のようになります (読みやすいように改行してあります)。

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| パラメーター | 説明 |
| --- | --- |
| `access_token` |`response_type` に `token` が含まれる場合に含まれます。 この場合は、Microsoft Graph 用にアプリケーションが要求したアクセス トークンです。 アクセス トークンはデコードしないようにする必要があります。そうしないと、検証した場合に不透明な文字列として扱われます。 |
| `token_type` |`response_type` に `token` が含まれる場合に含まれます。 常に `Bearer` になります。 |
| `expires_in`|`response_type` に `token` が含まれる場合に含まれます。 キャッシュ用に有効なトークンの秒数を示します。 |
| `scope` |`response_type` に `token` が含まれる場合に含まれます。 access_token が有効なスコープを示します。 要求されたスコープをユーザーに適用できなかった場合 (ログインのために個人アカウントが使用されているときに Azure AD のみのスコープが要求された場合)、必ずしもすべてのスコープが含まれないことがあります。 |
| `id_token` | 署名付き JSON Web トークン (JWT)。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 id_token の詳細については、[`id_token reference`](id-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求された場合のみ提供されます。 |
| `state` |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### <a name="error-response"></a>エラー応答

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| --- | --- |
| `error` |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="validate-the-idtoken"></a>id_token を検証する

id_token を受信するだけでは、ユーザーを認証するために十分ではありません。id_token の署名を検証し、トークン内の要求をアプリの要件に基づいて確認することも必要です。 Microsoft ID プラットフォーム エンドポイントは、[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号化を使用してトークンに署名し、それらが有効であることを確認します。

クライアント コードで `id_token` を検証することもできますが、`id_token` をバックエンド サーバーに送信して検証を実行するのが一般的な方法です。 id_token の署名を検証した後、確認する必要のある要求がいくつか存在します。 [トークンの検証](id-tokens.md#validating-an-id_token)と[署名キーのロールオーバーに関する重要な情報](active-directory-signing-key-rollover.md)などの詳細については、[`id_token` のリファレンス](id-tokens.md)を参照してください。 トークンの解析および検証には、ほとんどの言語とプラットフォームに少なくとも 1 つは用意されているライブラリを活用することをお勧めします。

シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

* ユーザー/組織がアプリにサインアップ済みであることを確認する。
* 適切な承認/特権がユーザーにあることを確認する。
* 多要素認証など特定の強度の認証が行われたことを確認する。

id_token を検証したら、ユーザーとのセッションを開始し、id_token 内の要求を使用してアプリでそのユーザーに関する情報を取得することができます。 この情報は、表示、記録、パーソナル化などに使用できます。

## <a name="get-access-tokens"></a>アクセス トークンを取得する

これでユーザーをシングルページ アプリにサインインさせたので、[Microsoft Graph](https://developer.microsoft.com/graph) などの Microsoft ID プラットフォームによってセキュリティ保護された Web API を呼び出すためのアクセス トークンを取得できます。 このメソッドを使用すると、`token` response_type を使用してトークンを既に取得している場合でも、再度サインインするためにユーザーをリダイレクトする必要なく、その他のリソースのトークンを取得できます。

通常の OpenID Connect/OAuth フローでは、これは Microsoft ID プラットフォームの `/token` エンドポイントに要求を発行することによって行います。 ただし、Microsoft ID プラットフォーム エンドポイントは CORS 要求をサポートしていないため、AJAX 呼び出しによってトークンを取得または更新することは不可能です。 代わりに、非表示の iframe で暗黙的フローを使用して、他の Web API 用の新しいトークンを取得できます。 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

URL のクエリ パラメーターの詳細については、「[サインイン要求を送信する](#send-the-sign-in-request)」を参照してください。

> [!TIP]
> 以下の要求をコピーしてブラウザー タブに貼り付けてみてください (`login_hint` の値をユーザーの正しい値に置き換えてください)。
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

`prompt=none` パラメーターに応じて、要求はすぐに成功または失敗し、アプリケーションに戻ります。 成功すると、`response_mode` パラメーターで指定された方法を使用して、指定された `redirect_uri` でアプリに応答が送信されます。

#### <a name="successful-response"></a>成功応答

`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
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

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| パラメーター | 説明 |
| --- | --- |
| `error` |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

Iframe 要求でこのエラーを受信した場合、ユーザーは対話形式でもう一度サインインして新しいトークンを取得する必要があります。 この場合は、アプリケーションに適した任意の方法で処理できます。

## <a name="validating-access-tokens"></a>アクセス トークンの検証

access_token を受け取ったら、トークンの署名と以下の要求を確認してください。 シナリオに応じてその他の要求も検証することができます。

* **受信者**要求。トークンがそのアプリに与えられるものであることを確認します。
* **発行者**要求。トークンが Microsoft ID プラットフォーム エンドポイントによってそのアプリに発行されたことを確認します。
* **期間の開始時刻**要求と**期限切れ日時**要求。トークンが期限切れでないことを検証します。

アクセス トークンに含まれている要求の詳細については、[アクセス トークン リファレンス](access-tokens.md)を参照してください

## <a name="refreshing-tokens"></a>トークンを更新する

暗黙的な付与では、更新トークンが与えられません。 `id_token` と `access_token` はどちらも短時間で期限切れになるため、トークンを定期的に更新するようにアプリを準備しておく必要があります。 どちらの種類のトークンを更新する場合も、ID プラットフォームの動作を制御するための `prompt=none` パラメーターを使用して、上と同じ非表示の iframe 要求を実行できます。 新しい `id_token` を取得する場合は、`nonce` に加えて、必ず `response_type=id_token` と `scope=openid` を使用してください。

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する

OpenID Connect `end_session_endpoint` を使用すると、アプリから Microsoft ID プラットフォーム エンドポイントへの要求をエンド ユーザーのセッションに送信し、Microsoft ID プラットフォーム エンドポイントによって設定された Cookie をクリアできます。 ユーザーが Web アプリケーションから完全にサインアウトするには、アプリがユーザーとのセッションを終了し (通常、トークン キャッシュをクリアするか Cookie を切断する)、ブラウザーを以下にリダイレクトする必要があります。

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| パラメーター |  | 説明 |
| --- | --- | --- |
| `tenant` |必須 |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| `post_logout_redirect_uri` | 推奨 | ログアウト完了後にユーザーが戻る URL。 この値は、アプリケーションに登録されているリダイレクト URI のいずれかと一致する必要があります。 一致しない場合、Microsoft ID プラットフォーム エンドポイントにより汎用メッセージが表示されます。 |

## <a name="next-steps"></a>次の手順

* [MSAL JS のサンプル](sample-v2-code.md)を見直して、コーディング作業を開始します。
