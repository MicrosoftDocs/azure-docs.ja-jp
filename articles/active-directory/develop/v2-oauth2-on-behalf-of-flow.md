---
title: Microsoft ID プラットフォームと OAuth2.0 On-Behalf-Of フロー | Azure
description: この記事では、HTTP メッセージを使用して、OAuth2.0 On-Behalf-Of フローを使用するサービス間の認証を実装する方法について説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7582cd8453b25f071c18566f09d2155a6377a0a6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482163"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft ID プラットフォームと OAuth2.0 On-Behalf-Of フロー

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

OAuth 2.0 の On-Behalf-Of (OBO) フローは、アプリケーションがサービス/Web API を呼び出し、それがさらに別のサービス/Web API を呼び出す必要のあるユース ケースを提供します。 その考え方は、委任されたユーザー ID とアクセス許可を要求チェーン経由で伝達するというものです。 中間層サービスがダウンストリーム サービスに認証済み要求を発行するには、そのサービスは Microsoft ID プラットフォームからのアクセス トークンをユーザーに代わってセキュリティ保護する必要があります。

> [!NOTE]
>
> - Microsoft ID プラットフォームのエンドポイントでは、すべてのシナリオや機能がサポートされているわけではありません。 Microsoft ID プラットフォームのエンドポイントを使用する必要があるかどうかを判断するには、[Microsoft ID プラットフォームの制限事項](active-directory-v2-limitations.md)に関する記事を参照してください。 特に、既知のクライアント アプリケーションは、Microsoft アカウント (MSA) と Azure AD の対象ユーザーを含むアプリではサポートされていません。 したがって、OBO の一般的な同意パターンは、個人アカウントと職場または学校のアカウントの両方にサインインするクライアントに対しては機能しません。 フローのこのステップを処理する方法について詳しくは、「[中間層アプリケーションの同意の取得](#gaining-consent-for-the-middle-tier-application)」を参照してください。
> - 2018 年 5 月の時点では、暗黙的なフローから派生する一部の `id_token` は、OBO フローで使用できません。 シングルページ アプリ (SPA) では、中間層の機密クライアントに**アクセス** トークンを渡して、OBO フローを代わりに実行する必要があります。 OBO 呼び出しを実行できるクライアントの詳細については、[制限事項](#client-limitations)に関する記事を参照してください。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

[OAuth 2.0 認証コード付与フロー](v2-oauth2-auth-code-flow.md)を使用するアプリケーションで、ユーザーが認証されているとします。 この時点で、そのアプリケーションには、中間層 Web API (API A) にアクセスするためのユーザーの要求と同意を含む *API A の*アクセス トークン (トークン A) があります。 ここで、API A はダウンストリーム Web API (API B) に認証済み要求を発行する必要があります。

以降の手順は OBO フローを構成するものであり、次の図を使用して説明します。

![OAuth2.0 の On-Behalf-Of フローを示します](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. クライアント アプリケーションは、トークン A (API A の `aud` 要求) を使用して API A に要求を発行します。
1. API A が Microsoft ID プラットフォーム トークン発行エンドポイントに対して認証を行い、API B にアクセスするためのトークンを要求します。
1. Microsoft ID プラットフォーム トークン発行エンドポイントはトークン A を使用して API A の資格情報を検証し、API B (トークン B) へのアクセス トークンを発行します。
1. API B への要求の承認ヘッダー内にトークン B が設定されます。
1. セキュリティで保護されたリソースからのデータが API B によって返されます。

> [!NOTE]
> このシナリオでは、中間層サービスに、ダウンストリーム API にアクセスするユーザーの同意を得るためのユーザー操作はありません。 そのため、ダウンストリーム API へのアクセス権を付与するオプションは、認証中の同意手順の一部として事前に提供されます。 ご使用のアプリに対してこれを設定する方法について詳しくは、「[中間層アプリケーションの同意の取得](#gaining-consent-for-the-middle-tier-application)」を参照してください。

## <a name="service-to-service-access-token-request"></a>サービス間のアクセス トークン要求

アクセス トークンを要求するには、次のパラメーターを使用して、テナントに固有の Microsoft ID プラットフォーム トークン エンドポイントへの HTTP POST を作成します。

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

クライアント アプリケーションのセキュリティ保護に共有シークレットまたは証明書のどちらを使うかに応じて、2 つのケースがあります。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>最初のケース:共有シークレットを使ったアクセス トークン要求

共有シークレットを使用する場合、サービス間のアクセス トークン要求には、次のパラメーターが含まれてます。

| パラメーター |  | 説明 |
| --- | --- | --- |
| `grant_type` | 必須 | トークン要求の種類。 JWT を使用した要求では、この値は `urn:ietf:params:oauth:grant-type:jwt-bearer` にする必要があります。 |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページで、アプリに割り当てられたアプリケーション (クライアント) ID。 |
| `client_secret` | 必須 | Azure portal の [アプリの登録] ページで、アプリ用に生成したクライアント シークレット。 |
| `assertion` | 必須 | 要求で使用されるトークンの値。 |
| `scope` | 必須 | トークン要求のスコープのスペース区切りリスト。 詳細については、「[スコープ](v2-permissions-and-consent.md)」を参照してください。 |
| `requested_token_use` | 必須 | 要求の処理方法を指定します。 OBO フローでは、この値は `on_behalf_of` に設定する必要があります。 |

#### <a name="example"></a>例

次の HTTP POST は、 https://graph.microsoft.com Web API に対する `user.read` スコープを含むアクセス トークンと更新トークンを要求します。

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>2 番目のケース:証明書を使ったアクセス トークン要求

証明書を含むサービス間のアクセス トークン要求には、次のパラメーターが含まれています。

| パラメーター |  | 説明 |
| --- | --- | --- |
| `grant_type` | 必須 | トークン要求の種類。 JWT を使用した要求では、この値は `urn:ietf:params:oauth:grant-type:jwt-bearer` にする必要があります。 |
| `client_id` | 必須 |  [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページで、アプリに割り当てられたアプリケーション (クライアント) ID。 |
| `client_assertion_type` | 必須 | 値は `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` である必要があります。 |
| `client_assertion` | 必須 | 作成する必要があるアサーション (JSON Web トークン) です。このアサーションは、アプリケーションの資格情報として登録した証明書で署名する必要があります。 証明書の登録方法とアサーションの形式の詳細については、[証明書資格情報](active-directory-certificate-credentials.md)に関する記事を参照してください。 |
| `assertion` | 必須 | 要求で使用されるトークンの値。 |
| `requested_token_use` | 必須 | 要求の処理方法を指定します。 OBO フローでは、この値は `on_behalf_of` に設定する必要があります。 |
| `scope` | 必須 | トークン要求のスコープのスペース区切りリスト。 詳細については、「[スコープ](v2-permissions-and-consent.md)」を参照してください。|

パラメーターは、共有シークレットによる要求のパラメーターとほぼ同じであることに注意してください。唯一異なるのは、`client_secret` パラメーターが、`client_assertion_type` と `client_assertion` の 2 つのパラメーターに置き換えられている点です。

#### <a name="example"></a>例

次の HTTP POST は、証明書を使用して https://graph.microsoft.com Web API に対する `user.read` スコープを含むアクセス トークンを要求します。

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>サービス間のアクセス トークン応答

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

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> 上記のアクセス トークンは v1.0 でフォーマットされたトークンです。 これは、アクセス対象のリソースに基づいて、トークンが提供されるためです。 Microsoft Graph は v1.0 トークンを要求するため、クライアントが Microsoft Graph のトークンを要求すると、Microsoft ID プラットフォームによって v1.0 アクセス トークンが生成されます。 アクセス トークンを調べる必要があるのはアプリケーションのみです。 クライアントが調べる必要はありません。

### <a name="error-response-example"></a>エラー応答の例

ダウンストリーム API に条件付きアクセス ポリシー (多要素認証など) が設計されている場合は、ダウンストリーム API へのアクセス トークンを取得しようとすると、トークン エンドポイントによってエラー応答が返されます。 クライアント アプリケーションが条件付きアクセス ポリシーを満たすためのユーザー操作を提供できるように、中間層サービスでこのエラーをクライアント アプリケーションに示す必要があります。

```
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

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>中間層アプリケーションの同意の取得

アプリケーションの対象ユーザーに基づき、OBO フローを確実に成功させるためのさまざまな方法を検討することをお勧めします。 すべての場合において、最終的な目標は適切な同意が得られるようにすることです。 ただし、それがどのように行われるかは、アプリケーションがサポートしているユーザーによって異なります。

### <a name="consent-for-azure-ad-only-applications"></a>Azure AD 専用アプリケーションの同意

#### <a name="default-and-combined-consent"></a>/.default と組み合わせ同意

職場または学校のアカウントにのみサインインする必要のあるアプリケーションの場合、従来の "既知のクライアント アプリケーション" のアプローチで十分です。 中間層アプリケーションは、そのマニフェストの既知のクライアント アプリケーションの一覧にクライアントを追加します。その後、そのクライアントは、それ自体と中間層アプリケーションの両方に対して組み合わせ同意フローをトリガーできます。 Microsoft ID プラットフォームのエンドポイント上では、これは[`/.default`スコープ](v2-permissions-and-consent.md#the-default-scope)を使用して行われます。 既知のクライアント アプリケーションと `/.default` を使用して同意画面をトリガーすると、その同意画面には、クライアントと中間層 API の両方に対するアクセス許可が表示され、その中間層 API で必要となるすべてのアクセス許可が要求されます。 ユーザーが両方のアプリケーションに対して同意を行うと、OBO フローが動作します。

現時点で、Microsoft 個人アカウント システムは、組み合わせ同意をサポートしていません。そのため、このアプローチは、特に個人アカウントにサインインするアプリに対しては機能しません。 テナントでゲスト アカウントとして使用される Microsoft 個人アカウントは、Azure AD システムを使用して処理され、組み合わせ同意で処理できます。

#### <a name="pre-authorized-applications"></a>事前承認済みアプリケーション

アプリケーション ポータルの 1 つの機能に、"事前承認済みアプリケーション" があります。 これにより、特定のアプリケーションが特定のスコープを受け取る許可を常に持つことをリソースで示すことができます。 これは、フロントエンド クライアントとバックエンド リソース間の接続をよりシームレスに行う場合に主に役立ちます。 1 つのリソースで、複数の事前承認済みアプリケーションを宣言できます。このようなアプリケーションは、OBO フローでこれらのアクセス許可を要求し、ユーザーによる同意なしで、それらのアクセス許可を受け取ることができます。

#### <a name="admin-consent"></a>管理者の同意

テナント管理者は、中間層アプリケーションに対して管理者同意を提供することで、アプリケーションが必要な API を呼び出すためのアクセス許可を確実に持つようにすることができます。 これを行うために、管理者は、テナントで中間層アプリケーションを見つけ、必要なアクセス許可ページを開き、アプリに対してアクセス許可を付与することを選択できます。 管理者の同意について詳しくは、[同意とアクセス許可のドキュメント](v2-permissions-and-consent.md)に関する記事を参照してください。

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Azure AD と Microsoft アカウント アプリケーションの同意

個人アカウントのアクセス許可モデルでの制限と、管理テナントの欠落により、個人アカウントの同意要件は、Azure AD とは少し異なります。 テナント全体にわたる同意を提供するテナントは存在しません。また、組み合わせ同意を行う機能もありません。 したがって、他の方法があります。それらは、Azure AD アカウントのサポートのみが求められるアプリケーションにも有効であることに留意してください。

#### <a name="use-of-a-single-application"></a>単一アプリケーションの使用

一部のシナリオでは、中間層クライアントとフロントエンド クライアントの単一ペアのみ使用する場合があります。 このシナリオでは、これを単一アプリケーションにする方が簡単で、中間層アプリケーションをまったく必要としない場合があります。 フロントエンドと Web API 間で認証を行うために、アプリケーション自体に要求された cookie、id_token、またはアクセス トークンを使用できます。 その後、この単一アプリケーションからバックエンド リソースへの同意を要求します。

## <a name="client-limitations"></a>クライアントの制限事項

クライアントで暗黙的フローを使って id_token を取得する場合、また、応答 URL にワイルドカードが含まれている場合には、id_token を OBO フローで使用することはできません。  ただし、開始元のクライアントが登録済みのワイルドカード応答 URL を持っている場合でも、機密クライアントでは引き続き、暗黙的な付与フローを通じて取得したアクセス トークンを利用することができます。

## <a name="next-steps"></a>次の手順

OAuth 2.0 プロトコルと、クライアント資格情報を使用したサービス間認証を実行する別の方法について詳しく学びます。

* [Microsoft ID プラットフォームでの OAuth 2.0 クライアント資格情報の付与](v2-oauth2-client-creds-grant-flow.md)
* [Microsoft ID プラットフォームでの OAuth 2.0 コード フロー](v2-oauth2-auth-code-flow.md)
* [`/.default` スコープの使用](v2-permissions-and-consent.md#the-default-scope)
