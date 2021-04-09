---
title: OpenID Connect と Azure AD を使用して Web アプリのアクセスを承認する | Microsoft Docs
description: この記事では、Azure Active Directory と OpenID Connect を利用してテナントの Web アプリケーションと Web API へのアクセスを承認するために HTTP メッセージを使用する方法について説明します。
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: b719e866852d2e865c16c62fddd8c549ae505b7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85551562"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>OpenID Connect と Azure Active Directory を使用する Web アプリケーションへのアクセスの承認

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 プロトコル上に構築された単純な ID 層です。 OAuth 2.0 では保護されたリソースにアクセスするための [**アクセス トークン**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)を取得して使用するためのメカニズムを定義しますが、ID 情報を提供するための標準的な方法は定義しません。 OpenID Connect は、OAuth 2.0 承認プロセスの拡張機能として認証を実装します。 エンド ユーザーに関する情報を [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) の形式で提供し、これを使ってユーザーの ID を検証し、ユーザーに関する基本的なプロファイル情報を提供します。

OpenID Connect は、サーバーでホストされ、ブラウザーでアクセスされる Web アプリケーションを構築している場合に推奨されます。

## <a name="register-your-application-with-your-ad-tenant"></a>AD テナントへのアプリケーションの登録
まず、Azure Active Directory (Azure AD) テナントにアプリケーションを登録する必要があります。 これにより、アプリケーションのアプリケーション ID を取得でき、トークンを受信できるようになります。

