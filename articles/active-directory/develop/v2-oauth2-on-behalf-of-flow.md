---
title: Microsoft ID プラットフォームと OAuth2.0 On-Behalf-Of フロー | Azure
titleSuffix: Microsoft identity platform
description: この記事では、HTTP メッセージを使用して、OAuth2.0 On-Behalf-Of フローを使用するサービス間の認証を実装する方法について説明します。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ff8e03b813e2cb890192667e3466d920eaabc72c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756082"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft ID プラットフォームと OAuth2.0 On-Behalf-Of フロー


OAuth 2.0 の On-Behalf-Of (OBO) フローは、アプリケーションがサービス/Web API を呼び出し、それがさらに別のサービス/Web API を呼び出す必要のあるユース ケースを提供します。 その考え方は、委任されたユーザー ID とアクセス許可を要求チェーン経由で伝達するというものです。 中間層サービスがダウンストリーム サービスに認証済み要求を発行するには、そのサービスは Microsoft ID プラットフォームからのアクセス トークンをユーザーに代わってセキュリティ保護する必要があります。

この記事では、アプリケーションでプロトコルに対して直接プログラミングする方法について説明します。  可能な場合は、[トークンを取得してセキュリティで保護された Web API を呼び出す](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)代わりに、サポートされている Microsoft 認証ライブラリ (MSAL) を使用することをお勧めします。  また、[MSAL を使用するサンプル アプリ](sample-v2-code.md)も参照してください。


