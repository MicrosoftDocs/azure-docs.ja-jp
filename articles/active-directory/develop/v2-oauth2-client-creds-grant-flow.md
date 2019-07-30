---
title: Microsoft ID プラットフォームを使用して、セキュリティで保護されたリソースにユーザーの介入なしでアクセスする | Azure
description: Microsoft ID プラットフォームで導入された OAuth 2.0 認証プロトコルを利用して、Web アプリケーションを構築します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
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
ms.openlocfilehash: 85a32244a9aff9319343fd7d3961941973aa9d9a
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482247"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft ID プラットフォームと OAuth 2.0 クライアント資格情報フロー

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

RFC 6749 に明記されている [OAuth 2.0 クライアント資格情報の許可](https://tools.ietf.org/html/rfc6749#section-4.4)は、*2 本足の OAuth* とも呼ばれ、アプリケーションの ID を使用した Web ホストのリソースへのアクセスに使用できます。 この種類の許可は、バックグラウンドでの実行が必要なサーバー間の相互作用に使用され、ユーザーとの即時の相互動作は必要ありません。 これらのアプリケーションは、 *"デーモン"* または *"サービス アカウント"* と呼ばれます。

OAuth 2.0 クライアント資格情報付与フローでは、Web サービス (Confidential クライアント) が別の Web サービスを呼び出すときに、ユーザーを偽装する代わりに、独自の資格情報を使用して認証することができます。 このシナリオでは、クライアントは通常、中間層の Web サービス、デーモン サービス、または Web サイトです。 高いレベルの保証では、Microsoft ID プラットフォームにより、呼び出し元サービスが、資格情報として (共有シークレットではなく) 証明書を使用することもできます。

> [!NOTE]
> Microsoft ID プラットフォームのエンドポイントでは、すべての Azure AD シナリオや機能をサポートしているわけではありません。 Microsoft ID プラットフォームのエンドポイントを使用する必要があるかどうかを判断するには、[Microsoft ID プラットフォームの制限事項](active-directory-v2-limitations.md)に関する記事を参照してください。

より一般的な *3 本足の OAuth* では、特定のユーザーに代わり、クライアント アプリケーションにリソースへのアクセス許可が付与されます。 通常、[同意](v2-permissions-and-consent.md)プロセス中に、アクセス許可はユーザーからアプリケーションに委任されます。 ただし、クライアント資格情報 (*two-legged OAuth*) フローでは、アクセス許可はアプリケーション自体に直接付与されます。 アプリがリソースにトークンを提示する場合、リソースでは、ユーザーではなくアプリ自体がアクション実行の承認を行います。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

全体的なクライアント資格情報フローは、次の図のようになります。 それぞれの手順については、この記事で後述します。

![クライアント資格情報フローを示す図](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>直接承認を取得する

アプリは通常、次の 2 つの方法のいずれかで、直接承認を受信してリソースにアクセスします。

* [リソースのアクセス制御リスト (ACL) を利用する](#access-control-lists)
* [Azure AD のアプリケーション許可の割り当てを利用する](#application-permissions)

これらの 2 つの方法は、Azure AD で最も一般的であり、クライアント資格情報フローを実行するクライアントとリソースに対して推奨されています。 リソースは、他の方法でそのクライアントを承認することもできます。 各リソース サーバーでは、そのアプリケーションに最も適した方法を選択できます。

### <a name="access-control-lists"></a>アクセス制御リスト

リソース プロバイダーは、アプリケーション (クライアント) ID 一覧に基づいて承認チェックを適用する場合があります。この一覧はリソース プロバイダーによって認識され、一定レベルのアクセス許可が付与されます。 リソースは Microsoft ID プラットフォーム エンドポイントからトークンを受け取るときに、トークンをデコードし、`appid` と `iss` の要求からクライアント アプリケーション ID を抽出できます。 その後、リソースは、保持しているアクセス制御リスト (ACL) とアプリケーションを比較します。 ACL の粒度と方法は、リソース間で大幅に異なる場合があります。

一般的なユース ケースでは、ACL を使用して Web アプリケーションまたは Web API のテストを実行します。 Web API は、特定のクライアントに完全なアクセス許可のサブセットのみを与える可能性があります。 API でエンドツーエンド テストを実行するには、Microsoft ID プラットフォーム エンドポイントからトークンを取得し、次にそれらを API に送信する必要があるテスト クライアントを作成します。 API は、API のすべての機能にフル アクセスを持つテスト クライアントのアプリケーション ID が ACL にあるかどうかを確認します。 この種類の ACL を使用する場合、必ず呼び出し元の `appid` 値を検証するだけでなく、トークンの `iss` 値が信頼されていることも検証します。

この種類の承認は、デーモンおよび個人の Microsoft アカウントを持つコンシューマー ユーザーが所有するデータにアクセスする必要があるサービス アカウントに共通しています。 組織が所有するデータでは、アプリケーションのアクセス許可を介して必要な承認を取得することをお勧めします。

### <a name="application-permissions"></a>アプリケーションのアクセス許可

ACL を使用する代わりに、API を使用してアプリケーションのアクセス許可のセットを公開することができます。 アプリケーションのアクセス許可は、組織の管理者によってアプリケーションに付与され、その組織と従業員が所有するデータにアクセスする場合にのみ使用されます。 たとえば、Microsoft Graph は次の操作を行うアクセス許可を公開しています:

* すべてのメールボックスのメールの読み取り
* すべてのメールボックスのメールの読み書き
* 任意のユーザーとしてのメールの送信
* ディレクトリ データの読み取り

アプリケーションのアクセス許可の詳細については、[Microsoft Graph](https://developer.microsoft.com/graph) を参照してください。

アプリケーションのアクセス許可をアプリで使用するには、以降のセクションで説明する手順に従います。

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>アプリケーション登録ポータルでアクセス許可を要求する

1. 新しい[アプリの登録 (プレビュー) エクスペリエンス](quickstart-register-app.md)によって、アプリを登録し、作成します。
2. アプリの登録 (プレビュー) エクスペリエンスで、アプリケーションに移動します。 トークンを要求するには少なくとも 1 つのクライアント シークレットが必要なため、 **[証明書とシークレット]** セクションに移動し、**新しいクライアント シークレット**を追加します。
3. **[API のアクセス許可]** セクションを見つけて、アプリに必要な **[アプリケーションのアクセス許可]** を追加します。
4. アプリの登録を**保存**します。

#### <a name="recommended-sign-the-user-into-your-app"></a>推奨:ユーザーをアプリにサインインさせる

通常、アプリケーションのアクセス許可を使用するアプリケーションを構築する場合は、アプリ側に管理者がアプリのアクセス許可を承認するページまたはビューが必要です。 このページは、アプリのサインイン フローやアプリの設定の一部にするか、専用の "接続" フローにすることができます。 多くの場合、職場または学校の Microsoft アカウントでユーザーがサインインした後にのみ、”接続" ビューが表示されます。

ユーザーをアプリにサインインさせると、アクセス許可の承認をユーザーに依頼する前に、ユーザーが所属する組織を識別できます。 必須ではありませんが、ユーザーに向けたより直観的なエクスペリエンスの作成に役立ちます。 ユーザーをサインインさせるには、[Microsoft ID プラットフォーム プロトコルのチュートリアル](active-directory-v2-protocols.md)に従ってください。

#### <a name="request-the-permissions-from-a-directory-admin"></a>ディレクトリ管理者にアクセス許可を要求する

組織の管理者にアクセス許可を要求する準備ができたら、Microsoft ID プラットフォームの*管理者の同意エンドポイント*にユーザーをリダイレクトできます。

> [!TIP]
> を必ず置き換えてください)。 (最良の結果を得るには、ご自身のアプリ ID を使用してください。チュートリアル アプリケーションでは有用なアクセス許可は要求されません。)[![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| `tenant` | 必須 | アクセス許可を要求するディレクトリ テナント。 これは GUID またはフレンドリ名の形式で指定できます。 ユーザーが所属するテナントがわからず、任意のテナントでサインインを行う場合は、`common` を使用します。 |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた**アプリケーション (クライアント) ID**。 |
| `redirect_uri` | 必須 | 処理するアプリの応答の送信先となるリダイレクト URI。 ポータルで登録したリダイレクト URI のいずれかと完全に一致させる必要があります (ただし、URL エンコードが必要であり、またその他のパスのセグメントがある場合があります)。 |
| `state` | 推奨 | トークンの応答でも返される要求に含まれる値。 任意の文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。 |

現時点で、Azure AD では、テナント管理者のみがサインインして、要求を完了することができます。 管理者は、アプリケーション登録ポータルでユーザーがアプリに要求したすべての直接のアプリケーション アクセス許可への承認を求められます。

##### <a name="successful-response"></a>成功応答

管理者がアプリケーションにアクセス許可を承認すると、成功応答は次のようになります。

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| パラメーター | 説明 |
| --- | --- |
| `tenant` | アプリケーションが要求したアクセス許可を GUID 形式で付与するディレクトリ テナント。 |
| `state` | 要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。 |
| `admin_consent` | **True** に設定します。 |

##### <a name="error-response"></a>エラー応答

管理者がアプリケーションにアクセス許可を承認しない場合、失敗した応答は次のようになります。

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| パラメーター | 説明 |
| --- | --- |
| `error` | エラーの種類を分類したりエラーに対処したりする際に使用できるエラー コード文字列。 |
| `error_description` | エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |

エンドポイントをプロビジョニングしたアプリから成功応答を受信した後に、アプリは要求した直接のアプリケーション アクセス許可を獲得しています。 これで、必要なリソースのトークンを要求することができます。

## <a name="get-a-token"></a>トークンを取得する

アプリケーションに必要な承認を獲得後、API のアクセス トークンの取得を開始します。 クライアント資格情報の許可を使用してトークンを取得するには、次のように `/token` Microsoft ID プラットフォーム エンドポイントに POST 要求を送信します。

> [!TIP]
> を必ず置き換えてください)。 (最良の結果を得るには、ご自身のアプリ ID を使用してください。チュートリアル アプリケーションでは有用なアクセス許可は要求されません。)[![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>最初のケース:共有シークレットを使ったアクセス トークン要求

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| `tenant` | 必須 | GUID またはドメイン名形式で示すディレクトリ テナント。これに対してアプリケーションが動作する予定です。 |
| `client_id` | 必須 | お使いのアプリに割り当てられたアプリケーション ID。 アプリを登録したポータルで、この情報を確認できます。 |
| `scope` | 必須 | この要求の `scope` パラメーターに渡される値は、`.default` サフィックスが付いた目的のリソースのリソース識別子 (アプリケーション ID URI) である必要があります。 Microsoft Graph の場合は、値は `https://graph.microsoft.com/.default` です。 <br/>この値は、アプリ用に構成したすべての直接のアプリケーション アクセス許可のうち、目的のリソースに関連付けられたトークンを発行するように、Microsoft ID プラットフォーム エンドポイントに命じます。 `/.default` スコープの詳細については、[同意に関するドキュメント](v2-permissions-and-consent.md#the-default-scope)を参照してください。 |
| `client_secret` | 必須 | アプリケーション登録ポータルでアプリ用に生成したクライアント シークレット。 クライアント シークレットは、送信前に URL エンコードされる必要があります。 |
| `grant_type` | 必須 | `client_credentials` に設定する必要があります。 |

### <a name="second-case-access-token-request-with-a-certificate"></a>2 番目のケース:証明書を使ったアクセス トークン要求

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| `tenant` | 必須 | GUID またはドメイン名形式で示すディレクトリ テナント。これに対してアプリケーションが動作する予定です。 |
| `client_id` | 必須 |お使いのアプリに割り当てられるアプリケーション (クライアント) ID。 |
| `scope` | 必須 | この要求の `scope` パラメーターに渡される値は、`.default` サフィックスが付いた目的のリソースのリソース識別子 (アプリケーション ID URI) である必要があります。 Microsoft Graph の場合は、値は `https://graph.microsoft.com/.default` です。 <br/>この値は、アプリ用に構成したすべての直接のアプリケーション アクセス許可のうち、目的のリソースに関連付けられたトークンを発行するように、Microsoft ID プラットフォーム エンドポイントに命じます。 `/.default` スコープの詳細については、[同意に関するドキュメント](v2-permissions-and-consent.md#the-default-scope)を参照してください。 |
| `client_assertion_type` | 必須 | この値は `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` に設定する必要があります。 |
| `client_assertion` | 必須 | 作成する必要があるアサーション (JSON Web トークン) です。このアサーションは、アプリケーションの資格情報として登録した証明書で署名する必要があります。 証明書の登録方法とアサーションの形式の詳細については、[証明書資格情報](active-directory-certificate-credentials.md)に関する記事を参照してください。|
| `grant_type` | 必須 | `client_credentials` に設定する必要があります。 |

パラメーターは、共有シークレットによる要求のパラメーターとほぼ同じであることに注意してください。唯一異なるのは、client_secret パラメーターが、client_assertion_type と client_assertion の 2 つのパラメーターに置き換えられている点です。

### <a name="successful-response"></a>成功応答

成功応答は次のようになります。

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| パラメーター | 説明 |
| --- | --- |
| `access_token` | 要求されたアクセス トークン。 アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、認証することができます。 |
| `token_type` | トークン タイプ値を指定します。 Microsoft ID プラットフォームでサポートされる種類は `bearer` のみです。 |
| `expires_in` | アクセス トークンが有効な時間 (秒単位)。 |

### <a name="error-response"></a>エラー応答

エラーの場合の応答は次のようになります。

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| パラメーター | 説明 |
| --- | --- |
| `error` | 発生したエラーの種類を分類したりエラーに対処したりする際に使用できるエラー コード文字列。 |
| `error_description` | 認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |
| `error_codes` | STS 固有のエラー コードの一覧。診断に役立つ可能性がある。 |
| `timestamp` | エラーが発生した時刻。 |
| `trace_id` | 診断に役立つ、要求の一意の識別子。 |
| `correlation_id` | コンポーネント間での診断に役立つ、要求の一意の識別子。 |

> [!NOTE]
> アプリケーションが v2 トークンを受信できるようにするために、Microsoft Azure portal 内からアプリケーションのマニフェスト ファイルを更新できます。 属性 `accessTokenAcceptedVersion` を追加し、値を `"accessTokenAcceptedVersion": 2` として 2 に設定できます。 同じことについて詳しく理解するには、[アプリケーション マニフェスト](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-reference)に関する記事を確認してください。 既定では、アプリケーションは現在、v1 トークンを受信します。 これがアプリケーション/Web API マニフェスト内で定義されていない場合、マニフェスト内のこの属性に対する値は、既定で 1 に設定され、したがってアプリケーションは v1 トークンを受信します。  


## <a name="use-a-token"></a>トークンを使用する

トークンを獲得したら、そのトークンを使用してリソースへの要求を作成します。 トークンの有効期限が切れたときは、`/token` エンドポイントへの要求を繰り返し、新しいアクセス トークンを取得します。

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>コード サンプルとその他のドキュメント

Microsoft 認証ライブラリの[クライアントの資格情報の概要に関するドキュメント](https://aka.ms/msal-net-client-credentials)を参照してください。

| サンプル | プラットフォーム |説明 |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1 コンソール | ユーザーの代理ではなく、アプリケーションの ID を使用して Microsoft Graph へのクエリを実行するテナントのユーザーを表示する単純な .NET Core アプリケーション。 このサンプルは、認証に証明書を使用するバリエーションも示しています。 |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | ユーザーの代理ではなく、アプリケーションの ID を使用して Microsoft Graph のデータを同期する Web アプリケーション。 |