1. [Azure portal](https://portal.azure.com) にサインインします。
   
1. ページ右上隅にあるアカウントを選択し、 **[ディレクトリの切り替え]** ナビゲーションを選択してから、適切なテナントを選択して、Azure AD テナントを選択します。 
   - アカウントの下の Azure AD テナントが 1 つのみの場合、または適切な Azure AD テナントを既に選択している場合は、この手順をスキップします。
   
1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。
   
1. **[Azure Active Directory]** の左側のメニューで、 **[アプリの登録]** を選択し、 **[新しい登録]** を選択します。
   
1. 画面の指示に従い、新しいアプリケーションを作成します。 このチュートリアルでは、Web アプリケーションであるかパブリック クライアント (モバイルとデスクトップ) アプリケーションであるかは重要ではありませんが、Web アプリケーションまたはパブリック クライアント アプリケーションの具体的な例を確認するには、[クイック スタート](v1-overview.md)をご覧ください。
   
   - **[名前]** はアプリケーションの名前で、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
   - **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
   - **[リダイレクト URI]** を指定します。 Web アプリケーションの場合、これはユーザーのサインイン場所となるアプリのベース URL です。  たとえば、「 `http://localhost:12345` 」のように入力します。 パブリック クライアント (モバイルとデスクトップ) の場合、Azure AD はこれを使用してトークン応答を返します。 アプリケーション固有の値を入力します。  たとえば、「 `http://MyFirstAADApp` 」のように入力します。
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. 登録が完了すると、Azure AD により、アプリケーションに一意のクライアント ID (**アプリケーション ID**) が割り当てられます。 この値は次のセクションで必要になるので、アプリケーション ページからコピーします。
   
1. Azure portal でアプリケーションを見つけるには、 **[アプリの登録]** を選択し、 **[アプリケーションをすべて表示]** を選択します。

## <a name="authentication-flow-using-openid-connect"></a>OpenID Connect を使用する認証フロー

最も基本的なサインイン フローには次の手順が含まれています。各手順についてはこの後詳しく説明します。

![OpenID Connect 認証フロー](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect のメタデータ ドキュメント

OpenID Connect はメタデータ ドキュメントについて説明するものです。メタデータ ドキュメントは、アプリがサインインを実行するために必要な情報の大半を含んでいます。 これには、使用する URL、サービスの公開署名キーの場所などの情報が含まれます。 OpenID Connect のメタデータ ドキュメントは、次の場所にあります。

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
メタデータは、単純な JavaScript Object Notation (JSON) ドキュメントです。 例については、次のスニペットを参照してください。 スニペットの内容については、[OpenID Connect の仕様](https://openid.net)に詳しく記載されています。 上記の {tenant} に `common` ではなくテナント ID を指定すると、JSON オブジェクトのテナント固有の URI が返されます。

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

[claims-mapping](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 機能を使用した結果として、アプリにカスタム署名キーがある場合は、アプリの署名キー情報をポイントする `jwks_uri` を取得するために、アプリ ID を含む `appid` クエリ パラメーターを追加する必要があります。 例: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` には、`https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` の `jwks_uri` が含まれます。

## <a name="send-the-sign-in-request"></a>サインイン要求を送信する

Web アプリケーションでユーザーを認証する必要がある場合、ユーザーを `/authorize` エンドポイントにリダイレクトさせる必要があります。 この要求は、 [OAuth 2.0 承認コード フロー](v1-protocols-oauth-code.md)の最初の部分に似ていますが、いくつかの重要な違いがあります。

* 要求の `scope` パラメーターにはスコープ `openid` が含まれる必要があります。
* `response_type` パラメーターには、`id_token` が設定されている必要があります。
* 要求には `nonce` パラメーターが含まれる必要があります。

したがって、要求の例は次のようになります。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| パラメーター | Type | 説明 |
| --- | --- | --- |
| tenant |required |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値はテナント ID です。たとえば、`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`、`contoso.onmicrosoft.com` または `common` (テナント独立のトークンの場合) です |
| client_id |required |Azure AD への登録時にアプリに割り当てられたアプリケーション ID。 これは、Azure Portal で確認できます。 **[Azure Active Directory]** 、 **[アプリの登録]** の順にクリックし、アプリケーションを選択して、アプリケーション ページでアプリケーション ID を特定します。 |
| response_type |required |OpenID Connect サインインでは、 `id_token` を指定する必要があります。 `code` や `token` などの他の response_types が含まれていてもかまいません。 |
| scope | 推奨 | OpenID Connect の仕様では、スコープとして `openid` を指定する必要があります。このスコープは、承認 UI で "サインイン" アクセス許可に変換されます。 これとその他の OIDC スコープは v1.0 エンドポイントでは無視されますが、標準に準拠したクライアントにとっては依然としてベスト プラクティスです。 |
| nonce |required |アプリによって生成された、要求に含まれる値。この値が、最終的な `id_token` に要求として含まれます。 アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 通常、この値はランダム化された一意の文字列または GUID になっており、要求の送信元を特定する際に使用できます。 |
| redirect_uri | 推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 ない場合は、ユーザー エージェントが、アプリ用に登録されたリダイレクト URI のいずれかにランダムに送り返されます。 最大長は 255 バイトです。 |
| response_mode |省略可能 |結果として得られた authorization_code をアプリに返す際に使用するメソッドを指定します。 サポートされる値は、`form_post` (*HTTP フォーム ポスト* の場合) および `fragment` (*URL フラグメント* の場合) です。 Web アプリケーションでは、トークンをアプリケーションに最も安全に転送できるように、`response_mode=form_post` を使用することをお勧めします。 id_token を含むすべてのフローの既定値は `fragment` です。|
| state |推奨 |要求に含まれ、トークンの応答として返される値。 任意の文字列を指定することができます。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](https://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類を指定します。 現時点で有効な値は 'login'、'none'、'consent' だけです。 `prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。 `prompt=none` は反対に、ユーザーに対して対話形式のプロンプトを表示しません。 シングル サインオンによって自動的に要求を完了できない場合、エンドポイントはエラーを返します。 `prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| login_hint |省略可能 |ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。 多くのアプリでは、`preferred_username` 要求を使用して以前のサインインからユーザー名を抽出しておき、再認証時にこのパラメーターを使用します。 |

この時点で、ユーザーに資格情報の入力と認証が求められます。

### <a name="sample-response"></a>応答のサンプル

ユーザーが認証された後でサインイン要求内に指定された `redirect_uri` に送信される応答の例は、次のようになります。

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| パラメーター | 説明 |
| --- | --- |
| id_token |アプリが要求した `id_token` 。 この `id_token` を使用してユーザーの身元を確認し、そのユーザーとのセッションを開始することができます。 |
| state |要求に含まれ、トークンの応答としても返される値。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](https://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |

### <a name="error-response"></a>エラー応答

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>承認エンドポイント エラーのエラー コード

次の表で、エラー応答の `error` パラメーターで返される可能性のあるさまざまなエラー コードを説明します。

| エラー コード | 説明 | クライアント側の処理 |
| --- | --- | --- |
| invalid_request |必要なパラメーターが不足しているなどのプロトコル エラーです。 |要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。 |
| unauthorized_client |クライアント アプリケーションは、認証コードの要求を許可されていません。 |これは通常、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| access_denied |リソースの所有者が同意を拒否しました。 |クライアント アプリケーションは、ユーザーが同意しないと続行できないことを、ユーザーに通知できます。 |
| unsupported_response_type |承認サーバーは要求に含まれる応答の種類をサポートしていません。 |要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。 |
| server_error |サーバーで予期しないエラーが発生しました。 |要求をやり直してください。 これらのエラーは一時的な状況によって発生します。 クライアント アプリケーションは、一時的なエラーのため応答が遅れることをユーザーに説明する場合があります。 |
| temporarily_unavailable |サーバーが一時的にビジー状態であるため、要求を処理できません。 |要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |
| invalid_resource |対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 |これは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |

## <a name="validate-the-id_token"></a>id_token を検証する

単に `id_token` を受け取るだけでは、ユーザーを認証するには不十分です。`id_token` の署名を検証し、要求をアプリの要件に従って確認する必要があります。 Azure AD エンドポイントは、JSON Web トークン (JWT) と公開キー暗号を使用してトークンに署名し、それらが有効であることを確認します。

クライアント コードで `id_token` を検証することもできますが、`id_token` をバックエンド サーバーに送信して検証を実行するのが一般的な方法です。 

シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

* ユーザー/組織がアプリにサインアップ済みであることを確認する。
* `wids` または `roles` 要求を使用して、適切な承認/特権がユーザーにあることを確認する。 
* 多要素認証など特定の強度の認証が行われたことを確認する。

`id_token` を検証したら、ユーザーとのセッションを開始し、`id_token` の要求を使用してそのユーザーに関する情報をアプリで取得することができます。 この情報は、表示、記録、パーソナル化などに利用することができます。`id_tokens` と要求の詳細については、[AAD id_tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) に関するページを参照してください。

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する

ユーザーをアプリからサインアウトさせるとき、アプリの Cookie を消去する、あるいはユーザーとのセッションを終了するだけでは十分ではありません。 サインアウトするには、ユーザーを `end_session_endpoint` にリダイレクトする必要もあります。そうしないと、ユーザーが資格情報を再入力しなくてもアプリで再認証できます。Azure AD エンドポイントのシングル サインオン セッションが有効であるためです。

OpenID Connect メタデータ ドキュメントの一覧にある `end_session_endpoint` にはユーザーを簡単にリダイレクトできます。

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| パラメーター | Type | 説明 |
| --- | --- | --- |
| post_logout_redirect_uri |推奨 |サインアウトの正常終了後にユーザーをリダイレクトする URL。この URL は、アプリ登録ポータルのアプリケーションに対する登録済みリダイレクト URI のいずれかと一致させる必要があります。  *post_logout_redirect_uri* が含まれていない場合、ユーザーに汎用メッセージが表示されます。 |

## <a name="single-sign-out"></a>シングル サインアウト

ユーザーが `end_session_endpoint` にリダイレクトされると、Azure AD は、ユーザーのセッションをブラウザーからクリアします。 ただし、ユーザーは認証に Azure AD を使用する他のアプリケーションにサインインしたままになることがあります。 ユーザーがアプリケーションから同時にサインアウトできるように、Azure AD は、ユーザーが現在サインインしているすべてのアプリケーションの登録済み `LogoutUrl` に HTTP GET 要求を送信します。 アプリケーションは、ユーザーを識別するすべてのセッションを消去し、`200` 応答を返すことで、この要求に応答する必要があります。 アプリケーションでシングル サインアウトをサポートする場合は、アプリケーションのコードで `LogoutUrl` などを実装する必要があります。 `LogoutUrl` は Azure Portal から設定できます。

1. [Azure Portal](https://portal.azure.com) に移動します。
2. ページの右上隅のアカウントをクリックして、Active Directory を選択します。
3. 左側のナビゲーション パネルで **[Azure Active Directory]** 、 **[アプリの登録]** の順に選択し、アプリケーションを選択します。
4. **[設定]** 、 **[プロパティ]** の順にクリックして、 **[ログアウト URL]** テキスト ボックスを探します。 

## <a name="token-acquisition"></a>トークンの取得
多くの Web アプリは、ユーザーをサインインさせるだけでなく、OAuth を使用してそのユーザーの代わりに Web サービスにアクセスする必要もあります。 このシナリオでは、OpenID Connect を使ってユーザー認証を行うと同時に、[OAuth 承認コード フロー](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)を使って、`access_tokens` を取得するための `authorization_code` を取得します。

## <a name="get-access-tokens"></a>アクセス トークンの取得
アクセス トークンを取得するには、前に示したサインイン要求を変更する必要があります。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

アクセス許可スコープを要求に組み込み、`response_type=code+id_token` を使うことによって、`authorize` エンドポイントはユーザーが `scope` クエリ パラメーターで示されているアクセス許可に同意したことを確認し、アクセス トークンと引き換えに承認コードをアプリに返します。

### <a name="successful-response"></a>成功応答

`response_mode=form_post` を使用して `redirect_uri` に送信された成功応答は、次のようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| パラメーター | 説明 |
| --- | --- |
| id_token |アプリが要求した `id_token` 。 この `id_token` を使用してユーザーの身元を確認し、そのユーザーとのセッションを開始することができます。 |
| code |アプリが要求した authorization_code。 アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。 承認コードは有効期間が非常に短く、通常 10 分後には期限切れとなります。 |
| state |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

### <a name="error-response"></a>エラー応答

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

想定されるエラー コードとクライアントに推奨される対処法については、「[承認エンドポイント エラーのエラー コード](#error-codes-for-authorization-endpoint-errors)」を参照してください。

承認の `code` と `id_token` を取得した後は、ユーザーの代理でユーザーのサインインを実行し、[アクセス トークン](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)を取得することができます。 ユーザーをサインインさせるには、前に説明したように `id_token` を厳密に検証する必要があります。 アクセス トークンを取得するには、[OAuth コード フローのドキュメント](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)の「承認コードを使用してアクセス トークンを要求する」セクションで説明されているステップに従ってください。

## <a name="next-steps"></a>次のステップ

* [アクセス トークン](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)の詳細を学習します。
* [`id_token` と要求](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)の詳細を学習します。
