
---
title: "Azure AD v2.0 を使用して、セキュリティで保護されたリソースにユーザーの介入なしでアクセスする | Microsoft Docs"
description: "Azure AD で導入された OAuth 2.0 認証プロトコルを利用し、Web アプリケーションを構築します。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 08e2e561b6b939de4d829c816c1948361e16f6c4
ms.openlocfilehash: 2bbfed6e14a77964a32b84ae3f78f3529f13043c


---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v2.0 と OAuth 2.0 クライアント資格情報フロー
[OAuth 2.0 クライアント資格情報の許可](http://tools.ietf.org/html/rfc6749#section-4.4)は、*2 本足の OAuth* とも呼ばれ、アプリケーションの ID を使用した Web ホストのリソースへのアクセスに使用できます。 この種類の許可は、バックグラウンドでの実行が必要なサーバー間の相互作用に使用され、ユーザーとの即時の相互動作は必要ありません。 これらのアプリケーションは、*デーモン*または*サービス アカウント*と呼ばれます。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。 v2.0 エンドポイントを使用するかどうかを判断するには、[v2.0 の制限](active-directory-v2-limitations.md)に関する記事を参照してください。
> 
> 

より一般的な *3 本足の OAuth* では、特定のユーザーに代わり、クライアント アプリケーションにリソースへのアクセス許可が付与されます。 通常、[同意](active-directory-v2-scopes.md)プロセス中に、アクセス許可はユーザーからアプリケーションに委任されます。 ただし、クライアント資格情報フローでは、アクセス許可はアプリケーション自体に直接付与されます。 アプリがリソースにトークンを提示する場合、リソースはアプリ自体がアクションを実行する承認を行います。ユーザーが承認を行うわけではありません。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム
全体的なクライアント資格情報フローは、次のダイアグラムのようになります。 それぞれの手順については、この記事で後述します。

![クライアントの資格情報フロー](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>直接承認を取得する
通常、次の&2; つのうちのいずれかの方法で、アプリはリソースにアクセスするための直接承認を受けます。1 つはリソースのアクセス制御リスト (ACL) を介する方法、もう&1; つは Azure Active Directory (Azure AD) でのアプリケーション アクセス許可の割り当てを介する方法です。 これらの&2; つの方法は、Azure AD で最も一般的であり、クライアント資格情報フローを実行するクライアントとリソースに対して推奨されています。 ただし、リソースは、他の方法でもそのクライアントを承認することができます。 各リソース サーバーでは、そのアプリケーションに最も適した方法を選択できます。

### <a name="access-control-lists"></a>アクセス制御リスト
リソース プロバイダーは、アプリケーション ID 一覧に基づいて承認チェックを適用する場合があります。この一覧はリソース プロバイダーによって認識され、一定レベルのアクセス許可が付与されます。 リソースが v2.0 エンドポイントからトークンを受け取るときに、トークンをデコードし、`appid` と `iss` の要求からクライアント アプリケーション ID を抽出できます。 その後、リソースは、保持している ACL とアプリケーションを比較します。 ACL の粒度と方法は、リソース間で大幅に異なる場合があります。

一般的なユース ケースでは、ACL を使用して Web アプリケーションまたは Web API のテストを実行します。 Web API は、特定のクライアントに完全なアクセス許可のサブセットのみを与える可能性があります。 API でエンド ツー エンド テストを実行するには、テスト クライアントを作成し、そのクライアントで v2.0 エンドポイントからトークンを取得して、API に送信します。 API は、API のすべての機能にフル アクセスを持つテスト クライアントのアプリケーション ID が ACL にあるかどうかを確認します。 このような ACL を使用する場合は、必ず呼び出し元の `appid` 値を検証します。 さらに、トークンの `iss` 値が信頼されていることも検証します。

この種類の承認は、デーモンおよび個人の Microsoft アカウントを持つコンシューマー ユーザーが所有するデータにアクセスする必要があるサービス アカウントに共通しています。 組織が所有するデータでは、アプリケーションのアクセス許可を介して必要な承認を取得することをお勧めします。

### <a name="application-permissions"></a>アプリケーションのアクセス許可
ACL を使用する代わりに、API を使用してアプリケーションのアクセス許可のセットを公開することができます。 アプリケーションのアクセス許可は、組織の管理者によってアプリケーションに付与され、その組織と従業員が所有するデータにアクセスする場合にのみ使用されます。 たとえば、Microsoft Graph は次の操作を行うアクセス許可を公開しています:

* すべてのメールボックスのメールの読み取り
* すべてのメールボックスのメールの読み書き
* 任意のユーザーとしてのメールの送信
* ディレクトリ データの読み取り

アプリケーションのアクセス許可の詳細については、[Microsoft Graph](https://graph.microsoft.io) を参照してください。

アプリケーションのアクセス許可をアプリで使用するには、以降のセクションで説明する手順を行います。

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>アプリケーション登録ポータルでアクセス許可を要求する
1. [アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)でアプリケーションに移動するか、アプリを作成していない場合は[アプリを作成](active-directory-v2-app-registration.md)します。 アプリを作成するときに、少なくとも&1; つのアプリケーション シークレットを使用する必要があります。
2. **アプリケーションの直接アクセス許可**セクションを検索し、アプリに必要なアクセス許可を追加します。
3. アプリの登録を**保存**します。

#### <a name="recommended-sign-the-user-in-to-your-app"></a>推奨: アプリへのユーザーのサインイン
通常、アプリケーションのアクセス許可を使用するアプリケーションを構築する場合は、アプリ側に管理者がアプリのアクセス許可を承認するページまたはビューが必要です。 このページは、アプリのサインイン フローやアプリの設定の一部にするか、専用の "接続" フローにすることができます。 多くの場合、職場または学校の Microsoft アカウントでユーザーがサインインした後にのみ、”接続" ビューが表示されます。

ユーザーをアプリにサインインさせると、アクセス許可の承認をユーザーに依頼する前に、ユーザーが所属する組織を識別できます。 必須ではありませんが、ユーザーに向けたより直観的なエクスペリエンスの作成に役立ちます。 ユーザーのサインインを行うには、 [v2.0 プロトコル チュートリアル](active-directory-v2-protocols.md)に従ってください。

#### <a name="request-the-permissions-from-a-directory-admin"></a>ディレクトリ管理者にアクセス許可を要求する
組織の管理者にアクセス許可を要求する準備ができたら、ユーザーを v2.0 *管理者の同意エンドポイント*にリダイレクトできます。

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| テナント |必須 |アクセス許可を要求するディレクトリ テナント。 これは GUID またはフレンドリ名の形式で指定できます。 ユーザーが所属するテナントがわからず、任意のテナントでサインインを行う場合は、`common` を使用します。 |
| client_id |必須 |[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)によってアプリに割り当てられたアプリケーション ID。 |
| redirect_uri |必須 |処理するアプリの応答の送信先となるリダイレクト URI。 ポータルで登録したリダイレクト URI のいずれかと完全に一致させる必要があります (ただし、URL エンコードが必要であり、またその他のパスのセグメントがある場合があります)。 |
| state |推奨 |要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。 |

現時点で、Azure AD では、テナント管理者のみがサインインして、要求を完了することができます。 管理者は、アプリケーション登録ポータルでユーザーがアプリに要求したすべての直接のアプリケーション アクセス許可への承認を求められます。

##### <a name="successful-response"></a>成功応答
管理者がアプリケーションにアクセス許可を承認すると、成功応答は次のようになります。

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| パラメーター | Description |
| --- | --- | --- |
| テナント |アプリケーションが要求したアクセス許可を GUID 形式で付与するディレクトリ テナント。 |
| state |要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。 |
| admin_consent |**true**に設定します。 |

##### <a name="error-response"></a>エラー応答
管理者がアプリケーションにアクセス許可を承認しない場合、失敗した応答は次のようになります。

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| パラメーター | Description |
| --- | --- | --- |
| error |エラーの種類を分類したりエラーに対処したりする際に使用できるエラー コード文字列。 |
| error_description |エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |

エンドポイントをプロビジョニングしたアプリから成功応答を受信した後に、アプリは要求した直接のアプリケーション アクセス許可を獲得しています。 これで、必要なリソースのトークンを要求することができます。

## <a name="get-a-token"></a>トークンを取得する
アプリケーションに必要な承認を獲得後、API のアクセス トークンの取得を開始します。 クライアント資格情報の許可を使用してトークンを取得するには、次のように `/token` v2.0 エンドポイントに POST 要求を送信します。

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| client_id |必須 |[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)によってアプリに割り当てられたアプリケーション ID。 |
| scope |必須 |この要求の `scope` パラメーターに渡された値は、`.default` サフィックスが付いた目的のリソース識別子 (アプリケーション ID URI) である必要があります。 Microsoft Graph の場合は、値は `https://graph.microsoft.com/.default` です。 この値は、アプリ用に構成したすべての直接のアプリケーション アクセス許可のうち、v2.0 エンドポイントに目的のリソースに関連付けられたトークンを発行することを通知します。 |
| client_secret |必須 |アプリケーション登録ポータルでアプリ用に生成したアプリケーション シークレット。 |
| grant_type |必須 |`client_credentials`である必要があります。 |

##### <a name="successful-response"></a>成功応答
成功応答は次のようになります。

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| パラメーター | Description |
| --- | --- |
| access_token |要求されたアクセス トークン。 アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、認証することができます。 |
| token_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは `bearer` のみです。 |
| expires_in |アクセス トークンの有効期間 (秒)。 |

##### <a name="error-response"></a>エラー応答
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

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用できるエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |
| error_codes |STS 固有のエラー コードの一覧。診断に役立つ可能性がある。 |
| timestamp |エラーが発生した時刻。 |
| trace_id |要求の一意の識別子。診断に役立つ可能性がある。 |
| correlation_id |要求の一意の識別子。コンポーネント間での診断に役立つ可能性がある。 |

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

## <a name="code-sample"></a>サンプル コード
管理者の同意エンドポイントを使用するクライアント資格情報の許可を実装するアプリケーションの例を参照するには、[v2.0 デーモンのコード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)を参照してください。




<!--HONumber=Jan17_HO4-->


