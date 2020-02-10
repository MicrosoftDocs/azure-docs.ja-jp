---
title: Microsoft ID プラットフォームの ID トークン リファレンス | Microsoft Docs
description: Azure AD v1.0 および Microsoft ID プラットフォーム (v2.0) のエンドポイントによって生成される id_token の使用方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 912287200097906af7a8a9d6d12eb1421f3edadc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696760"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft ID プラットフォームの ID トークン

`id_tokens` は、[OpenID Connect](v1-protocols-openid-connect-code.md) フローの中でクライアント アプリケーションに送信されます。 これは、アクセス トークンと一緒に、またはアクセス トークンのかわりに送信することができ、ユーザーを認証するためにクライアントによって使用されます。

## <a name="using-the-id_token"></a>id_token を使用する

ID トークンは、ユーザーが本人の主張どおりの人物であることを検証し、ユーザーについてその他の役に立つ情報を取得するために使用されます。[アクセス トークン](access-tokens.md)の代わりに、承認のために使用してはなりません。 これによって提供される要求は、アプリケーション内の UX でデータベースのキーとして使用でき、クライアント アプリケーションへのアクセスが提供されます。  データベースのキーを作成するときは、ゲスト シナリオが乱雑になるため、`idp` は使用しないでください。  キーの処理は `sub` (常に一意) のみで実行する必要があり、必要に応じてルーティングのために `tid` が使用されます。  サービス間でデータを共有する必要がある場合、複数のサービスで同じ `oid` が取得されるため、`oid`+`sub`+`tid` で機能します。

## <a name="claims-in-an-id_token"></a>id_token の要求

