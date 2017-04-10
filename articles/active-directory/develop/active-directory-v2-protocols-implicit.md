---
title: "Azure AD v2.0 の暗黙的フローを使用して、シングル ページ アプリケーションをセキュリティで保護する | Microsoft Docs"
description: "Azure AD の v2.0 で導入されたシングル ページ アプリ向け暗黙的フローを使用して、Web アプリケーションを構築します。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: ba958d029e5bf1bc914a2dff4b6c09282d578c67
ms.openlocfilehash: 1ea1f54832a13b57caf3d6783e482fad4ba00781


---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 プロトコル: 暗黙的なフローを使用する SPA
v2.0 エンドポイントを使ったシングル ページ アプリでは、ユーザーは、Microsoft の個人アカウントと職場/学校アカウントのどちらでもサインインできます。  シングル ページ アプリなどの主にブラウザーで実行される JavaScript アプリには、認証に関して重要な課題があります。

* これらのアプリのセキュリティ特性は、従来のサーバー ベースの Web アプリケーションとは大きく異なります。
* 多くの承認サーバーや ID プロバイダーでは、CORS 要求をサポートしていません。
* アプリからブラウザーにフル ページがリダイレクトされると、ユーザー エクスペリエンスに大きな悪影響が及びます。

このようなアプリケーション (AngularJS、Ember.js、React.js など) 向けに、Azure AD では OAuth 2.0 Implicit Grant フローをサポートしています。  この暗黙的フローは、[OAuth 2.0 仕様](http://tools.ietf.org/html/rfc6749#section-4.2)で規定されています。  主な利点は、バックエンド サーバーと資格情報をやり取りしなくても、アプリが Azure AD からトークンを取得できることです。  これにより、アプリは、ユーザーのサインイン、セッションの維持、他の Web API へのトークンの取得をすべてクライアント JavaScript コード内で実行できます。  暗黙的フローを使用する際に考慮が必要なセキュリティに関する重要事項がいくつかあります。具体的には、[クライアント](http://tools.ietf.org/html/rfc6749#section-10.3)と[ユーザーの偽装](http://tools.ietf.org/html/rfc6749#section-10.3)に関する事項です。

暗黙的フローと Azure AD を使用して JavaScript アプリに認証を追加する場合は、オープン ソースの JavaScript ライブラリである [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js)を使用することをお勧めします。  作業の開始に役立つ AngularJS チュートリアルを [こちら](active-directory-appmodel-v2-overview.md#getting-started) で入手できます。  

シングル ページ アプリケーションでライブラリを使用せずに、自分でプロトコル メッセージを送信する場合は、次の一般的な手順を実行してください。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。  v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
> 
> 

## <a name="protocol-diagram"></a>プロトコルのダイアグラム
暗黙的なサインイン フローの全体像は次のようになります。各手順についてはこの後詳しく説明します。

![OpenId Connect Swimlanes](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>サインイン要求を送信する
最初にユーザーをアプリにサインインするために、v2.0 エンドポイントから [OpenID Connect](active-directory-v2-protocols-oidc.md) 承認要求を送信し、`id_token` を取得します。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> この要求を実行するには、以下のリンクをクリックしてください。 サインイン後、ブラウザーは `https://localhost/myapp/` にリダイレクトされ、アドレス バーに `id_token` が含まれた状態になります。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| パラメーター |  | Description |
| --- | --- | --- |
| テナント |必須 |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。  使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。  詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| client_id |必須 |登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) によってアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。  response_type `token` が含まれる場合もあります。 ここで `token` を使用すると、アプリでは承認エンドポイントへ&2; 度目の要求を行うことなく、承認エンドポイントからアクセス トークンをすぐに受け取ることができます。  `token` response_type を使用する場合、`scope` パラメーターには、トークンを発行するリソースを示すスコープを含める必要があります。 |
| redirect_uri |推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。  ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。  OpenID Connect では、スコープとして `openid` を指定する必要があります。このスコープは、承認 UI で "サインイン" アクセス許可に変換されます。  必要に応じて、その他のユーザー データにアクセスするために `email` または `profile` [スコープ](active-directory-v2-scopes.md)を含めることも可能です。  さまざまなリソースに同意を求めるこの要求には、他のスコープが含まれていてもかまいません。 |
| response_mode |推奨 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。  暗黙的フローでは `fragment` を指定する必要があります。 |
| state |推奨 |要求に含まれ、かつトークンの応答として返される値。  任意の文字列を指定することができます。  [クロスサイト リクエスト フォージェリ攻撃を防ぐ](http://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。  この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な id_token に要求として追加されます。  アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。  通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類を指定します。  現時点で有効な値は 'login'、'none'、'consent' だけです。  `prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。  `prompt=none` は反対に、ユーザーに対して対話形式のプロンプトを表示しません。  シングル サインオンによって自動的に要求を完了できない場合、v2.0 エンドポイントはエラーを返します。  `prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| login_hint |省略可能 |ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。  アプリはしばしば前回のサインインから `preferred_username` 要求を抽出して再認証時にこのパラメーターを使用します。 |
| domain_hint |省略可能 |`consumers` か `organizations` のいずれかを指定できます。  これが含まれる場合、v2.0 のサインイン ページでユーザーが行う電子メール ベースの検出プロセスがスキップされ、ユーザー エクスペリエンスは若干簡素化されたものになります。  アプリはしばしば id_token から `tid` 要求を抽出して再認証時にこのパラメーターを使用します。  `tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用する必要があります。  それ以外の場合は、 `domain_hint=organizations`を指定します。 |

現時点では、ユーザーに資格情報の入力と認証が求められます。  v2.0 エンドポイントは、 `scope` クエリ パラメーターで指定されたアクセス許可にユーザーが同意済みであることの保証も行います。  いずれのアクセス許可にもユーザーが同意しなかった場合、必要なアクセス許可に同意するようユーザーに求めます。  アクセス許可、同意、マルチテナント アプリの詳細については、 [こちら](active-directory-v2-scopes.md)を参照してください。

ユーザーが本人であることを証明し、同意の許可を与えると、v2.0 エンドポイントは、`response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。

#### <a name="successful-response"></a>成功応答
`response_mode=fragment` と `response_type=id_token+token` を使用した成功応答は、次のようになります (読みやすいように改行してあります)。

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| パラメーター | Description |
| --- | --- |
| access_token |`response_type` に `token` が含まれる場合に含まれます。 この場合は、Microsoft Graph 用にアプリケーションが要求したアクセス トークンです。  アクセス トークンはデコードしないようにする必要があります。検証した場合、不明瞭な文字列として扱われることがあります。 |
| token_type |`response_type` に `token` が含まれる場合に含まれます。  常に `Bearer`になります。 |
| expires_in |`response_type` に `token` が含まれる場合に含まれます。  キャッシュ用に有効なトークンの秒数を示します。 |
| scope |`response_type` に `token` が含まれる場合に含まれます。  access_token が有効なスコープを示します。 |
| id_token |アプリが要求した id_token。 この id_token を使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。  id_token とその内容の詳細については、[v2.0 エンドポイント トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |
| state |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### <a name="error-response"></a>エラー応答
アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーターが含まれる必要があります。 | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="validate-the-idtoken"></a>id_token を検証する
単に id_token を受け取るだけでは、ユーザーを認証するには不十分です。id_token の署名を検証し、そのトークンに含まれる要求をアプリの要件に従って確認する必要があります。  v2.0 エンドポイントは、[JSON Web トークン (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

クライアント コードで `id_token` を検証することもできますが、`id_token` をバックエンド サーバーに送信して検証を実行するのが一般的な方法です。  id_token の署名を検証した後に、確認の必要な要求がいくつか存在します。  [トークンの検証](active-directory-v2-tokens.md#validating-tokens)と[署名キーのロールオーバーに関する重要な情報](active-directory-v2-tokens.md#validating-tokens)などの詳細については、「[v2.0 トークンのリファレンス](active-directory-v2-tokens.md)」を参照してください。  トークンの解析および検証には、ほとんどの言語とプラットフォームに少なくとも&1; つは用意されているライブラリを活用することをお勧めします。
<!--TODO: Improve the information on this-->

シナリオに応じてその他の要求も検証することができます。  以下に一般的な検証の例をいくつか挙げます。

* ユーザー/組織がアプリにサインアップ済みであることを確認する。
* 適切な承認/特権がユーザーにあることを確認する。
* 多要素認証など特定の強度の認証が行われたことを確認する。

id_token に含まれる要求の詳細については、[v2.0 エンドポイント トークン リファレンス](active-directory-v2-tokens.md)を参照してください。

id_token を十分検証したら、ユーザーとのセッションを開始し、id_token に含まれる要求を使ってそのユーザーに関する情報をアプリの中で取得することができます。  取得した情報は、表示、記録、承認などに利用することができます。

## <a name="get-access-tokens"></a>アクセス トークンを取得する
ユーザーをシングル ページ アプリにサインインしたら、 [Microsoft Graph](https://graph.microsoft.io)など、Azure AD によってセキュリティ保護された Web API を呼び出すためのアクセス トークンを取得できます。  このメソッドを使用すると、`token` response_type を使用してトークンを既に取得している場合でも、再度サインインするためにユーザーをリダイレクトする必要なく、その他のリソースのトークンを取得できます。

通常の OpenID Connect/OAuth フローでは、この操作として、v2.0 `/token` エンドポイントへの要求を作成します。  ただし、v2.0 エンドポイントでは CORS 要求をサポートしていないため、AJAX 呼び出しでトークンの取得や更新を実行することはできません。  代わりに、非表示の iframe で暗黙的フローを使用して、他の Web API 用の新しいトークンを取得できます。 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [!TIP]
> 以下の要求をコピーしてブラウザー タブに貼り付けてみてください (`domain_hint` と `login_hint` の値をユーザーの正しい値に置き換えてください)。
> 
> 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| パラメーター |  | Description |
| --- | --- | --- |
| テナント |必須 |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。  使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。  詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| client_id |必須 |登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) によってアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。  `code` などの他の response_types が含まれていてもかまいません。 |
| redirect_uri |推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。  ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。  トークンを取得するには、リソースに必要な [スコープ](active-directory-v2-scopes.md) をすべて含めてください。 |
| response_mode |推奨 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。  `query`、`form_post`、`fragment` のいずれかを指定できます。 |
| state |推奨 |要求に含まれ、かつトークンの応答として返される値。  任意の文字列を指定することができます。  クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。  この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な id_token に要求として追加されます。  アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。  通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| prompt |必須 |非表示の iframe でトークンを更新および取得するには、iframe が v2.0 のサインイン ページでハングせずにすぐに応答できるよう `prompt=none` を使用する必要があります。 |
| login_hint |必須 |非表示の iframe のトークンを更新および取得するには、ある時点でのユーザーの複数のセッションを区別できるよう、このヒントにユーザーのユーザー名を含める必要があります。 `preferred_username` 要求を使用すると、前回のサインインからユーザー名を抽出できます。 |
| domain_hint |必須 |`consumers` か `organizations` のいずれかを指定できます。  非表示の iframe のトークンを更新および取得するには、要求に domain_hint を含める必要があります。  使用する値を決定するには、前回サインイン時の id_token から `tid` 要求を抽出します。  `tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用する必要があります。  それ以外の場合は、 `domain_hint=organizations`を指定します。 |

`prompt=none` パラメーターに応じて、要求はすぐに成功または失敗し、アプリケーションに戻ります。  成功すると、`response_mode` パラメーターで指定された方法を使用して、指定された `redirect_uri` でアプリに応答が送信されます。

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
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

Iframe 要求でこのエラーを受信した場合、ユーザーは対話形式でもう一度サインインして新しいトークンを取得する必要があります。  この場合は、アプリケーションに適した任意の方法で処理できます。

## <a name="refreshing-tokens"></a>トークンを更新する
`id_token` と `access_token` はどちらも短時間で期限切れになるため、トークンを定期的に更新するようにアプリを準備しておく必要があります。  どちらの種類のトークンを更新する場合も、Azure AD の動作を制御する `prompt=none` パラメーターを使用して、上記と同じ非表示の iframe 要求を実行できます。  新しい `id_token` を取得する場合は、`nonce` に加えて、必ず `response_type=id_token` と `scope=openid` を使用してください。

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する
OpenIdConnect `end_session_endpoint` は現在、v2.0 エンドポイントではサポートされていません。 したがって、v2.0 エンドポイントに対しアプリから、ユーザーのセッションを終了し、v2.0 エンドポイントによって設定された Cookie をクリアする要求を送信することはできません。
ユーザーをサインアウトさせるには、単にアプリ側でユーザーとのセッションを終了し、ユーザー側の v2.0 エンドポイントとのセッションは放置してください。  次回ユーザーがサインインしようとすると "アカウントの選択" ページが表示され、実際にサインインしているアカウントが一覧表示されます。
そのページでユーザーは任意のアカウントを選んでサインアウトし、v2.0 エンドポイントとのセッションを終了することができます。

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->



<!--HONumber=Jan17_HO4-->


