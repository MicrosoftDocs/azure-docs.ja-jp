---
title: Microsoft ID プラットフォーム アクセス トークン リファレンス | Azure
description: Azure AD v1.0 および Microsoft ID プラットフォーム (v2.0) エンドポイントによって出力されるアクセス トークンについて説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 355e61fdfd9847e54a4bd13ac3b0f2d416c05812
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111957"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft ID プラットフォーム アクセス トークン

アクセス トークンにより、クライアントは Azure によって保護された API を安全に呼び出すことができます。 Microsoft ID プラットフォーム アクセス トークンは [JWT](https://tools.ietf.org/html/rfc7519)、つまり Azure によって署名された Base64 でエンコードされた JSON オブジェクトです。 アクセス トークンの内容は特定のリソースのみを対象としているため、クライアントはトークンを不透明型の文字列として扱う必要があります。 検証とデバッグを目的として、開発者は [jwt.ms](https://jwt.ms) などのサイトを使用して、JWT をデコードすることができます。 クライアントは、さまざまなプロトコルを使用して、v1.0 エンドポイントまたは v2.0 エンドポイントのどちらからでもアクセス トークンを取得できます。

クライアントがアクセス トークンを要求すると、Azure AD も、アプリで消費されるそのアクセス トークンに関する何らかのメタデータを返します。 この情報には、アクセス トークンの有効期限や、それが有効なスコープが含まれます。 このデータを使用すると、アプリはアクセス トークン自体を解析しなくても、そのアクセス トークンのインテリジェントなキャッシュを実行できます。

ご自分のアプリケーションが、クライアントからアクセス要求が可能なリソース (Web API) である場合、アクセス トークンを使って、認証と承認に使用する有用な情報 (ユーザー、クライアント、発行元、アクセス許可など) を提供できます。

リソースでアクセス トークン内のクレームを検証して使用する方法については、以下のセクションをご覧ください。

> [!IMPORTANT]
> アクセス トークンは、トークンの*対象ユーザー*、つまりそのトークン内のスコープを所有するアプリケーションに基づいて作成されます。  これは、[アプリ マニフェスト](reference-app-manifest.md#manifest-reference)内の `accessTokenAcceptedVersion` を `2` に設定しているリソースにより、v1.0 エンドポイントを呼び出しているクライアントがどのように v2.0 アクセス トークンを受信できるかを示しています。  同様に、これはクライアントのアクセス トークンの[省略可能な要求](active-directory-optional-claims.md)を変更しても、MS Graph リソースによって所有される `user.read` に対してトークンが要求されたときに受信されるアクセス トークンが変更されない理由でもあります。  
> 同じ理由で、個人アカウント (hotmail.com や outlook.com など) によるクライアント アプリケーションをテストしたときに、クライアントが受け取ったアクセス トークンが不透明型の文字列であることがわかります。 これは、アクセス対象のリソースが、暗号化されていてクライアントが認識できない従来の MSA (Microsoft アカウント) チケットを要求したためです。

## <a name="sample-tokens"></a>サンプル トークン

v1.0 トークンと v2.0 トークンは似ており、同じクレームが多く含まれています。 各トークンの例を次に示します。

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd
```

この v1.0 トークンは [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd) で表示できます。

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

この v2.0 トークンは [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt) で表示できます。

## <a name="claims-in-access-tokens"></a>アクセス トークン内のクレーム

JWT は次の 3 つの部分に分かれています。

* **ヘッダー** - トークンの種類や署名方法に関する情報など、[トークンの検証](#validating-tokens)方法に関する情報が提供されます。
* **ペイロード** - サービスを呼び出そうとしているユーザーまたはアプリに関する重要なデータがすべて含まれています。
* **署名** - トークンの検証に使用される原材料です。

各部分はピリオド (`.`) で区切られ、Base64 で個別にエンコードされます。

クレームは、そこに入力される値が存在する場合にのみ存在します。 そのため、アプリはクレームの存在に依存することはできません。 例として、`pwd_exp` (すべてのテナントがパスワードを期限切れにする必要があるわけではありません) や、`family_name` (名前のないアプリケーションに代わって、[クライアント資格情報フロー](v1-oauth2-client-creds-grant-flow.md)が使用されます) などがあります。 アクセス トークンの検証に使用されるクレームは常に存在します。

> [!NOTE]
> 再利用に備え、Azure AD を使ったトークンのセキュリティ保護に活用されるクレームもあります。 これらは公開されないものとして、記述で "Opaque" としてマークされます。 これらのクレームはトークンに表示される場合とされない場合があり、新しいものが予告なく追加される場合もあります。

### <a name="header-claims"></a>ヘッダーのクレーム

|要求 | 形式 | 説明 |
|--------|--------|-------------|
| `typ` | 文字列 - 常に "JWT" | トークンが JWT であることを示します。|
| `nonce` | String | トークン リプレイ攻撃から保護するために使用される一意識別子。 リソースでこの値を記録することで、再生を防ぐことができます。 |
| `alg` | String | トークンの署名に使用されたアルゴリズム ("RS256" など) を示します。 |
| `kid` | String | このトークンの署名に使用されている公開キーの拇印が記述されています。 v1.0 と v2.0 のどちらのアクセス トークンでも生成されます。 |
| `x5t` | String | `kid` と同様に機能します (使用方法も値も同じ)。 `x5t` は、互換性を目的として v1.0 アクセス トークンでのみ生成されるレガシ クレームです。 |

### <a name="payload-claims"></a>ペイロードのクレーム

| 要求 | 形式 | 説明 |
|-----|--------|-------------|
| `aud` | 文字列、アプリケーション ID/URI | トークンの受信者を示します。 アクセス トークンでは、オーディエンスは Azure portal でアプリに割り当てられたアプリのアプリケーション ID です。 アプリでは、この値を検証し、値が一致しない場合はトークンを拒否する必要があります。 |
| `iss` | 文字列、STS URI | トークンを作成して返したセキュリティ トークン サービス (STS)、およびユーザーが認証された Azure AD テナントを示します。 発行されたトークンが v2.0 トークンである場合 (`ver` 要求を参照)、URI は `/v2.0` で終了します。 ユーザーが Microsoft アカウントを持つコンシューマー ユーザーであることを示す GUID は `9188040d-6c67-4c5b-b112-36a304b66dad` です。 要求の GUID 部分を使用して、アプリにサインインできるテナントのセットを制限します (該当する場合)。 |
|`idp`| 文字列 (通常は STS URI) | トークンのサブジェクトを認証した ID プロバイダーを記録します。 この値は、発行者とテナントが異なるユーザー アカウント (たとえばゲスト) の場合を除いて、発行者クレームの値と同じです。 クレームが存在しない場合は、代わりに `iss` の値を使用できることを示しています。  個人用アカウントが組織のコンテキストで使用されている場合 (たとえば、個人用アカウントが Azure AD テナントに招待された場合)、`idp` 要求は 'live.com' または Microsoft アカウント テナント `9188040d-6c67-4c5b-b112-36a304b66dad` を含む STS URI である可能性があります。 |  
| `iat` | int、UNIX タイムスタンプ | "Issued At" は、このトークンの認証がいつ行われたのかを示します。 |
| `nbf` | int、UNIX タイムスタンプ | "nbf" (not before) 要求は JWT が有効になる日時を示します。これ以前にその JWT を受け入れて処理することはできません。 |
| `exp` | int、UNIX タイムスタンプ | "exp" (expiration time) 要求は、JWT の有効期限を示します。これ以降は、その JWT を受け入れて処理することはできません。 この日時より前でも、リソースがトークンを拒否する場合があることに注意してください。たとえば、認証での変更が必要な場合や、トークンの失効が検出された場合です。 |
| `aio` | 不透明な文字列 | Azure AD がトークンの再利用のためにデータの記録に使用する内部要求。 リソースでこの要求を使用しないでください。 |
| `acr` | 文字列、"0" または "1" | v1.0 トークンにのみ存在します。 "認証コンテキスト クラス" 要求。 値 「0」 は、エンドユーザーの認証が ISO/IEC 29115 の要件を満たしていないことを示します。 |
| `amr` | 文字列の JSON 配列 | v1.0 トークンにのみ存在します。 トークンのサブジェクトが認証された方法を示します。 詳細については、「[amr 要求](#the-amr-claim)」をご覧ください。 |
| `appid` | 文字列、GUID | v1.0 トークンにのみ存在します。 トークンを使用するクライアントのアプリケーションID。 アプリケーションとして識別することもできますが、アプリケーションを使用しているユーザーとして識別することもできます。 アプリケーション ID は通常、アプリケーション オブジェクトを表しますが、Azure AD 内のサービス プリンシパル オブジェクトを表すこともできます。 |
| `appidacr` | "0"、"1"、または "2" | v1.0 トークンにのみ存在します。 クライアントが認証された方法を示します。 パブリック クライアントの場合、値は "0" です。 クライアント ID とクライアント シークレットが使用されている場合、値は "1" です。 クライアント証明書が認証に使用された場合、値は "2" です。 |
| `azp` | 文字列、GUID | V2.0 トークンにのみ存在します。`appid` に代わるものです。 トークンを使用するクライアントのアプリケーションID。 アプリケーションとして識別することもできますが、アプリケーションを使用しているユーザーとして識別することもできます。 アプリケーション ID は通常、アプリケーション オブジェクトを表しますが、Azure AD 内のサービス プリンシパル オブジェクトを表すこともできます。 |
| `azpacr` | "0"、"1"、または "2" | V2.0 トークンにのみ存在します。`appidacr` に代わるものです。 クライアントが認証された方法を示します。 パブリック クライアントの場合、値は "0" です。 クライアント ID とクライアント シークレットが使用されている場合、値は "1" です。 クライアント証明書が認証に使用された場合、値は "2" です。 |
| `preferred_username` | string | ユーザーを表すプライマリ ユーザー名です。 電子メール アドレス、電話番号、または指定された書式のない一般的なユーザー名を指定できます。 その値は、変更可能であり、時間の経過と共に変化することがあります。 これは変更可能であるため、この値は、承認の決定には使用できません。  ただしユーザー名のヒントには使用できます。 この要求を受け取るには、 `profile` スコープが必要です。 |
| `name` | String | トークンのサブジェクトを識別する、人間が判読できる値を提供します。 この値は、一意であるとは限らず、変更可能であり、表示目的でのみ使用されます。 この要求を受け取るには、 `profile` スコープが必要です。 |
| `scp` | 文字列、スコープのスペース区切りリスト | クライアント アプリケーションが同意を要求し、同意を得た、アプリケーションによって公開されているスコープのセット。 アプリでは、これらのスコープがアプリによって公開されている有効なスコープであることを確認し、これらのスコープの値に基づいて承認を決定する必要があります。 [ユーザー トークン](#user-and-application-tokens)にのみ含まれます。 |
| `roles` | 文字列の配列、アクセス許可の一覧 | 要求元のアプリケーションに呼び出しのアクセス許可が付与されている、アプリケーションまたはユーザーによって公開されているアクセス許可のセット。 [アプリケーション トークン](#user-and-application-tokens)では、これはユーザー スコープの代わりに[クライアント資格情報](v1-oauth2-client-creds-grant-flow.md)フローで使用されます。  [ユーザー トークン](#user-and-application-tokens)では、これにはターゲット アプリケーションでユーザーに割り当てられたロールが設定されます。 |
| `wids` | [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) GUID の配列 | [管理者ロール ページ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids)に存在するロールのセクションから、このユーザーに割り当てられたテナント全体のロールを示します。  この要求は、[アプリケーション マニフェスト](reference-app-manifest.md)の `groupMembershipClaims` プロパティを介して、アプリケーションごとに構成されます。  これを "All" または "DirectoryRole" に設定することが必要です。  トークンの長さの問題のため、暗黙的フローを介して取得されたトークンには存在しない可能性があります。 |
| `groups` | GUID の JSON 配列 | サブジェクトのグループ メンバーシップを表すオブジェクト ID です。 これらの値は一意 (「オブジェクト ID」を参照) であり、アクセスの管理 (リソースへのアクセスを承認するなど) に安全に使用できます。 groups 要求に含まれるグループは、[アプリケーション マニフェスト](reference-app-manifest.md)の `groupMembershipClaims` プロパティを使用してアプリケーションごとに構成されます。 値が null の場合はすべてのグループが除外され、値が ”SecurityGroup” の場合は Active Directory セキュリティ グループのメンバーシップのみが含まれ、値が ”All” の場合はセキュリティ グループと Office 365 配布リストの両方が含まれます。 <br><br>暗黙的な許可での `groups` 要求の使用の詳細については、以下の `hasgroups` 要求を参照してください。 <br>他のフローでは、ユーザーが属するグループの数が上限 (SAML の場合は 150、JWT の場合は 200) を超えた場合、ユーザーのグループのリストを含む AAD Graph エンドポイントを参照する要求ソースに超過要求が追加されます。 |
| `hasgroups` | Boolean | 存在する場合、常に `true` であり、ユーザーが 1 つ以上のグループに属していることを示します。 すべてのグループ要求で URL 長の制限 (現在は 6 以上のグループ) を超えて URI フラグメントが拡張された場合、暗黙的な許可フローの JWT で `groups` 要求の代わりに使用されます。 クライアントが Graph を使用して、ユーザーのグループを決定する必要があることを示します (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`)。 |
| `groups:src1` | JSON オブジェクト | 長さは制限されていないが (上記 `hasgroups` を参照)、トークンには大きすぎるトークン要求の場合、ユーザーのすべてのグループ リストへのリンクが含まれます。 SAML では `groups` 要求の代わりに新しい要求として、JWT では分散要求として使用されます。 <br><br>**JWT 値の例**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | 文字列、GUID | トークンが情報をアサートするプリンシパルです (アプリのユーザーなど)。 この値は変更不可で、再割り当ても再利用もできません。 そのため、この値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。また、データベース テーブルのキーとして使用することもできます。 サブジェクトは、Azure AD が発行するトークン内に常に存在するため、汎用性のある承認システムでこの値を使用することをお勧めします。 ただし、サブジェクトはペアワイズ識別子で、特定のアプリケーション ID に一意です。 そのため、1 人のユーザーが 2 つの異なるクライアント ID を使用して 2 つの異なるアプリにサインインすると、そのアプリは、サブジェクト要求に対して 2 つの異なる値を受け取ることになります。 この動作が求められているかどうかは、アーキテクチャやプライバシーの要件によって異なります。 `oid` 要求 (テナント内のアプリ全体で同じままです) も参照してください。 |
| `oid` | 文字列、GUID | Microsoft ID プラットフォーム (ここではユーザー アカウント) におけるオブジェクトの変更不可の識別子です。 承認チェックの安全に実行するとき、また、データベース テーブルのキーとして使用することもできます。 この ID によって、複数のアプリケーションでユーザーが一意に識別されます。同じユーザーにサインインする 2 つの異なるアプリケーションは `oid` 要求で同じ値を受け取ります。 そのため、Microsoft Graph などの Microsoft オンライン サービスに対してクエリを実行するときに `oid` を使用できます。 Microsoft Graph は、この ID を、指定されたユーザー アカウントの `id` プロパティとして返します。 `oid` では複数のアプリがユーザーを関連付けることができるため、この要求を受け取るには、`profile` スコープが必要です。 1 人のユーザーが複数のテナントに存在する場合、そのユーザーのオブジェクト ID はテナントごとに異なります。つまり、ユーザーは、同じ資格情報で各アカウントにログインしても、異なるアカウントと見なされます。 |
| `tid` | 文字列、GUID | ユーザーが属している Azure AD テナントを表します。 職場または学校アカウントの場合、GUID はユーザーが属している組織の不変のテナント ID です。 個人アカウントでは、この値は `9188040d-6c67-4c5b-b112-36a304b66dad` です。 この要求を受け取るには、 `profile` スコープが必要です。 |
| `unique_name` | String | v1.0 トークンにのみ存在します。 トークンのサブジェクトを識別する、人が判読できる値を提供します。 この値は、テナント内で一意であることが保証されているわけではなく、表示目的でのみ使用する必要があります。 |
| `uti` | 不透明な文字列 | Azure がトークンの再検証に使用する内部要求。 リソースでこの要求を使用しないでください。 |
| `rh` | 不透明な文字列 | Azure がトークンの再検証に使用する内部要求。 リソースでこの要求を使用しないでください。 |
| `ver` | 文字列、`1.0` または `2.0` | アクセス トークンのバージョンを示します。 |

#### <a name="v10-basic-claims"></a>v1.0 の基本要求

次の要求は、該当する場合は v1.0 トークンに含まれますが、既定では v2.0 トークンには含まれません。 v2.0 を使用しており、これらの要求のいずれかが必要な場合は、[省略可能な要求](active-directory-optional-claims.md)を使用してそれらを要求してください。

| 要求 | 形式 | 説明 |
|-----|--------|-------------|
| `ipaddr`| String | ユーザーが認証された IP アドレス。 |
| `onprem_sid`| 文字列 ([SID 形式](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components)) | ユーザーがオンプレミス認証を行った場合、この要求によって SID が提供されます。 レガシ アプリケーションでの承認に `onprem_sid` を使用できます。|
| `pwd_exp`| int、UNIX タイムスタンプ | ユーザーのパスワードの有効期限を示します。 |
| `pwd_url`| String | パスワードをリセットするためにユーザーを送信できる URL。 |
| `in_corp`| ブール値 | クライアントが企業ネットワークからログインしている場合に通知します。 そうでない場合、この要求は含まれません。 |
| `nickname`| string | ユーザーの追加の名前。姓または名とは別の名前です。|
| `family_name` | String | ユーザー オブジェクトで定義されたユーザーの姓を示します。 |
| `given_name` | String | ユーザー オブジェクトに設定されたユーザーの名を示します。 |
| `upn` | string | ユーザーのユーザー名。 電話番号、電子メール アドレス、または書式なし文字列を指定できます。 表示目的でのみ使用し、再認証のシナリオでユーザー名のヒントを提供する必要があります。 |

#### <a name="the-amr-claim"></a>`amr` 要求

Microsoft ID は、アプリケーションに関連している可能性のあるさまざまな方法で認証できます。 `amr` 要求は、パスワードと Authenticator アプリの両方を使用した認証用に、複数の項目を格納できる配列 (`["mfa", "rsa", "pwd"]` など) です。

| 値 | 説明 |
|-----|-------------|
| `pwd` | パスワード認証。ユーザーの Microsoft パスワードまたはアプリのクライアント スークレット。 |
| `rsa` | [Microsoft Authenticator アプリ](https://aka.ms/AA2kvvu)を使用した認証など、認証が RSA キーの証明に基づいていたことを示します。 これは、認証が、サービスが所有する X509 証明書を使用して自己署名 JWT によって実行された場合に含まれます。 |
| `otp` | 電子メールまたはテキスト メッセージを使用したワンタイム パスコード。 |
| `fed` | フェデレーション認証アサーション (JWT や SAML など) が使用されたことを示します。 |
| `wia` | Windows 統合認証 |
| `mfa` | 多要素認証が使用されたことを示します。 この値が存在する場合は、他の認証方法も含まれます。 |
| `ngcmfa` | `mfa` と同等です。特定の高度な資格情報の種類のプロビジョニングに使用されます。 |
| `wiaormfa`| ユーザーが Windows 資格情報または MFA 資格情報を使用して認証されたことを示します。 |
| `none` | 認証は実行されませんでした。 |

## <a name="validating-tokens"></a>トークンの検証

id_token または access_token を検証するには、アプリはトークンの署名と要求の両方を検証する必要があります。 アクセス トークンを検証するには、アプリは発行者、対象ユーザー、および署名トークンも検証する必要があります。 これらの検証は、OpenID 探索ドキュメント内の値に対して行ってください。 たとえば、テナントに依存しないバージョンのドキュメントは [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) にあります。

Azure AD ミドルウェアにはアクセス トークンを検証するための機能が組み込まれており、選択した言語の[サンプル](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples)を参照できます。 JWT トークンを明示的に検証する方法の詳細については、[JWT の手動での検証のサンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation)を参照してください。

トークンの検証を簡単に処理する方法を示すライブラリとコード サンプルが用意されています。 以下の情報は、基になるプロセスを理解することを望む開発者を対象としています。 JWT の検証に使用できるサードパーティのオープン ソース ライブラリもいくつか存在し、ほぼすべてのプラットフォームと言語に対して少なくとも 1 つのオプションがあります。 Azure AD 認証ライブラリとコード サンプルの詳細については、[v1.0 認証ライブラリ](active-directory-authentication-libraries.md)に関する記事および [v2.0 認証ライブラリ](reference-v2-libraries.md)に関する記事をご覧ください。

### <a name="validating-the-signature"></a>署名の検証

JWT には 3 つのセグメントがあり、 `.` 文字で区切られています。 1 番目のセグメントは**ヘッダー**、2 番目は**本文**、3 番目は**署名**と呼ばれます。 署名セグメントを使用してトークンの信頼性を検証し、アプリで信頼できることを確認できます。

Azure AD によって発行されるトークンは、RSA 256 などの業界標準の非対称暗号アルゴリズムを使用して署名されます。 JWT のヘッダーには、トークンの署名に使用されたキーと暗号方法に関する情報が含まれます。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` 要求はトークンへの署名に使用されたアルゴリズムを示し、`kid` 要求はトークンへの署名に使用された特定の公開キーを示します。

いつでも、Azure AD は公開/秘密キー ペアの特定セットのいずれかを使用して、id_token に署名できます。 Azure AD は定期的に使用可能なキー セットをローテーションするので、このキー変更を自動的に処理するようにアプリを作成する必要があります。 Azure AD によって使用される公開キーの更新を確認する適切な頻度は、24 時間間隔です。

署名の検証に必要な署名キー データは、次の場所にある [OpenID Connect メタデータのドキュメント](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)を使用して入手できます。

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> ブラウザーでこの [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) を試してみてください!

このメタデータ ドキュメントの詳細は次のとおりです。

* OpenID Connect 認証を実行するために必要なさまざまなエンドポイントの場所などの、いくつかの有効な情報を含む JSON オブジェクトです。
* トークンの署名に使用される公開キーのセットの場所を示す `jwks_uri` が含まれます。 `jwks_uri` にある JSON ドキュメントには、特定の時点で使用されているすべての公開キー情報が含まれます。 アプリでは、 `kid` 要求を JWT ヘッダーで使用して、特定のトークンの署名に使用されたこのドキュメント内の公開キーを選択できます。 その後、正しい公開キーと指定されたアルゴリズムを使用して、署名の検証を実行できます。

> [!NOTE]
> V1.0 エンドポイントは `x5t` および `kid` の両方の要求を返すのに対して、v2.0 エンドポイントは `kid` 要求のみで応答します。 いずれは、`kid` 要求を利用してトークンを検証することをお勧めします。

署名の検証の実行は、このドキュメントの範囲外です。必要に応じて、それを実行するために役立つオープン ソース ライブラリが多数存在します。  ただし、Microsoft Identity プラットフォームには、標準に対する 1 つのトークン署名拡張であるカスタム署名トークンがあります。  

[claims-mapping](active-directory-claims-mapping.md) 機能を使用した結果としてアプリにカスタム署名キーがある場合は、アプリの署名キー情報 (検証に使用する必要があります) を指す `jwks_uri` を取得するために、アプリ ID を含む `appid` クエリ パラメーターを追加する必要があります。 例: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` には、`https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` の `jwks_uri` が含まれます。

### <a name="claims-based-authorization"></a>クレーム ベースの承認

アプリケーションのビジネス ロジックでこの手順を示します。一般的な承認方法を次に示します。

* `scp` または `roles` 要求をチェックして、存在するすべてのスコープが API によって公開されているものに一致することを確認し、クライアントが要求されたアクションを実行することを許可します。
* ご自分の API に対して、呼び出し元のクライアントが `appid` クレームを使った 呼び出しを許可されていることを確認します。
* `appidacr` を使用して、呼び出し元のクライアントの認証の状態を検証します。これは、パブリック クライアントが API の呼び出しを許可されていない場合は 0 になります。
* 過去の `nonce` 要求のリストと照合して、トークンが再生されていないことを確認します。
* `tid` が、API の呼び出しを許可されているテナントと一致することを確認します。
* `acr` 要求を使用して、ユーザーが MFA を実行したことを確認します。 これは、[条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)を使用して適用する必要があります。
* アクセス トークンで `roles` または `groups` 要求を要求した場合は、ユーザーが、このアクションの実行を許可されているグループに属していることを確認します。
  * 暗黙的フローを使用してトークンを取得した場合、このデータは大きすぎてトークンに収まらないことが多いため、データを [Microsoft Graph](https://developer.microsoft.com/graph/) に照会することが必要になる可能性があります。

## <a name="user-and-application-tokens"></a>ユーザー トークンとアプリケーション トークン

アプリケーションは、ユーザーに代わってトークンを受け取る場合もあれば (通常のフロー)、([クライアント資格情報フロー](v1-oauth2-client-creds-grant-flow.md)を通じて) アプリケーションから直接受け取る場合もあります。 これらのアプリ専用トークンは、この呼び出しがアプリケーションからのものであり、その背後にユーザーがいないことを示します。 これらのトークンはほぼ同様に処理されますが、違いがいくつかあります。

* アプリ専用トークンには `scp` 要求は含まれず、代わりに `roles` 要求が含まれることがあります。 これに、(委任されたアクセス許可ではなく) アプリケーションのアクセス許可が記録されます。 委任されたアクセス許可とアプリケーションのアクセス許可の詳細については、[v1.0](v1-permissions-and-consent.md) および [v2.0](v2-permissions-and-consent.md) のアクセス許可と同意を参照してください。
* 多くの人間固有の要求 (`name` や `upn` など) はありません。
* `sub` および `oid` 要求は同じになります。 

## <a name="token-revocation"></a>トークンの失効

更新トークンは、さまざまな理由でいつでも無効にしたり、取り消したりできます。 これらはタイムアウトと失効の 2 つに大きく分けることができます。

### <a name="token-timeouts"></a>トークンのタイムアウト

* MaxInactiveTime:更新トークンが MaxInactiveTime で指示された時間内に使用されなかった場合、更新トークンは無効になります。
* MaxSessionAge:MaxAgeSessionMultiFactor または MaxAgeSessionSingleFactor が既定値 (Until-revoked) 以外に設定されている場合、MaxAgeSession* に設定された時間が経過すると、再認証が必要になります。
* 次に例を示します。
  * テナントの MaxInactiveTime が 5 日で、ユーザーが 1 週間の休暇を取った場合、7 日間にわたり Azure AD はユーザーからの新しいトークン要求を認識しません。 ユーザーが次に新しいトークンを要求するとき、更新トークンが失効していることがわかります。資格情報を再び入力する必要があります。
  * 機密性の高いアプリケーションの MaxAgeSessionSingleFactor は 1 日に設定されています。 ユーザーが月曜日にログインし、次に火曜日 (25 時間が経過した後) にログインした場合は、再認証が必要になります。

### <a name="revocation"></a>無効化

|   | パスワードに基づくクッキー | パスワードに基づくトークン | パスワードに基づかないクッキー | パスワードに基づかないトークン | 機密のクライアントのトークン |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| パスワードが期限切れ | 存続 | 存続 | 存続 | 存続 | 存続 |
| ユーザーによるパスワードの変更 | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| ユーザーがSSPRである | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| 管理者によるパスワードのリセット | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| ユーザーが [PowerShellによって、](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken)更新トークンを無効にする | 取り消し | 取り消し | 取り消し | 取り消し | 取り消し |
| 管理者が [PowerShellによって、](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken)テナントのすべての更新トークンを無効にする | 取り消し | 取り消し |取り消し | 取り消し | 取り消し |
| Web での[シングル サインアウト](v1-protocols-openid-connect-code.md#single-sign-out) | 取り消し | 存続 | 取り消し | 存続 | 存続 |

> [!NOTE]
> ｢パスワード基づかない｣ログインは、ユーザーがそれを得るために、パスワードをタイプしなかった場合です。 たとえば、Windows Hello、FIDO キー、または PIN で自分の顔を使用する場合です。
>
> 既知の問題は、Windows Primary 更新トークンに存在します。 PRT が、パスワードを使用して取得され、ユーザーが Hello を介して、ログインする場合、この PRT の発信元は変更されません。ユーザーが自分のパスワードが変更した場合、は取り消されます。
>
> 更新トークンは、新しいアクセス トークンや更新トークンのフェッチに使用されるときに無効になる、または取り消されることはありません。  

## <a name="next-steps"></a>次の手順

* [Azure AD の `id_tokens`](id-tokens.md) の詳細を確認します。
* [v1.0](v1-permissions-and-consent.md) と [v2.0](v2-permissions-and-consent.md) のアクセス許可と同意の詳細を確認します。