Microsoft Identity の`id_tokens` は [JWT](https://tools.ietf.org/html/rfc7519) です。つまり、ヘッダー、ペイロードおよび署名の部分から構成されます。 ヘッダーと署名を使用して、トークンの信頼性を確認できます。ペイロードには、クライアントによって要求されたユーザーの情報が含まれます。 特に明記しない限り、ここに示すすべての要求は v1.0 と v2.0 両方のトークンに出現します。

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
|`typ` | 文字列 - 常に "JWT" | トークンが JWT であることを示します。|
|`alg` | String | トークンの署名に使用されたアルゴリズムを示します。 例:"RS256" |
|`kid` | String | このトークンの署名に使用される公開キーの拇印。 v1.0 および v2.0 の `id_tokens` で生成されます。 |
|`x5t` | String | `kid` と同様 (使用方法および値)。 ただし、これは、互換性のために v1.0 `id_tokens`でのみ生成される従来の要求です。 |

### <a name="payload-claims"></a>ペイロードのクレーム

この一覧には、(明記されている場合を除き) 既定でほとんどの id_token に含まれる要求が示されます。  ただし、アプリは、[省略可能な要求](active-directory-optional-claims.md)を使用して、id_token で追加の要求を要求することができます。  これらは、`groups` 要求から、ユーザーの名前に関する情報にまで及ぶ場合があります。

|要求 | Format | 説明 |
|-----|--------|-------------|
|`aud` |  文字列、アプリケーション ID URI | トークンの受信者を示します。 `id_tokens`では、対象の受信者は Azure portal でアプリに割り当てられたアプリのアプリケーション ID です。 アプリでは、この値を検証し、値が一致しない場合はトークンを拒否する必要があります。 |
|`iss` |  文字列、STS URI | トークンを作成して返したセキュリティ トークン サービス (STS)、およびユーザーが認証された Azure AD テナントを示します。 トークンが v2.0 エンドポイントによって発行された場合、URI の末尾は `/v2.0` になります。  ユーザーが Microsoft アカウントを持つコンシューマー ユーザーであることを示す GUID は `9188040d-6c67-4c5b-b112-36a304b66dad` です。 要求の GUID 部分を使用して、アプリにサインインできるテナントのセットを制限します (該当する場合)。 |
|`iat` |  int、UNIX タイムスタンプ | "Issued At" は、このトークンの認証がいつ行われたのかを示します。  |
|`idp`|文字列 (通常は STS URI) | トークンのサブジェクトを認証した ID プロバイダーを記録します。 この値は、発行者とテナントが異なるユーザー アカウント (たとえばゲスト) の場合を除いて、発行者クレームの値と同じです。 クレームが存在しない場合は、代わりに `iss` の値を使用できることを示しています。  個人用アカウントが組織のコンテキストで使用されている場合 (たとえば、個人用アカウントが Azure AD テナントに招待された場合)、`idp` 要求は 'live.com' または Microsoft アカウント テナント `9188040d-6c67-4c5b-b112-36a304b66dad` を含む STS URI である可能性があります。 |
|`nbf` |  int、UNIX タイムスタンプ | "nbf" (指定時刻よりも後) 要求では、指定した時刻よりも後に JWT の処理を受け入れることができるようになります。|
|`exp` |  int、UNIX タイムスタンプ | "exp" (有効期限) 要求は、JWT の処理を受け入れることができなくなる時刻を指定します。  この時刻よりも前でも、リソースによってトークンが拒否される可能性があることに注意してください。たとえば、認証での変更が必要な場合や、トークンの取り消しが検出された場合です。 |
| `c_hash`| String |コード ハッシュは、ID トークンが OAuth 2.0 認証コードと共に発行される場合にのみ、ID トークンに含まれます。 これを使用して、認証コードの信頼性を検証できます。 この検証の実行の詳細については、[OpenID Connect の仕様](https://openid.net/specs/openid-connect-core-1_0.html) を参照してください。 |
|`at_hash`| String |アクセス トークン ハッシュは、ID トークンが OAuth 2.0 アクセス トークンと共に発行される場合にのみ、ID トークンに含まれます。 これを使用して、アクセス トークンの信頼性を検証できます。 この検証の実行の詳細については、[OpenID Connect の仕様](https://openid.net/specs/openid-connect-core-1_0.html) を参照してください。 |
|`aio` | 不透明な文字列 | Azure AD がトークン再利用のためにデータの記録に使用する内部の要求。 無視してください。|
|`preferred_username` | String | ユーザーを表すプライマリ ユーザー名です。 電子メール アドレス、電話番号、または指定された書式のない一般的なユーザー名を指定できます。 その値は、変更可能であり、時間の経過と共に変化することがあります。 これは変更可能であるため、この値は、承認の決定には使用できません。 この要求を受け取るには `profile` スコープが必要です。|
|`email` | String | `email` 要求は、電子メール アドレスを持つゲスト アカウントに対して既定で使用されます。  アプリでは、[オプション要求](active-directory-optional-claims.md)である `email` を使用して、管理対象ユーザー (リソースと同じテナントのユーザー) の電子メール要求を要求できます。  v2.0 エンドポイントでは、アプリで `email` OpenID Connect スコープを要求することもできます (要求を取得するためにオプション要求とスコープの両方を要求する必要はありません)。  電子メール要求では、ユーザーのプロファイル情報からアドレス指定可能なメールのみがサポートされます。 |
|`name` | String | `name`要求は、トークンのサブジェクトを識別する、人が認識できる値を示します。 この値は、一意であるとは限らず、変更可能であり、表示目的でのみ使用するように設計されています。 この要求を受け取るには `profile` スコープが必要です。 |
|`nonce`| String | nonce は、IDP に対する元の要求または承認要求に含まれるパラメーターと一致します。 一致しない場合は、アプリケーションによってトークンが拒否されます。 |
|`oid` | 文字列、GUID | Microsoft ID システム (ここではユーザー アカウント) のオブジェクトに対する変更不可の識別子です。 この ID によって、複数のアプリケーションでユーザーが一意に識別されます。同じユーザーにサインインする 2 つの異なるアプリケーションは `oid` 要求で同じ値を受け取ります。 Microsoft Graph は、この ID を、指定されたユーザー アカウントの `id` プロパティとして返します。 `oid` では複数のアプリがユーザーを関連付けることができるため、この要求を受け取るには `profile` スコープが必要です。 1 人のユーザーが複数のテナントに存在する場合、そのユーザーのオブジェクト ID はテナントごとに異なります。つまり、ユーザーが同じ資格情報で各アカウントにログインしても、それらは異なるアカウントと見なされます。 `oid` 要求は GUID であり、再利用することはできません。 |
|`roles`| 文字列の配列 | ログインしているユーザーに割り当てられた一連のロール。 |
|`rh` | 不透明な文字列 |Azure がトークンの再検証に使用する内部要求。 無視してください。 |
|`sub` | 文字列、GUID | トークンが情報をアサートするプリンシパルです (アプリのユーザーなど)。 この値は変更不可で、再割り当ても再利用もできません。 サブジェクトはペアワイズ識別子で、特定のアプリケーション ID に一意です。 1 人のユーザーが 2 つの異なるクライアント ID を使用して 2 つの異なるアプリにサインインすると、そのアプリは、サブジェクト要求に対して 2 つの異なる値を受け取ることになります。 この動作が求められているかどうかは、アーキテクチャやプライバシーの要件によって異なります。 |
|`tid` | 文字列、GUID | ユーザーが属している Azure AD テナントを表す GUID です。 職場または学校アカウントの場合、GUID はユーザーが属している組織の不変のテナント ID です。 個人アカウントでは、この値は `9188040d-6c67-4c5b-b112-36a304b66dad` です。 この要求を受け取るには `profile` スコープが必要です。 |
|`unique_name` | String | トークンのサブジェクトを識別する、人が判読できる値を提供します。 この値は特定の時点で一意ですが、電子メールやその他の識別子は再利用される場合があり、この値は他のアカウントで再表示される可能性があるため、表示目的でのみ使用してください。 v1.0 `id_tokens` のみで発行されます。 |
|`uti` | 不透明な文字列 | Azure がトークンの再検証に使用する内部要求。 無視してください。 |
|`ver` | 文字列、1.0 または 2.0 | id_token のバージョンを示します。 |


> [!NOTE]
> v1 と v2 の id_token は、上記の例に示すように、含まれる情報の量に違いがあります。 基本的に、バージョンによって発行元の Azure AD プラットフォーム エンドポイントが指定されます。 [Azure AD の OAuth 実装](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform)は長年にわたって進化してきました。 現在、AzureAD アプリケーション用に 2 つの異なる OAuth エンドポイントがあります。 v2 として分類されている新しいエンドポイントを使用することも、v1 と呼ばれる古いエンドポイントを使用することもできます。 これらの両方で OAuth エンドポイントは異なります。 v2 エンドポイントは、v1 エンドポイントのすべての機能が移行されようとしている新しいエンドポイントであり、新たな開発者の方は v2 エンドポイントを使用することをお勧めします。 
> - V1: Azure Active Directory エンドポイント: `https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Microsoft ID プラットフォーム エンドポイント: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>id_token の検証

`id_token` の検証は、[アクセス トークンの検証](access-tokens.md#validating-tokens)の最初の手順と似ています。クライアントは、正しい発行者がトークンを返送したことと、改ざんされていないことを検証する必要があります。 `id_tokens` は常に JWT であるため、トークンを検証する多くのライブラリが存在しています。みずから検証するのではなく、これらのライブラリのいずれかを使用することをお勧めします。

トークンを手動で検証するには、[アクセス トークンを検証](access-tokens.md#validating-tokens)する手順の詳細をご覧ください。 トークンの署名を検証した後で、id_token 内の次の要求を検証します (トークン検証ライブラリではこれも行われる場合があります)。

* タイムスタンプ: `iat`、`nbf`、`exp` の各タイムスタンプがすべて、現在の時刻の前か後であることが必要です (該当する場合)。 
* 対象: `aud` 要求がアプリケーションのアプリ ID と一致する必要があります。
* nonce: ペイロードの `nonce` 要求が、最初の要求で /authorize エンドポイントに渡された nonce パラメーターと一致する必要があります。

## <a name="next-steps"></a>次のステップ

* [アクセス トークン](access-tokens.md)の詳細情報
* [省略可能な要求](active-directory-optional-claims.md)を使用して id_token の要求をカスタマイズする
