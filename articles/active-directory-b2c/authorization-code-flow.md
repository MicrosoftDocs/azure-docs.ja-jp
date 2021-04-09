---
title: 承認コード フロー - Azure Active Directory B2C | Microsoft Docs
description: Azure AD B2C と OpenID Connect 認証プロトコルを使用して Web アプリをビルドする方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a6a993fdf4fd266afb9459fedd13412d8796e0a5
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611506"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C での OAuth 2.0 承認コード フロー

OAuth 2.0 認証コード付与を利用して、デバイスにインストールされているアプリに、Web API など、保護されているリソースにアクセスする権利を与えることができます。 Azure Active Directory B2C (Azure AD B2C) で導入された OAuth 2.0 を利用することで、サインアップ、サインイン、その他の ID 管理タスクをシングルページ、モバイル、デスクトップのアプリに追加できます。 この記事は言語に依存しません。 この記事では、オープンソース ライブラリを利用しないで、HTTP メッセージを送受信する方法について説明します。 可能な場合は、サポートされている Microsoft 認証ライブラリ (MSAL) を使用することをお勧めします。[MSAL を使用するサンプル アプリ](code-samples.md)をご覧ください。

OAuth 2.0 承認コード フローは、 [OAuth 2.0 仕様のセクション 4.1](https://tools.ietf.org/html/rfc6749)で規定されています。 Web アプリケーション、シングルページ アプリケーション、ネイティブにインストールされるアプリケーションを含め、多くの[アプリケーションの種類](application-types.md)で認証と承認を行う際にこのフローを利用できます。 OAuth 2.0 承認コード フローを利用して、[承認サーバー](protocols-overview.md)で保護されているリソースにアクセスするために使用できる、アプリケーション用のアクセス トークンと更新トークンを安全に取得できます。  クライアントは、更新トークンを使用して、アクセス トークンの期限が切れた後 (通常は 1 時間後)、新しいアクセス (および更新) トークンを取得できます。

この記事では、**パブリック クライアント** の OAuth 2.0 承認コード フローに重点を置いて説明します。 パブリック クライアントとは、秘密のパスワードの整合性を守る目的で信頼できないクライアント アプリケーションのことです。 これには、シングルページ アプリケーション、モバイル アプリ、デスクトップ アプリケーションなど、サーバー上で実行されない事実上すべてのアプリが該当します。

> [!NOTE]
> Azure AD B2C を利用して Web アプリに ID 管理を追加するには、OAuth 2.0 ではなく、[OpenID Connect](openid-connect.md) を使用してください。

Azure AD B2C は、単純な認証と承認以上のことができるように標準の OAuth 2.0 プロトコルを拡張したものです。 これには、[ユーザー フロー](user-flow-overview.md)が導入されています。 ユーザー フローと共に OAuth 2.0 を使用して、サインアップ、サインイン、プロファイル管理などのユーザー エクスペリエンスをアプリケーションに追加できます。 OAuth 2.0 プロトコルを使用する ID プロバイダーとしては、[Amazon](identity-provider-amazon.md)、[Azure Active Directory](identity-provider-azure-ad-single-tenant.md)、[Facebook](identity-provider-facebook.md)、[GitHub](identity-provider-github.md)、[Google](identity-provider-google.md)、[LinkedIn](identity-provider-linkedin.md) などがあります。

この記事の HTTP 要求を試すには:

1. `{tenant}`を Azure AD B2C テナントの名前に置き換えます。
1. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` を、Azure AD B2C テナントに登録済みのアプリケーションのアプリ ID に置き換えます。
1. `{policy}` を、テナント内に作成したポリシーの名前に置き換えます (例: `b2c_1_sign_in`)。

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>シングル ページ アプリに必要なリダイレクト URI セットアップ

シングル ページ アプリケーションの承認コード フローでは、追加のセットアップが必要です。  [シングルページ アプリケーションの作成](tutorial-register-spa.md)手順に従って、CORS に対して有効としてリダイレクト URI を正しくマークします。 既存のリダイレクト URI を更新して CORS を有効にするために、 **[アプリの登録]** の **[認証]** タブの [Web] セクションにある移行プロンプトをクリックできます。あるいは、**アプリの登録のマニフェスト エディター** を開き、`replyUrlsWithType` セクションでリダイレクト URI の `type` フィールドを `spa` に設定します。

`spa` のリダイレクトの種類は、暗黙的なフローと下位互換性があります。 トークンを取得するために暗黙的なフローを現在使用しているアプリは、問題なく `spa` のリダイレクト URI の種類に移行し、暗黙的なフローを引き続き使用することができます。

## <a name="1-get-an-authorization-code"></a>1.承認コードを取得する
承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。 これはフローの対話部分であり、ユーザーが操作します。 この要求で、クライアントは、`scope` パラメーターで、ユーザーから取得する必要のあるアクセス許可を指定します。 3 つの例を以下に示します (読みやすいように改行してあります)。それぞれ異なるユーザー フローが使用されています。


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
|{tenant}| 必須 | Azure AD B2C テナントの名前。|
| {policy} | 必須 | 実行するユーザー フロー。 Azure AD B2C テナントに作成したユーザー フローの名前を指定します。 例: `b2c_1_sign_in`、`b2c_1_sign_up`、または`b2c_1_edit_profile`。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com) でアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |応答の種類。承認コード フローでは `code` を指定する必要があります。 |
| redirect_uri |必須 |アプリのリダイレクト URI。アプリは、この URI で認証応答を送受信します。 ポータルで登録したいずれかのリダイレクト URI と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure Active Directory (Azure AD) に示します。 クライアント ID をスコープとして使用することは、同じクライアント ID で表される、独自のサービスまたは Web API に対して使用できるアクセス トークンをアプリが必要とすることを示します。  `offline_access` スコープは、アプリがリソースに長時間アクセスするには更新トークンが必要になることを示します。 Azure AD B2C に ID トークンを要求するために、`openid` スコープを使用することもできます。 |
| response_mode |推奨 |結果として得られた承認コードをアプリに返すときに使用するメソッド。 `query`、`form_post`、または `fragment` を指定できます。 |
| state |推奨 |要求に含まれる値。使用したい任意の内容の文字列を指定できます。 通常、クロスサイト リクエスト フォージェリ攻撃を防ぐために、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内のユーザーの状態に関する情報をエンコードする目的にも使用されます。 たとえば、ユーザーが表示していたページや実行されていたユーザー フローがその対象です。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類。 現在、有効な値は `login` のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。 シングル サインオンは作用しません。 |
| code_challenge  | 推奨/必須 | PKCE (Proof Key for Code Exchange) を使用して承認コード付与をセキュリティ保護するために使用されます。 `code_challenge_method` が含まれている場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 これは、すべての種類のアプリケーション (ネイティブ アプリ、SPA、Web アプリなどの機密クライアント) で推奨されるようになりました。 | 
| `code_challenge_method` | 推奨/必須 | `code_challenge` パラメーターの `code_verifier` をエンコードするために使用されるメソッド。 これは `S256` である *べき* ですが、何らかの理由によってクライアントで SHA256 がサポートされない場合、仕様では `plain` の使用が許可されています。 <br/><br/>除外されていると、`code_challenge` が含まれている場合、`code_challenge` はプレーンテキストであると見なされます。 Microsoft ID プラットフォームは `plain` と `S256` の両方をサポートします。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 これは、[承認コード フローを使用するシングル ページ アプリ](tutorial-register-spa.md)には必須です。|
| login_hint | いいえ| サインインページのサインイン名フィールドに事前に入力するために使用できます。 詳細については、「[サインイン名を事前入力する](direct-signin.md#prepopulate-the-sign-in-name)」を参照してください。  |
| domain_hint | いいえ| サインインに使用する必要があるソーシャル ID プロバイダーに関するヒントを Azure AD B2C に提供します。 有効な値が含まれている場合、ユーザーは直接 ID プロバイダーのサインイン ページに移動します。  詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 |
| カスタム パラメーター | いいえ| [カスタムポリシー](custom-policy-overview.md)で使用できるカスタム パラメーター。 例: [動的なカスタム ページ コンテンツ URI](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)、または[キー値要求リゾルバー](claim-resolver-overview.md#oauth2-key-value-parameters)。 |

この時点で、ユーザーはユーザー フローのワークフローを完了するよう求められます。 ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりするなど、いくつかの手順が必要なことがあります。 ユーザー アクションは、ユーザー フローがどのように定義されているかによって異なります。

ユーザーがユーザー フローを完了すると、Azure AD はユーザーが `redirect_uri` に使用した値でアプリに応答を返します。 これには、`response_mode` パラメーターで指定されたメソッドが使用されます。 応答は、実行されたユーザー フローとは無関係に、ユーザー アクションの各シナリオでまったく同じになります。

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| パラメーター | 説明 |
| --- | --- |
| code |アプリが要求した承認コード。 アプリは、承認コードを使用してターゲット リソースのアクセス トークンを要求できます。 承認コードの有効期間は非常に短時間です。 通常、約 10 分で期限が切れます。 |
| state |詳細については前のセクションの表を参照してください。 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。 |

アプリ側でエラーを適切に処理できるように、エラー応答をリダイレクト URI に送信することもできます。

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類する際に使用できるエラー コード文字列。 この文字列はエラーへの対応に利用することもできます。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |
| state |前の表の詳しい説明を参照してください。 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。 |

## <a name="2-get-an-access-token"></a>2.アクセス トークンを取得する
承認コードを取得したところで、POST 要求を `/token` エンドポイントに送信して、トークンの `code` を目的のリソースに適用できます。 Azure AD B2C では、対応するスコープを要求の中で指定することによって、普通に[他の API のアクセス トークンを要求](access-tokens.md#request-a-token)できます。

さらに、要求スコープとしてアプリのクライアント ID を使用するという慣例によって、アプリ独自のバックエンド Web API 用アクセス トークンを要求することもできます (その場合、そのクライアント ID を "audience" として持つアクセス トークンが得られます)。

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
|{tenant}| 必須 | Azure AD B2C テナントの名前。|
|{policy}| 必須| 認証コードの取得に使用されたユーザー フロー。 この要求に別のユーザー フローを使用することはできません。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com) でアプリに割り当てられたアプリケーション ID。|
| client_secret | はい (Web アプリの場合) | [Azure portal](https://portal.azure.com/) で生成されたアプリケーション シークレット。 クライアントが安全にクライアント シークレットを格納できる Web アプリのシナリオでは、このフローでクライアント シークレットが使用されます。 ネイティブ アプリ (パブリック クライアント) のシナリオでは、クライアント シークレットは安全に保存できないため、この呼び出しでは使用されません。 クライアント シークレットを使用する場合は、定期的に変更してください。 |
| grant_type |必須 |付与の種類。 承認コード フローでは、付与の種類には `authorization_code` を指定する必要があります。 |
| scope |推奨 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 クライアント ID をスコープとして使用することは、同じクライアント ID で表される、独自のサービスまたは Web API に対して使用できるアクセス トークンをアプリが必要とすることを示します。  `offline_access` スコープは、アプリがリソースに長時間アクセスするには更新トークンが必要になることを示します。  Azure AD B2C に ID トークンを要求するために、`openid` スコープを使用することもできます。 |
| code |必須 |フローの最初の段階で取得した承認コード。 |
| redirect_uri |必須 |承認コードを受け取った、アプリケーションのリダイレクト URI。 |
| code_verifier | 推奨 | authorization_code を取得するために使用されたのと同じ code_verifier。 承認コード付与要求で PKCE が使用された場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 |

正常なトークン応答は次のようになります。

```json
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
| token_type |トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| access_token |要求した署名付き JSON Web トークン (JWT)。 |
| scope |トークンが有効である範囲。 スコープは、後で使用できるようトークンをキャッシュするためにも使用できます。 |
| expires_in |トークンが有効な時間の長さ (秒単位)。 |
| refresh_token |OAuth 2.0 更新トークン。 現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。 更新トークンの有効期間は長期です。 リソースへのアクセスを長期間保持するときに利用できます。 詳細については、「[Azure AD B2C: トークン リファレンス](tokens-overview.md)」を参照してください。 |

エラー応答は次のようになります。

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類する際に使用できるエラー コード文字列。 この文字列はエラーへの対応に利用することもできます。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="3-use-the-token"></a>3.トークンを使用する
これでアクセス トークンを正常に取得できたので、そのトークンを `Authorization` ヘッダー内に含めることによって、それをバックエンド Web API への要求で使用できます。

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4.トークンを更新する
アクセス トークンと ID トークンの有効期限は非常に短いです。 有効期限が切れた後に、リソースに引き続きアクセスできるようにするには、トークンを更新する必要があります。 そのためには、別の POST 要求を `/token` エンドポイントに送信します。 このとき、`code` の代わりに `refresh_token` を指定します。

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
|{tenant}| 必須 | Azure AD B2C テナントの名前。|
|{policy} |必須 |元の更新トークンの取得に使用されたユーザー フロー。 この要求に別のユーザー フローを使用することはできません。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com) でアプリに割り当てられたアプリケーション ID。 |
| client_secret | はい (Web アプリの場合) | [Azure portal](https://portal.azure.com/) で生成されたアプリケーション シークレット。 クライアントが安全にクライアント シークレットを格納できる Web アプリのシナリオでは、このフローでクライアント シークレットが使用されます。 ネイティブ アプリ (パブリック クライアント) のシナリオでは、クライアント シークレットは安全に保存できないため、この呼び出しでは使用されません。 クライアント シークレットを使用する場合は、定期的に変更してください。 |
| grant_type |必須 |付与の種類。 この段階の承認コード フローでは、付与の種類には `refresh_token` を指定する必要があります。 |
| scope |推奨 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 クライアント ID をスコープとして使用することは、同じクライアント ID で表される、独自のサービスまたは Web API に対して使用できるアクセス トークンをアプリが必要とすることを示します。  `offline_access` スコープは、アプリがリソースに長時間アクセスするには更新トークンが必要になることを示します。  Azure AD B2C に ID トークンを要求するために、`openid` スコープを使用することもできます。 |
| redirect_uri |オプション |承認コードを受け取った、アプリケーションのリダイレクト URI。 |
| refresh_token |必須 |フローの第 2 段階で取得した元の更新トークン。 |

正常なトークン応答は次のようになります。

```json
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
| token_type |トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| access_token |要求した署名付きの JWT。 |
| scope |トークンが有効である範囲。 スコープを使用して、後で使用するトークンをキャッシュすることもできます。 |
| expires_in |トークンが有効な時間の長さ (秒単位)。 |
| refresh_token |OAuth 2.0 更新トークン。 現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。 更新トークンは有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 詳細については、「[Azure AD B2C: トークン リファレンス](tokens-overview.md)」を参照してください。 |

エラー応答は次のようになります。

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類する際に使用できるエラー コード文字列。 この文字列はエラーへの対応に利用することもできます。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="use-your-own-azure-ad-b2c-directory"></a>独自の Azure AD B2C ディレクトリを使用する
これらの要求を自分で試すには、次の手順を実行します。 この記事で使用したサンプルの値を独自の値で置き換えます。

1. [Azure AD B2C ディレクトリを作成します](tutorial-create-tenant.md)。 要求で独自のディレクトリの名前を使用します。
2. [アプリケーションを作成し](tutorial-register-applications.md)、アプリケーション ID とリダイレクト URI を取得します。 アプリにネイティブ クライアントを含めます。
3. [使用するユーザー フローを作成](user-flow-overview.md)して、そのユーザー フロー名を取得します。