2018 年 5 月の時点では、暗黙的なフローから派生する一部の `id_token` は、OBO フローで使用できません。 シングルページ アプリ (SPA) では、中間層の機密クライアントに **アクセス** トークンを渡して、OBO フローを代わりに実行する必要があります。 OBO 呼び出しを実行できるクライアントの詳細については、[制限事項](#client-limitations)に関する記事を参照してください。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

[OAuth 2.0 認証コード付与フロー](v2-oauth2-auth-code-flow.md)または他のログイン フローを使用するアプリケーションで、ユーザーが認証されているとします。 この時点で、そのアプリケーションには、中間層 Web API (API A) にアクセスするためのユーザーの要求と同意を含む *API A の* アクセス トークン (トークン A) があります。 ここで、API A はダウンストリーム Web API (API B) に認証済み要求を発行する必要があります。

以降の手順は OBO フローを構成するものであり、次の図を使用して説明します。

![OAuth2.0 の On-Behalf-Of フローを示します](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. クライアント アプリケーションは、トークン A (API A の `aud` 要求) を使用して API A に要求を発行します。
1. API A が Microsoft ID プラットフォーム トークン発行エンドポイントに対して認証を行い、API B にアクセスするためのトークンを要求します。
1. Microsoft ID プラットフォーム トークン発行エンドポイントはトークン A を使用して API A の資格情報を検証し、API B (トークン B) から API B へのアクセス トークンを発行します。
1. API A によって、API B への要求の承認ヘッダー内にトークン B が設定されます。
1. セキュリティで保護されたリソースからのデータが API B によって API A に返され、次に、クライアントに返されます。

このシナリオでは、中間層サービスに、ダウンストリーム API にアクセスするユーザーの同意を得るためのユーザー操作はありません。 そのため、ダウンストリーム API へのアクセス権を付与するオプションは、認証中の同意手順の一部として事前に提供されます。 ご使用のアプリに対してこれを設定する方法について詳しくは、「[中間層アプリケーションの同意の取得](#gaining-consent-for-the-middle-tier-application)」を参照してください。

## <a name="middle-tier-access-token-request"></a>中間層アクセス トークン要求

アクセス トークンを要求するには、次のパラメーターを使用して、テナントに固有の Microsoft ID プラットフォーム トークン エンドポイントへの HTTP POST を作成します。

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

クライアント アプリケーションのセキュリティ保護に共有シークレットまたは証明書のどちらを使うかに応じて、2 つのケースがあります。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>最初のケース:共有シークレットを使ったアクセス トークン要求

共有シークレットを使用する場合、サービス間のアクセス トークン要求には、次のパラメーターが含まれてます。

| パラメーター | Type | 説明 |
| --- | --- | --- |
| `grant_type` | 必須 | トークン要求の種類。 JWT を使用した要求では、この値は `urn:ietf:params:oauth:grant-type:jwt-bearer` にする必要があります。 |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページで、アプリに割り当てられたアプリケーション (クライアント) ID。 |
| `client_secret` | 必須 | Azure portal の [アプリの登録] ページで、アプリ用に生成したクライアント シークレット。 |
| `assertion` | 必須 | 中間層 API に送信されたアクセス トークン。  このトークンには、この OBO 要求を行うアプリ (`client-id` フィールドに示されるアプリ) の対象ユーザー (`aud`) 要求が必要です。 アプリケーションは別のアプリ用のトークンを利用することはできません (たとえば、クライアントが API に MS Graph 用のトークンを送信した場合、API は OBO を使用してそれを利用することはできません。  代わりに、トークンを拒否する必要があります)。  |
| `scope` | 必須 | トークン要求のスコープのスペース区切りリスト。 詳細については、「[スコープ](v2-permissions-and-consent.md)」を参照してください。 |
| `requested_token_use` | 必須 | 要求の処理方法を指定します。 OBO フローでは、この値は `on_behalf_of` に設定する必要があります。 |

#### <a name="example"></a>例

次の HTTP POST は、 https://graph.microsoft.com Web API に対する `user.read` スコープを含むアクセス トークンと更新トークンを要求します。

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>2 番目のケース:証明書を使ったアクセス トークン要求

証明書を含むサービス間のアクセス トークン要求には、次のパラメーターが含まれています。

| パラメーター | Type | 説明 |
| --- | --- | --- |
| `grant_type` | 必須 | トークン要求の種類。 JWT を使用した要求では、この値は `urn:ietf:params:oauth:grant-type:jwt-bearer` にする必要があります。 |
| `client_id` | 必須 |  [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページで、アプリに割り当てられたアプリケーション (クライアント) ID。 |
| `client_assertion_type` | 必須 | 値は `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` である必要があります。 |
| `client_assertion` | 必須 | 作成する必要があるアサーション (JSON Web トークン) です。このアサーションは、アプリケーションの資格情報として登録した証明書で署名する必要があります。 証明書の登録方法とアサーションの形式の詳細については、[証明書資格情報](active-directory-certificate-credentials.md)に関する記事を参照してください。 |
| `assertion` | 必須 |  中間層 API に送信されたアクセス トークン。  このトークンには、この OBO 要求を行うアプリ (`client-id` フィールドに示されるアプリ) の対象ユーザー (`aud`) 要求が必要です。 アプリケーションは別のアプリ用のトークンを利用することはできません (たとえば、クライアントが API に MS Graph 用のトークンを送信した場合、API は OBO を使用してそれを利用することはできません。  代わりに、トークンを拒否する必要があります)。  |
| `requested_token_use` | 必須 | 要求の処理方法を指定します。 OBO フローでは、この値は `on_behalf_of` に設定する必要があります。 |
| `scope` | 必須 | トークン要求のスコープのスペース区切りリスト。 詳細については、「[スコープ](v2-permissions-and-consent.md)」を参照してください。|

パラメーターは、共有シークレットによる要求のパラメーターとほぼ同じであることに注意してください。唯一異なるのは、`client_secret` パラメーターが、`client_assertion_type` と `client_assertion` の 2 つのパラメーターに置き換えられている点です。

#### <a name="example"></a>例

次の HTTP POST は、証明書を使用して https://graph.microsoft.com Web API に対する `user.read` スコープを含むアクセス トークンを要求します。

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>中間層アクセス トークン応答

成功応答は、次のパラメーターを含む JSON OAuth 2.0 応答です。

| パラメーター | 説明 |
| --- | --- |
| `token_type` | トークン タイプ値を指定します。 Microsoft ID プラットフォームでサポートされる種類は `Bearer` のみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。 |
| `scope` | トークンで付与されるアクセスのスコープ。 |
| `expires_in` | アクセス トークンが有効な時間の長さ (秒単位)。 |
| `access_token` | 要求されたアクセス トークン。 呼び出し元のサービスは、このトークンを使用して受信側のサービスへの認証を行うことができます。 |
| `refresh_token` | 要求されたアクセス トークンの更新トークン。 呼び出し元のサービスは、現在のアクセス トークンの期限が切れた後に、このトークンを使用して別のアクセス トークンを要求できます。 更新トークンは、`offline_access` スコープが要求された場合にのみ提供されます。 |

### <a name="success-response-example"></a>成功応答の例

次の例に、 https://graph.microsoft.com Web API へのアクセス トークン要求に対する成功応答を示します。

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

上記のアクセス トークンは、Microsoft Graph 用に v1.0 でフォーマットされたトークンです。 これは、トークンの形式はアクセス対象の **リソース** に基づいたものであり、要求に使用されるエンドポイントとは無関係であるためです。 Microsoft Graph は v1.0 トークンを受け入れるように設定されているため、クライアントが Microsoft Graph のトークンを要求すると、Microsoft ID プラットフォームによって v1.0 アクセス トークンが生成されます。 他のアプリからは、v2.0 形式のトークンや v1.0 形式のトークン、さらには専用や暗号化されたトークン形式が必要であると示される場合があります。  v1.0 と v2.0 のエンドポイントは両方とも、どちらの形式のトークンも出力できます。この方法では、クライアントによってトークンが要求された方法や場所に関係なく、リソースは常に適切な形式のトークンを取得できます。 

アクセス トークンを調べる必要があるのはアプリケーションのみです。 クライアントは、それらを **調べることができません**。 コード内で他のアプリのアクセス トークンを調べると、そのアプリによって自身のトークンの形式が変更されたり、暗号化が開始されたりしたときに、アプリが予期せず中断される可能性があります。 

### <a name="error-response-example"></a>エラー応答の例

ダウンストリーム API に条件付きアクセス ポリシー ([多要素認証](../authentication/concept-mfa-howitworks.md)など) が設計されている場合は、ダウンストリーム API へのアクセス トークンを取得しようとすると、トークン エンドポイントによってエラー応答が返されます。 クライアント アプリケーションが条件付きアクセス ポリシーを満たすためのユーザー操作を提供できるように、中間層サービスでこのエラーをクライアント アプリケーションに示す必要があります。

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>アクセス トークンを使用して、セキュリティ保護されたリソースにアクセスする

これで、中間層サービスは上で取得されたトークンを使用して、そのトークンを `Authorization` ヘッダー内に設定することによってダウンストリーム Web API に認証済み要求を発行できます。

### <a name="example"></a>例

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>OAuth2.0 OBO フローにより取得した SAML アサーション

一部の OAuth ベースの Web サービスは、非対話型フローで SAML アサーションを受け入れるその他の Web サービス API にアクセスする必要があります。 Azure Active Directory は、SAML ベースの Web サービスをターゲット リソースとして使用する On-Behalf-Of フローに応答して SAML アサーションを提供できます。

これは、OAuth2 ベースのアプリケーションが SAML トークンを使用する Web サービス API エンドポイントにアクセスできる、OAuth 2.0 の On-Behalf-Of フローの非標準の拡張機能です。

> [!TIP]
> フロントエンド Web アプリケーションから SAML で保護された Web サービスを呼び出す場合、API を呼び出すだけで、ユーザーの既存のセッションで通常の対話型認証フローを開始できます。 サービス間の呼び出しでユーザー コンテキストを提供するために SAML トークンが必要なときのみ OBO フローを使用する必要があります。

## <a name="gaining-consent-for-the-middle-tier-application"></a>中間層アプリケーションの同意の取得

アプリケーションのアーキテクチャまたは使用状況に基づき、OBO フローを確実に成功させるためのさまざまな方法を検討することをお勧めします。 どのような場合でも、最終的な目標は、クライアント アプリから中間層アプリを呼び出すことができ、バックエンド リソースを呼び出すアクセス許可を中間層アプリに持たせるように、適切な同意を与えることです。

> [!NOTE]
> 以前は、Microsoft アカウント システム (個人アカウント) は [Known client application]\(既知のクライアント アプリケーション\) フィールドをサポートしていませんでした。また、組み合わせた同意を表示することもできませんでした。  これが追加されたので、Microsoft ID プラットフォーム上のすべてのアプリは、OBO 呼び出しの同意を得るために既知のクライアント アプリケーションの手法を使用できるようになります。

### <a name="default-and-combined-consent"></a>/.default と組み合わせ同意

中間層アプリケーションは、そのマニフェストの既知のクライアント アプリケーションの一覧にクライアントを追加します。その後、そのクライアントは、それ自体と中間層アプリケーションの両方に対して組み合わせ同意フローをトリガーできます。 Microsoft ID プラットフォームでは、これは [`/.default` スコープ](v2-permissions-and-consent.md#the-default-scope)を使用して行われます。 既知のクライアント アプリケーションと `/.default` を使用して同意画面をトリガーすると、その同意画面には、クライアントと中間層 API の **両方** に対するアクセス許可が表示され、その中間層 API で必要となるすべてのアクセス許可が要求されます。 ユーザーが両方のアプリケーションに対して同意を行うと、OBO フローが動作します。

### <a name="pre-authorized-applications"></a>事前承認済みアプリケーション

特定のアプリケーションが特定のスコープを受け取る許可を常に持つことをリソースで示すことができます。 これは、フロントエンド クライアントとバックエンド リソース間の接続をよりシームレスに行う場合に主に役立ちます。 1 つのリソースで、複数の事前承認済みアプリケーションを宣言できます。このようなアプリケーションは、OBO フローでこれらのアクセス許可を要求し、ユーザーによる同意なしで、それらのアクセス許可を受け取ることができます。

### <a name="admin-consent"></a>管理者の同意

テナント管理者は、中間層アプリケーションに対して管理者同意を提供することで、アプリケーションが必要な API を呼び出すためのアクセス許可を確実に持つようにすることができます。 これを行うために、管理者は、テナントで中間層アプリケーションを見つけ、必要なアクセス許可ページを開き、アプリに対してアクセス許可を付与することを選択できます。 管理者の同意について詳しくは、[同意とアクセス許可のドキュメント](v2-permissions-and-consent.md)に関する記事を参照してください。

### <a name="use-of-a-single-application"></a>単一アプリケーションの使用

一部のシナリオでは、中間層クライアントとフロントエンド クライアントの単一ペアのみ使用する場合があります。 このシナリオでは、これを単一アプリケーションにする方が簡単で、中間層アプリケーションをまったく必要としない場合があります。 フロントエンドと Web API 間で認証を行うために、アプリケーション自体に要求された cookie、id_token、またはアクセス トークンを使用できます。 その後、この単一アプリケーションからバックエンド リソースへの同意を要求します。

## <a name="client-limitations"></a>クライアントの制限事項

クライアントで暗黙的フローを使って id_token を取得する場合、また、応答 URL にワイルドカードが含まれている場合には、id_token を OBO フローで使用することはできません。  ただし、開始元のクライアントが登録済みのワイルドカード応答 URL を持っている場合でも、機密クライアントでは引き続き、暗黙的な付与フローを通じて取得したアクセス トークンを利用することができます。

## <a name="next-steps"></a>次のステップ

OAuth 2.0 プロトコルと、クライアント資格情報を使用したサービス間認証を実行する別の方法について詳しく学びます。

* [Microsoft ID プラットフォームでの OAuth 2.0 クライアント資格情報の付与](v2-oauth2-client-creds-grant-flow.md)
* [Microsoft ID プラットフォームでの OAuth 2.0 コード フロー](v2-oauth2-auth-code-flow.md)
* [`/.default` スコープの使用](v2-permissions-and-consent.md#the-default-scope)
