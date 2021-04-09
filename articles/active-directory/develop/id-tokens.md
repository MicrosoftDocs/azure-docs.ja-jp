---
title: Microsoft ID プラットフォームの ID トークン | Azure
titleSuffix: Microsoft identity platform
description: Azure AD v1.0 および Microsoft ID プラットフォーム (v2.0) のエンドポイントによって生成される id_token の使用方法について説明します。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 21e0b800e06b7a5ad0351ff53d26a5e2cd2ba71c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175390"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft ID プラットフォームの ID トークン

`id_tokens` は、[OpenID Connect](v2-protocols-oidc.md) (OIDC) フローの中でクライアント アプリケーションに送信されます。 これらは、アクセス トークンと一緒に、またはその代わりに送信することができ、ユーザーを認証するためにクライアントによって使用されます。

## <a name="using-the-id_token"></a>id_token を使用する

ID トークンは、ユーザーが本人の主張どおりの人物であることを検証し、ユーザーについてその他の役に立つ情報を取得するために使用されます。[アクセス トークン](access-tokens.md)の代わりに、承認のために使用してはなりません。 これによって提供される要求は、アプリケーション内の UX で[データベースのキー](#using-claims-to-reliably-identify-a-user-subject-and-object-id)として使用でき、クライアント アプリケーションへのアクセスが提供されます。  

## <a name="claims-in-an-id_token"></a>id_token の要求

`id_tokens` は [JWT](https://tools.ietf.org/html/rfc7519) (JSON Web トークン) です。つまり、ヘッダー、ペイロードおよび署名の部分から構成されます。 ヘッダーと署名を使用して、トークンの信頼性を確認できます。ペイロードには、クライアントによって要求されたユーザーの情報が含まれます。 特に明記しない限り、ここに示すすべての JWT 要求は v1.0 と v2.0 両方のトークンに出現します。

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

この v1.0 のサンプル トークンは [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q) で表示できます。

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

この v2.0 のサンプル トークンは [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw) で表示できます。

### <a name="header-claims"></a>ヘッダーのクレーム

|要求 | Format | 説明 |
|-----|--------|-------------|
|`typ` | 文字列 - 常に "JWT" | トークンが JWT トークンであることを示します。|
|`alg` | String | トークンの署名に使用されたアルゴリズムを示します。 例:"RS256" |
|`kid` | String | このトークンの検証に使用される公開キーの拇印。 v1.0 および v2.0 の `id_tokens` で生成されます。 |
|`x5t` | String | `kid` と同様 (使用方法および値)。 ただし、これは、互換性のために v1.0 `id_tokens`でのみ生成される従来の要求です。 |

### <a name="payload-claims"></a>ペイロードのクレーム

この一覧には、(明記されている場合を除き) 既定でほとんどの id_token に含まれる JWT 要求が示されます。  ただし、アプリは、[省略可能な要求](active-directory-optional-claims.md)を使用して、id_token で追加の JWT 要求を要求することができます。  これらは、`groups` 要求から、ユーザーの名前に関する情報にまで及ぶ場合があります。

|要求 | Format | 説明 |
|-----|--------|-------------|
|`aud` |  文字列、アプリケーション ID URI | トークンの受信者を示します。 `id_tokens`では、対象の受信者は Azure portal でアプリに割り当てられたアプリのアプリケーション ID です。 アプリでは、この値を検証し、値が一致しない場合はトークンを拒否する必要があります。 |
|`iss` |  文字列、STS URI | トークンを作成して返したセキュリティ トークン サービス (STS)、およびユーザーが認証された Azure AD テナントを示します。 トークンが v2.0 エンドポイントによって発行された場合、URI の末尾は `/v2.0` になります。  ユーザーが Microsoft アカウントを持つコンシューマー ユーザーであることを示す GUID は `9188040d-6c67-4c5b-b112-36a304b66dad` です。 要求の GUID 部分を使用して、アプリにサインインできるテナントのセットを制限します (該当する場合)。 |
|`iat` |  int、UNIX タイムスタンプ | "Issued At" は、このトークンの認証がいつ行われたのかを示します。  |
|`idp`|文字列 (通常は STS URI) | トークンのサブジェクトを認証した ID プロバイダーを記録します。 この値は、発行者とテナントが異なるユーザー アカウント (たとえばゲスト) の場合を除いて、発行者クレームの値と同じです。 クレームが存在しない場合は、代わりに `iss` の値を使用できることを示しています。  個人用アカウントが組織のコンテキストで使用されている場合 (たとえば、個人用アカウントが Azure AD テナントに招待された場合)、`idp` 要求は 'live.com' または Microsoft アカウント テナント `9188040d-6c67-4c5b-b112-36a304b66dad` を含む STS URI である可能性があります。 |
|`nbf` |  int、UNIX タイムスタンプ | "nbf" (指定時刻よりも後) 要求では、指定した時刻よりも後に JWT の処理を受け入れることができるようになります。|
|`exp` |  int、UNIX タイムスタンプ | "exp" (有効期限) 要求は、JWT の処理を受け入れることができなくなる時刻を指定します。  この時刻よりも前でも、リソースによってトークンが拒否される可能性があることに注意してください。たとえば、認証での変更が必要な場合や、トークンの取り消しが検出された場合です。 |
| `c_hash`| String |コード ハッシュは、ID トークンが OAuth 2.0 認証コードと共に発行される場合にのみ、ID トークンに含まれます。 これを使用して、認証コードの信頼性を検証できます。 この検証の実行の詳細については、[OpenID Connect の仕様](https://openid.net/specs/openid-connect-core-1_0.html) を参照してください。 |
|`at_hash`| String |アクセス トークン ハッシュは、ID トークンが `/authorize` エンドポイントから OAuth 2.0 アクセス トークンと共に発行される場合にのみ、ID トークンに含まれます。 これを使用して、アクセス トークンの信頼性を検証できます。 この検証の実行の詳細については、[OpenID Connect の仕様](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken) を参照してください。 これは、`/token` エンドポイントからの ID トークンでは返されません。 |
|`aio` | 不透明な文字列 | Azure AD がトークン再利用のためにデータの記録に使用する内部の要求。 無視してください。|
|`preferred_username` | String | ユーザーを表すプライマリ ユーザー名です。 電子メール アドレス、電話番号、または指定された書式のない一般的なユーザー名を指定できます。 その値は、変更可能であり、時間の経過と共に変化することがあります。 これは変更可能であるため、この値は、承認の決定には使用できません。 この要求を受け取るには `profile` スコープが必要です。|
|`email` | String | `email` 要求は、電子メール アドレスを持つゲスト アカウントに対して既定で使用されます。  アプリでは、[オプション要求](active-directory-optional-claims.md)である `email` を使用して、管理対象ユーザー (リソースと同じテナントのユーザー) の電子メール要求を要求できます。  v2.0 エンドポイントでは、アプリで `email` OpenID Connect スコープを要求することもできます (要求を取得するためにオプション要求とスコープの両方を要求する必要はありません)。  電子メール要求では、ユーザーのプロファイル情報からアドレス指定可能なメールのみがサポートされます。 |
|`name` | String | `name`要求は、トークンのサブジェクトを識別する、人が認識できる値を示します。 この値は、一意であるとは限らず、変更可能であり、表示目的でのみ使用するように設計されています。 この要求を受け取るには `profile` スコープが必要です。 |
|`nonce`| String | nonce は、IDP に対する元の要求または承認要求に含まれるパラメーターと一致します。 一致しない場合は、アプリケーションによってトークンが拒否されます。 |
|`oid` | 文字列、GUID | Microsoft ID システム (ここではユーザー アカウント) のオブジェクトに対する変更不可の識別子です。 この ID によって、複数のアプリケーションでユーザーが一意に識別されます。同じユーザーにサインインする 2 つの異なるアプリケーションは `oid` 要求で同じ値を受け取ります。 Microsoft Graph は、この ID を、指定されたユーザー アカウントの `id` プロパティとして返します。 `oid` では複数のアプリがユーザーを関連付けることができるため、この要求を受け取るには `profile` スコープが必要です。 1 人のユーザーが複数のテナントに存在する場合、そのユーザーのオブジェクト ID はテナントごとに異なります。つまり、ユーザーが同じ資格情報で各アカウントにログインしても、それらは異なるアカウントと見なされます。 `oid` 要求は GUID であり、再利用することはできません。 |
|`roles`| 文字列の配列 | ログインしているユーザーに割り当てられた一連のロール。 |
|`rh` | 不透明な文字列 |Azure がトークンの再検証に使用する内部要求。 無視してください。 |
|`sub` | String | トークンが情報をアサートするプリンシパルです (アプリのユーザーなど)。 この値は変更不可で、再割り当ても再利用もできません。 サブジェクトはペアワイズ識別子で、特定のアプリケーション ID に一意です。 1 人のユーザーが 2 つの異なるクライアント ID を使用して 2 つの異なるアプリにサインインすると、そのアプリは、サブジェクト要求に対して 2 つの異なる値を受け取ることになります。 この動作が求められているかどうかは、アーキテクチャやプライバシーの要件によって異なります。 |
|`tid` | 文字列、GUID | ユーザーが属している Azure AD テナントを表す GUID です。 職場または学校アカウントの場合、GUID はユーザーが属している組織の不変のテナント ID です。 個人アカウントでは、この値は `9188040d-6c67-4c5b-b112-36a304b66dad` です。 この要求を受け取るには `profile` スコープが必要です。 |
|`unique_name` | String | トークンのサブジェクトを識別する、人が判読できる値を提供します。 この値は特定の時点で一意ですが、電子メールやその他の識別子は再利用される場合があり、この値は他のアカウントで再表示される可能性があるため、表示目的でのみ使用してください。 v1.0 `id_tokens` のみで発行されます。 |
|`uti` | 不透明な文字列 | Azure がトークンの再検証に使用する内部要求。 無視してください。 |
|`ver` | 文字列、1.0 または 2.0 | id_token のバージョンを示します。 |
|`hasgroups`|Boolean|存在する場合、常に true であり、ユーザーが 1 つ以上のグループに属していることを示します。 すべてのグループ要求で URL 長の制限 (現在は 6 以上のグループ) を超えて URI フラグメントが拡張された場合、暗黙的な許可フローの JWT で groups 要求の代わりに使用されます。 クライアントが Microsoft Graph API を使用して、ユーザーのグループを決定する必要があることを示します (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`)。|
|`groups:src1`|JSON オブジェクト | 長さは制限されていないが (上記 `hasgroups` を参照)、トークンには大きすぎるトークン要求の場合、ユーザーのすべてのグループ リストへのリンクが含まれます。 SAML では `groups` 要求の代わりに新しい要求として、JWT では分散要求として使用されます。 <br><br>**JWT 値の例**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> 詳細については、「[グループ超過要求](#groups-overage-claim)」を参照してください。|

> [!NOTE]
> v1.0 と v2.0 の id_token は、上記の例に示すように、含まれる情報の量に違いがあります。 バージョンは、要求元のエンドポイントに基づきます。 既存のアプリケーションでは Azure AD エンドポイントを使用する場合が多いですが、新しいアプリケーションでは "Microsoft ID プラットフォーム" を使用する必要があります。
>
> - v1.0:Azure AD エンドポイント: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v2.0:Microsoft ID プラットフォーム エンドポイント: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>要求を使用してユーザーを確実に識別する (サブジェクトとオブジェクト ID)

ユーザーを識別する (たとえば、データベース内で検索したり、ユーザーが持つアクセス許可を決定したりする) 場合は、時間が経過しても一定で一意の情報を使用することが重要です。 レガシ アプリケーションでは、メール アドレス、電話番号、UPN などのフィールドが使われることがあります。  これらはすべて時間の経過と共に変わることがあり、時間の経過と共に再利用されることもあります。従業員の名前が変わったり、または従業員に以前の存在しなくなった従業員のメール アドレスに一致するアドレスが与えられたりする場合などです。 このため、アプリケーションでユーザーを識別するために、人間が判読できるデータを使用しないことが **重要** です。人間が判読可能であるとは、一般にだれかがそれを読んで、変更したくなることを意味します。 代わりに、OIDC 標準によって提供される要求、または Microsoft によって提供される拡張要求 (`sub` および `oid` 要求) を使用します。

ユーザーごとに情報を正しく格納するには、`sub` または `oid` を単独で使用し (GUID は一意であるため)、必要に応じて `tid` をルーティングまたはシャーディングに使用します。  サービス間でデータを共有する必要がある場合は、すべてのアプリで、特定のユーザーに対して同じ `oid` と `tid` 要求が取得されるため、`oid`+`tid` が最適です。  Microsoft ID プラットフォームの `sub` 要求は "ペア" になっています。これは、トークンの受信者、テナント、ユーザーの組み合わせに基づいて一意です。  したがって、特定のユーザーに対して ID トークンを要求する 2 つのアプリは、`sub` 要求は異なっていても、そのユーザーに対して同じ `oid` 要求を受け取ることになります。

>[!NOTE]
> テナント間でユーザーを関連付けようとして、`idp` 要求を使用して、ユーザーに関する情報を格納しないでください。  これは機能しません。ユーザーの `oid` および `sub` 要求は、設計によってテナント間で変わり、アプリケーションで、テナントを越えてユーザーを追跡できないようにしているためです。  
>
> ユーザーが 1 つのテナントに所属し、別のテナントで認証するゲスト シナリオでは、ユーザーがサービスに対して新しいユーザーであるかのように、ユーザーを処理する必要があります。  Contoso テナントのドキュメントと特権は、Fabrikam テナントでは適用できません。 これは、テナント間での予期しないデータの漏洩を防ぐために重要です。

### <a name="groups-overage-claim"></a>グループ超過要求
トークンのサイズが HTTP ヘッダー サイズの上限を超えないよう、Azure AD では、`groups` 要求に含まれるオブジェクト ID の数が制限されます。 超過制限 (SAML トークンの場合は 150、JWT トークンの場合は 200) を超えるグループのメンバーにユーザーがなっている場合、Azure AD は、グループ要求をトークンに出力しません。 代わりに、Microsoft Graph API に照会してユーザーのグループ メンバーシップを取得するようアプリケーションに指示する超過要求がトークンに追加されます。

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="validating-an-id_token"></a>id_token の検証

`id_token` の検証は、[アクセス トークンの検証](access-tokens.md#validating-tokens)の最初の手順と似ています。クライアントは、正しい発行者がトークンを返送したことと、改ざんされていないことを検証できます。 `id_tokens` は常に JWT トークンであるため、トークンを検証する多くのライブラリが存在しています。みずから検証するのではなく、これらのライブラリのいずれかを使用することをお勧めします。  Confidential クライアント (シークレットを持つクライアント) のみが ID トークンを検証すべきであることに注意してください。  パブリック アプリケーション (ユーザーのブラウザーやホーム ネットワークなど、制御できないデバイスまたはネットワークで完全に実行されるコード) は、ID トークンの検証から利点が得られません。これは、悪意のあるユーザーが、トークンの検証に使用するキーをインターセプトして編集できるためです。

トークンを手動で検証するには、[アクセス トークンを検証](access-tokens.md#validating-tokens)する手順の詳細をご覧ください。 トークンの署名を検証した後で、id_token 内の次の JWT 要求を検証します (トークン検証ライブラリではこれも行われる場合があります)。

* タイムスタンプ: `iat`、`nbf`、`exp` の各タイムスタンプがすべて、現在の時刻の前か後であることが必要です (該当する場合)。
* 対象: `aud` 要求がアプリケーションのアプリ ID と一致する必要があります。
* nonce: ペイロードの `nonce` 要求が、最初の要求で /authorize エンドポイントに渡された nonce パラメーターと一致する必要があります。

## <a name="next-steps"></a>次のステップ

* [アクセス トークン](access-tokens.md)の詳細情報
* [省略可能な要求](active-directory-optional-claims.md)を使用して id_token の JWT 要求をカスタマイズする
