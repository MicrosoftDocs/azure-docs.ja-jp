---
title: Microsoft ID プラットフォーム アクセス トークン | Azure
titleSuffix: Microsoft identity platform
description: Azure AD v1.0 および Microsoft ID プラットフォーム (v2.0) エンドポイントによって出力されるアクセス トークンについて説明します。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 2/18/2021
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 8630dd2fb1157fbeba99f2a06d73712ab46a63f4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035069"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft ID プラットフォーム アクセス トークン

アクセス トークンによって、クライアントは保護された Web API を安全に呼び出すことができます。Web API は、認証と認可を実行するためにアクセス トークンを使用します。 OAuth 仕様によると、アクセス トークンは、設定された形式を持たない不透明型の文字列です。ID プロバイダー (IDP) の中には GUID を使用するものもあれば、暗号化された BLOB を使用するものもあります。 Microsoft ID プラットフォームでは、トークンを受け入れる API の構成に応じて、さまざまなアクセス トークン形式が使用されます。 Microsoft ID プラットフォームで[開発者によって登録されるカスタム API](quickstart-configure-app-expose-web-apis.md) では、"v1" および "v2" と呼ばれる 2 つの異なる形式の JSON Web Token (JWT) のいずれかを選択できます。また、Microsoft Graph などの Microsoft が開発した API や Azure の API には、追加の専用トークン形式があります。 これらの専用形式は、暗号化トークン、JWT、または検証を行わない特殊な JWT 類似トークンである可能性があります。

トークンの内容は特定のリソース (API) のみを対象としているため、クライアントはアクセス トークンを不透明型の文字列として扱う必要があります。 検証とデバッグ "*のみ*" を目的として、開発者は [jwt.ms](https://jwt.ms) などのサイトを使用して JWT をデコードできます。 ただし、Microsoft API 用に受信するトークンは常に JWT であるとは限らず、それを常にデコードできるとは限らないことに注意してください。

アクセス トークン内の内容の詳細については、クライアントは、アクセス トークンと共にクライアントに返されるトークン応答データを使用する必要があります。 クライアントがアクセス トークンを要求すると、Microsoft ID プラットフォームからは、アプリで使用されるそのアクセス トークンに関する何らかのメタデータも返されます。 この情報には、アクセス トークンの有効期限や、それが有効なスコープが含まれます。 このデータを使用すると、アプリはアクセス トークン自体を解析しなくても、そのアクセス トークンのインテリジェントなキャッシュを実行できます。

API でアクセス トークン内の要求を検証して使用する方法については、次のセクションをご覧ください。  

> [!NOTE]
> このページのすべてのドキュメントは、特に記載がある場合を除き、登録した API に対して発行されるトークンにのみ適用されます。  Microsoft が所有する API に対して発行されるトークンには適用されません。また、それらのトークンを使用して、Microsoft ID プラットフォームが、作成した API に対してトークンを発行する方法を検証することもできません。  

## <a name="token-formats-and-ownership"></a>トークンの形式と所有権

### <a name="v10-and-v20"></a>v1.0 および v2.0 

Microsoft ID プラットフォームで使用できるアクセス トークンには、v1.0 と v2.0 の 2 つのバージョンがあります。  これらのバージョンによって、トークン内の要求の内容が管理され、それにより Web API はトークンの状態を制御できます。 Web API では、これらのいずれかが登録時に既定として選択されます。Azure AD 専用アプリでは v1.0、コンシューマー アカウントをサポートするアプリでは v2.0 です。  これは、[アプリ マニフェスト](reference-app-manifest.md#manifest-reference)の `accessTokenAcceptedVersion` 設定を使用してアプリケーションで制御できます。この場合、`null` および `1` では v1.0 トークンになり、`2` では v2.0 トークンになります。

### <a name="what-app-is-a-token-for"></a>どのようなアプリがトークンの "対象" となるか

アクセス トークン要求には、トークンを要求するクライアントと、API 呼び出し時にトークンを受け入れるリソース (API) の 2 つのパーティが関与します。 トークン内の `aud` 要求は、トークンが対象とするリソース (その "*対象者*") を示します。 クライアントはトークンを使用しますが、それを理解したり解析しようとしたりすることはできません。 リソースはトークンを受け入れます。  

Microsoft ID プラットフォームでは、任意のバージョンのエンドポイントからの任意のトークン バージョンの発行がサポートされています。これらは関連性がありません。 このため、`accessTokenAcceptedVersion` を `2` に設定したリソースでは、v1.0 エンドポイントを呼び出してその API のトークンを取得するクライアントは、v2.0 アクセス トークンを受け取ることになります。  リソースはトークン (その `aud` 要求を含むもの) を常に所有し、トークンの詳細を変更できる唯一のアプリケーションになります。 このため、"*クライアント*" のアクセス トークンの [省略可能な要求](active-directory-optional-claims.md)を変更しても、Microsoft Graph リソースが所有する `user.read` に対してトークンが要求されたときに受信されるアクセス トークンは変更されません。

### <a name="sample-tokens"></a>サンプル トークン

v1.0 トークンと v2.0 トークンは似ており、同じクレームが多く含まれています。 各トークンの例を次に示します。 ただし、これらの例のトークンは、発行前にキーがローテーションされ、その個人情報が削除されているため、[検証](#validating-tokens)されません。

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

この v1.0 トークンは [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd) で表示できます。

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

この v2.0 トークンは [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt) で表示できます。

## <a name="claims-in-access-tokens"></a>アクセス トークン内のクレーム

JWT (JSON Web トークン) は 3 つの部分に分かれています。

* **ヘッダー** - トークンの種類や署名方法に関する情報など、[トークンの検証](#validating-tokens)方法に関する情報が提供されます。
* **ペイロード** - サービスを呼び出そうとしているユーザーまたはアプリに関する重要なデータがすべて含まれています。
* **署名** - トークンの検証に使用される原材料です。

各部分はピリオド (`.`) で区切られ、Base64 で個別にエンコードされます。

クレームは、そこに入力される値が存在する場合にのみ存在します。 アプリは要求の存在に依存することはできません。 例として、`pwd_exp` (すべてのテナントがパスワードを期限切れにする必要があるわけではありません) や、`family_name` (名前のないアプリケーションに代わって、[クライアント資格情報フロー](v2-oauth2-client-creds-grant-flow.md)が使用されます) などがあります。 アクセス トークンの検証に使用されるクレームは常に存在します。

再利用に備え、Azure AD を使ったトークンのセキュリティ保護に活用されるクレームもあります。 これらは公開されないものとして、記述で "Opaque" としてマークされます。 これらのクレームはトークンに表示される場合とされない場合があり、新しいものが予告なく追加される場合もあります。

### <a name="header-claims"></a>ヘッダーのクレーム

|要求 | Format | 説明 |
|--------|--------|-------------|
| `typ` | 文字列 - 常に "JWT" | トークンが JWT であることを示します。|
| `nonce` | String | トークン リプレイ攻撃から保護するために使用される一意識別子。 リソースでこの値を記録することで、再生を防ぐことができます。 |
| `alg` | String | トークンの署名に使用されたアルゴリズム ("RS256" など) を示します。 |
| `kid` | String | このトークンの署名に使用されている公開キーの拇印が記述されています。 v1.0 と v2.0 のどちらのアクセス トークンでも生成されます。 |
| `x5t` | String | `kid` と同様に機能します (使用方法も値も同じ)。 `x5t` は、互換性を目的として v1.0 アクセス トークンでのみ生成されるレガシ クレームです。 |

### <a name="payload-claims"></a>ペイロードのクレーム

| 要求 | Format | 説明 |
|-----|--------|-------------|
| `aud` | 文字列、アプリ ID URI、または GUID | トークンの目的の受信者 (その対象者) を示します。  API でこの値を検証し、値が一致しない場合はトークンを拒否する必要があります。 v2.0 トークンでは、これは常に API のクライアント ID です。一方 v1.0 トークンでは、これは、クライアントがトークンを要求した方法に応じて、クライアント ID、または要求で使用されるリソース URI になります。|
| `iss` | 文字列、STS URI | トークンを作成して返したセキュリティ トークン サービス (STS)、およびユーザーが認証された Azure AD テナントを示します。 発行されたトークンが v2.0 トークンである場合 (`ver` 要求を参照)、URI は `/v2.0` で終了します。 ユーザーが Microsoft アカウントを持つコンシューマー ユーザーであることを示す GUID は `9188040d-6c67-4c5b-b112-36a304b66dad` です。 お使いのアプリで、要求の GUID 部分を使用して、アプリにサインインできるテナントのセットを制限できます (該当する場合)。 |
|`idp`| 文字列 (通常は STS URI) | トークンのサブジェクトを認証した ID プロバイダーを記録します。 この値は、発行者とテナントが異なるユーザー アカウント (たとえばゲスト) の場合を除いて、発行者クレームの値と同じです。 クレームが存在しない場合は、代わりに `iss` の値を使用できることを示しています。  個人用アカウントが組織のコンテキストで使用されている場合 (たとえば、個人用アカウントが Azure AD テナントに招待された場合)、`idp` 要求は 'live.com' または Microsoft アカウント テナント `9188040d-6c67-4c5b-b112-36a304b66dad` を含む STS URI である可能性があります。 |
| `iat` | int、UNIX タイムスタンプ | "Issued At" は、このトークンの認証がいつ行われたのかを示します。 |
| `nbf` | int、UNIX タイムスタンプ | "nbf" (not before) 要求は JWT が有効になる日時を示します。これ以前にその JWT を受け入れて処理することはできません。 |
| `exp` | int、UNIX タイムスタンプ | "exp" (expiration time) 要求は、JWT の有効期限を示します。これ以降は、その JWT を受け入れて処理することはできません。 この日時より前でも、リソースがトークンを拒否する場合があることに注意してください。たとえば、認証での変更が必要な場合や、トークンの失効が検出された場合です。 |
| `aio` | 不透明な文字列 | Azure AD がトークン再利用のためにデータの記録に使用する内部の要求。 リソースでこの要求を使用しないでください。 |
| `acr` | 文字列、"0" または "1" | v1.0 トークンにのみ存在します。 "認証コンテキスト クラス" 要求。 値 「0」 は、エンドユーザーの認証が ISO/IEC 29115 の要件を満たしていないことを示します。 |
| `amr` | 文字列の JSON 配列 | v1.0 トークンにのみ存在します。 トークンのサブジェクトが認証された方法を示します。 詳細については、「[amr 要求](#the-amr-claim)」をご覧ください。 |
| `appid` | 文字列、GUID | v1.0 トークンにのみ存在します。 トークンを使用するクライアントのアプリケーションID。 アプリケーションとして識別することもできますが、アプリケーションを使用しているユーザーとして識別することもできます。 アプリケーション ID は通常、アプリケーション オブジェクトを表しますが、Azure AD 内のサービス プリンシパル オブジェクトを表すこともできます。 |
| `azp` | 文字列、GUID | V2.0 トークンにのみ存在します。`appid` に代わるものです。 トークンを使用するクライアントのアプリケーションID。 アプリケーションとして識別することもできますが、アプリケーションを使用しているユーザーとして識別することもできます。 アプリケーション ID は通常、アプリケーション オブジェクトを表しますが、Azure AD 内のサービス プリンシパル オブジェクトを表すこともできます。 |
| `appidacr` | "0"、"1"、または "2" | v1.0 トークンにのみ存在します。 クライアントが認証された方法を示します。 パブリック クライアントの場合、値は "0" です。 クライアント ID とクライアント シークレットが使用されている場合、値は "1" です。 クライアント証明書が認証に使用された場合、値は "2" です。 |
| `azpacr` | "0"、"1"、または "2" | V2.0 トークンにのみ存在します。`appidacr` に代わるものです。 クライアントが認証された方法を示します。 パブリック クライアントの場合、値は "0" です。 クライアント ID とクライアント シークレットが使用されている場合、値は "1" です。 クライアント証明書が認証に使用された場合、値は "2" です。 |
| `preferred_username` | String | ユーザーを表すプライマリ ユーザー名です。 電子メール アドレス、電話番号、または指定された書式のない一般的なユーザー名を指定できます。 その値は、変更可能であり、時間の経過と共に変化することがあります。 これは変更可能であるため、この値は、承認の決定には使用できません。  ただし、これを、ユーザー名のヒントとして使用することや、人間が判読できる UI でユーザー名として使用することができます。 この要求を受け取るには、 `profile` スコープが必要です。 v2.0 トークンにのみ存在します。 |
| `name` | String | トークンのサブジェクトを識別する、人間が判読できる値を提供します。 この値は、一意であるとは限らず、変更可能であり、表示目的でのみ使用されます。 この要求を受け取るには、 `profile` スコープが必要です。 |
| `scp` | 文字列、スコープのスペース区切りリスト | クライアント アプリケーションが同意を要求し、同意を得た、アプリケーションによって公開されているスコープのセット。 アプリでは、これらのスコープがアプリによって公開されている有効なスコープであることを確認し、これらのスコープの値に基づいて承認を決定する必要があります。 [ユーザー トークン](#user-and-application-tokens)にのみ含まれます。 |
| `roles` | 文字列の配列、アクセス許可の一覧 | 要求元のアプリケーションに呼び出しのアクセス許可が付与されている、アプリケーションまたはユーザーによって公開されているアクセス許可のセット。 [アプリケーション トークン](#user-and-application-tokens)では、これはユーザー スコープの代わりにクライアント資格情報フロー ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)、[v2.0](v2-oauth2-client-creds-grant-flow.md)) で使用されます。  [ユーザー トークン](#user-and-application-tokens)では、これにはターゲット アプリケーションでユーザーに割り当てられたロールが設定されます。 |
| `wids` | [RoleTemplateID](../roles/permissions-reference.md#all-roles) GUID の配列 | [Azure AD 組み込みロール](../roles/permissions-reference.md#all-roles)に存在するロールのセクションから、このユーザーに割り当てられたテナント全体のロールを示します。  この要求は、[アプリケーション マニフェスト](reference-app-manifest.md)の `groupMembershipClaims` プロパティを介して、アプリケーションごとに構成されます。  これを "All" または "DirectoryRole" に設定することが必要です。  トークンの長さの問題のため、暗黙的フローを介して取得されたトークンには存在しない可能性があります。 |
| `groups` | GUID の JSON 配列 | サブジェクトのグループ メンバーシップを表すオブジェクト ID です。 これらの値は一意 (「オブジェクト ID」を参照) であり、アクセスの管理 (リソースへのアクセスを承認するなど) に安全に使用できます。 groups 要求に含まれるグループは、[アプリケーション マニフェスト](reference-app-manifest.md)の `groupMembershipClaims` プロパティを使用してアプリケーションごとに構成されます。 値が null の場合はすべてのグループが除外され、値が "SecurityGroup" の場合は Active Directory セキュリティ グループのメンバーシップのみが含まれ、値が ”All” の場合はセキュリティ グループと Microsoft 365 配布リストの両方が含まれます。 <br><br>暗黙的な許可での `groups` 要求の使用の詳細については、以下の `hasgroups` 要求を参照してください。 <br>他のフローでは、ユーザーが属するグループの数が上限 (SAML の場合は 150、JWT の場合は 200) を超えた場合、ユーザーのグループのリストを含む Microsoft Graph エンドポイントを参照する要求ソースに超過要求が追加されます。 |
| `hasgroups` | Boolean | 存在する場合、常に `true` であり、ユーザーが 1 つ以上のグループに属していることを示します。 すべてのグループ要求で URL 長の制限 (現在は 6 以上のグループ) を超えて URI フラグメントが拡張された場合、暗黙的な許可フローの JWT で `groups` 要求の代わりに使用されます。 クライアントが Microsoft Graph API を使用して、ユーザーのグループを決定する必要があることを示します (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`)。 |
| `groups:src1` | JSON オブジェクト | 長さは制限されていないが (上記 `hasgroups` を参照)、トークンには大きすぎるトークン要求の場合、ユーザーのすべてのグループ リストへのリンクが含まれます。 SAML では `groups` 要求の代わりに新しい要求として、JWT では分散要求として使用されます。 <br><br>**JWT 値の例**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | String | トークンが情報をアサートするプリンシパルです (アプリのユーザーなど)。 この値は変更不可で、再割り当ても再利用もできません。 そのため、この値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。また、データベース テーブルのキーとして使用することもできます。 サブジェクトは、Azure AD が発行するトークン内に常に存在するため、汎用性のある承認システムでこの値を使用することをお勧めします。 ただし、サブジェクトはペアワイズ識別子で、特定のアプリケーション ID に一意です。 そのため、1 人のユーザーが 2 つの異なるクライアント ID を使用して 2 つの異なるアプリにサインインすると、そのアプリは、サブジェクト要求に対して 2 つの異なる値を受け取ることになります。 この動作が求められているかどうかは、アーキテクチャやプライバシーの要件によって異なります。 `oid` 要求 (テナント内のアプリ全体で同じままです) も参照してください。 |
| `oid` | 文字列、GUID | 要求の "プリンシパル" の変更不可 ID - ID の有効性が確認済みのユーザーまたはサービス プリンシパル。  ID トークンとアプリ + ユーザー トークンでは、これはユーザーのオブジェクト ID です。  アプリ専用トークンでは、これは呼び出し元のサービス プリンシパルのオブジェクト ID です。 承認チェックの安全に実行するとき、また、データベース テーブルのキーとして使用することもできます。 この ID によって、複数のアプリケーションでプリンシパルが一意に識別されます。同じユーザーにサインインする 2 つの異なるアプリケーションは `oid` 要求で同じ値を受け取ります。 そのため、Microsoft Graph などの Microsoft オンライン サービスに対してクエリを実行するときに `oid` を使用できます。 Microsoft Graph は、この ID を、指定された[ユーザー アカウント](/graph/api/resources/user)の `id` プロパティとして返します。 `oid` では複数のアプリがプリンシパルを関連付けることができるため、ユーザーがこの要求を受け取るには、`profile` スコープが必要です。 1 人のユーザーが複数のテナントに存在する場合、そのユーザーのオブジェクト ID はテナントごとに異なります。つまり、ユーザーは、同じ資格情報で各アカウントにログインしても、異なるアカウントと見なされます。 |
| `tid` | 文字列、GUID | ユーザーが属している Azure AD テナントを表します。 職場または学校アカウントの場合、GUID はユーザーが属している組織の不変のテナント ID です。 個人アカウントでは、この値は `9188040d-6c67-4c5b-b112-36a304b66dad` です。 この要求を受け取るには、 `profile` スコープが必要です。 |
| `unique_name` | String | v1.0 トークンにのみ存在します。 トークンのサブジェクトを識別する、人が判読できる値を提供します。 この値は、テナント内で一意であることが保証されているわけではなく、表示目的でのみ使用する必要があります。 |
| `uti` | 不透明な文字列 | Azure がトークンの再検証に使用する内部要求。 リソースでこの要求を使用しないでください。 |
| `rh` | 不透明な文字列 | Azure がトークンの再検証に使用する内部要求。 リソースでこの要求を使用しないでください。 |
| `ver` | 文字列、`1.0` または `2.0` | アクセス トークンのバージョンを示します。 |

**グループ超過要求**

トークンのサイズが HTTP ヘッダー サイズの上限を超えないよう、Azure AD では、グループ要求に含まれるオブジェクト ID の数が制限されます。 超過制限 (SAML トークンの場合は 150、JWT トークンの場合は 200、暗黙的フローによって発行される場合は 6 のみ) を超えるグループのメンバーにユーザーがなっている場合、Azure AD は、グループ要求をトークンに出力しません。 代わりに、Microsoft Graph API に照会してユーザーのグループ メンバーシップを取得するようアプリケーションに指示する超過要求がトークンに追加されます。

```JSON
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

超過のシナリオは、[App Creation Scripts](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) フォルダーにある `BulkCreateGroups.ps1` を使用してテストできます。

#### <a name="v10-basic-claims"></a>v1.0 の基本要求

次の要求は、該当する場合は v1.0 トークンに含まれますが、既定では v2.0 トークンには含まれません。 v2.0 を使用しており、これらの要求のいずれかが必要な場合は、[省略可能な要求](active-directory-optional-claims.md)を使用してそれらを要求してください。

| 要求 | Format | 説明 |
|-----|--------|-------------|
| `ipaddr`| String | ユーザーが認証された IP アドレス。 |
| `onprem_sid`| 文字列 ([SID 形式](/windows/desktop/SecAuthZ/sid-components)) | ユーザーがオンプレミス認証を行った場合、この要求によって SID が提供されます。 レガシ アプリケーションでの承認に `onprem_sid` を使用できます。|
| `pwd_exp`| int、UNIX タイムスタンプ | ユーザーのパスワードの有効期限を示します。 |
| `pwd_url`| String | パスワードをリセットするためにユーザーを送信できる URL。 |
| `in_corp`| boolean | クライアントが企業ネットワークからログインしている場合に通知します。 そうでない場合、この要求は含まれません。 |
| `nickname`| String | ユーザーの追加の名前。姓または名とは別の名前です。|
| `family_name` | String | ユーザー オブジェクトで定義されたユーザーの姓を示します。 |
| `given_name` | String | ユーザー オブジェクトに設定されたユーザーの名を示します。 |
| `upn` | String | ユーザーのユーザー名。 電話番号、電子メール アドレス、または書式なし文字列を指定できます。 表示目的でのみ使用し、再認証のシナリオでユーザー名のヒントを提供する必要があります。 |

#### <a name="the-amr-claim"></a>`amr` 要求

Microsoft ID は、アプリケーションに関連している可能性のあるさまざまな方法で認証できます。 `amr` 要求は、パスワードと Authenticator アプリの両方を使用した認証用に、複数の項目を格納できる配列 (`["mfa", "rsa", "pwd"]` など) です。

| 値 | 説明 |
|-----|-------------|
| `pwd` | パスワード認証。ユーザーの Microsoft パスワードまたはアプリのクライアント スークレット。 |
| `rsa` | [Microsoft Authenticator アプリ](https://aka.ms/AA2kvvu)を使用した認証など、認証が RSA キーの証明に基づいていたことを示します。 これは、認証が、サービスが所有する X509 証明書を使用して自己署名 JWT によって実行された場合に含まれます。 |
| `otp` | 電子メールまたはテキスト メッセージを使用したワンタイム パスコード。 |
| `fed` | フェデレーション認証アサーション (JWT や SAML など) が使用されたことを示します。 |
| `wia` | Windows 統合認証 |
| `mfa` | [多要素認証](../authentication/concept-mfa-howitworks.md)が使用されました。 この値が存在する場合は、他の認証方法も含まれます。 |
| `ngcmfa` | `mfa` と同等です。特定の高度な資格情報の種類のプロビジョニングに使用されます。 |
| `wiaormfa`| ユーザーが Windows 資格情報または MFA 資格情報を使用して認証されたことを示します。 |
| `none` | 認証は実行されませんでした。 |

## <a name="validating-tokens"></a>トークンの検証

すべてのアプリでトークンを検証する必要はありません。 アプリでトークンを検証する必要があるのは、特定のシナリオのみです。

* [Web API](quickstart-configure-app-expose-web-apis.md) は、クライアントから自身に送信されたアクセス トークンを検証する必要があります。  `aud` 要求を含むトークンのみを受け入れる必要があります。
* ASP.NET Core のような機密性の高い Web アプリは、ユーザーのデータへのアクセスを許可したり、セッションを確立したりする前に、ハイブリッド フローでユーザーのブラウザーを介して自身に送信された ID トークンを検証する必要があります。

上記のシナリオのいずれにも当てはまらない場合、アプリケーションはトークンの検証からメリットを受けることはなく、トークンの有効性に基づいて判断が行われた場合、セキュリティと信頼性のリスクが発生する可能性があります。  ネイティブ アプリや SPA のようなパブリック クライアントはトークンの検証からメリットを受けることはありません。このアプリは IDP と直接通信するため、SSL 保護によってトークンが有効であることが保証されます。

API と Web アプリは、アプリケーションに一致する `aud` 要求を含むトークンのみを検証する必要があります。その他のリソースには、カスタム トークン検証規則がある場合があります。 たとえば、Microsoft Graph のトークンは、専用の形式であるため、これらの規則に従って検証することはできません。 別のリソースを対象とするトークンを検証して受け入れることは、[混乱した使節 (Confused Deputy)](https://cwe.mitre.org/data/definitions/441.html) の問題にたとえることができます。

アプリケーションで上記に従って id_token または access_token を検証する必要がある場合は、アプリで最初に OpenID 探索ドキュメントの値と突き合わせてトークンの署名と発行者を検証する必要があります。 たとえば、テナントに依存しないバージョンのドキュメントは [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) にあります。

次の情報は、基になるプロセスを理解する必要があるユーザーを対象としています。 Azure AD ミドルウェアにはアクセス トークンを検証するための機能が組み込まれており、選択した言語の[サンプル](sample-v2-code.md)を参照できます。 JWT の検証に使用できるサードパーティのオープン ソース ライブラリもいくつか存在し、ほぼすべてのプラットフォームと言語に対して少なくとも 1 つのオプションがあります。 Azure AD 認証ライブラリとコード サンプルの詳細については、[認証ライブラリ](reference-v2-libraries.md)に関する記事を参照してください。

### <a name="validating-the-signature"></a>署名の検証

JWT には 3 つのセグメントがあり、 `.` 文字で区切られています。 1 番目のセグメントは **ヘッダー**、2 番目は **本文**、3 番目は **署名** と呼ばれます。 署名セグメントを使用してトークンの信頼性を検証し、アプリで信頼できることを確認できます。

Azure AD によって発行されるトークンは、RS256 などの業界標準の非対称暗号アルゴリズムを使用して署名されます。 JWT のヘッダーには、トークンの署名に使用されたキーと暗号方法に関する情報が含まれます。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` 要求はトークンへの署名に使用されたアルゴリズムを示し、`kid` 要求はトークンへの検証に使用された特定の公開キーを示します。

いつでも、Azure AD は公開/秘密キー ペアの特定セットのいずれかを使用して、id_token に署名できます。 Azure AD は定期的に使用可能なキー セットをローテーションするので、このキー変更を自動的に処理するようにアプリを作成する必要があります。 Azure AD によって使用される公開キーの更新を確認する適切な頻度は、24 時間間隔です。

署名の検証に必要な署名キー データは、次の場所にある [OpenID Connect メタデータのドキュメント](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)を使用して入手できます。

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> ブラウザーでこの [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) を試してみてください!

このメタデータ ドキュメントの詳細は次のとおりです。

* OpenID Connect 認証を実行するために必要なさまざまなエンドポイントの場所などの、いくつかの有効な情報を含む JSON オブジェクトです。
* トークンの署名に使用される公開キーのセットの場所を示す `jwks_uri` が含まれます。 `jwks_uri` にある JSON Web キー (JWK) には、特定の時点で使用されているすべての公開キー情報が含まれます。  JWK 形式については [RFC 7517](https://tools.ietf.org/html/rfc7517) を参照してください。  アプリでは、 `kid` 要求を JWT ヘッダーで使用して、特定のトークンの署名に使用されたこのドキュメント内の公開キーを選択できます。 その後、正しい公開キーと指定されたアルゴリズムを使用して、署名の検証を実行できます。

> [!NOTE]
> `kid` 要求を利用してトークンを検証することをお勧めします。 v1.0 トークンには `x5t` と `kid` の両方の要求が含まれますが、v2.0 トークンには `kid` 要求のみ含まれます。

署名の検証の実行は、このドキュメントの範囲外です。必要に応じて、それを実行するために役立つオープン ソース ライブラリが多数存在します。  ただし、Microsoft ID プラットフォームには、標準に対する 1 つのトークン署名拡張であるカスタム署名キーがあります。

[claims-mapping](active-directory-claims-mapping.md) 機能を使用した結果としてアプリにカスタム署名キーがある場合は、アプリの署名キー情報 (検証に使用する必要があります) を指す `jwks_uri` を取得するために、アプリ ID を含む `appid` クエリ パラメーターを追加する必要があります。 例: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` には、`https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` の `jwks_uri` が含まれます。

### <a name="claims-based-authorization"></a>クレーム ベースの承認

アプリケーションのビジネス ロジックでこの手順を示します。一般的な承認方法を次に示します。

* `scp` または `roles` 要求をチェックして、存在するすべてのスコープが API によって公開されているものに一致することを確認し、クライアントが要求されたアクションを実行することを許可します。
* ご自分の API に対して、呼び出し元のクライアントが `appid` クレームを使った 呼び出しを許可されていることを確認します。
* `appidacr` を使用して、呼び出し元のクライアントの認証の状態を検証します。これは、パブリック クライアントが API の呼び出しを許可されていない場合は 0 になります。
* 過去の `nonce` 要求のリストと照合して、トークンが再生されていないことを確認します。
* `tid` が、API の呼び出しを許可されているテナントと一致することを確認します。
* `amr` 要求を使用して、ユーザーが MFA を実行したことを確認します。 これは、[条件付きアクセス](../conditional-access/overview.md)を使用して適用する必要があります。
* アクセス トークンで `roles` または `groups` 要求を要求した場合は、ユーザーが、このアクションの実行を許可されているグループに属していることを確認します。
  * 暗黙的フローを使用してトークンを取得した場合、このデータは大きすぎてトークンに収まらないことが多いため、データを [Microsoft Graph](https://developer.microsoft.com/graph/) に照会することが必要になる可能性があります。

## <a name="user-and-application-tokens"></a>ユーザー トークンとアプリケーション トークン

アプリケーションでは、ユーザー用トークンを受信する (通常のフロー) か、アプリケーションから直接受信します ([クライアント資格情報フロー](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)を通じて)。 これらのアプリ専用トークンは、この呼び出しがアプリケーションからのものであり、その背後にユーザーがいないことを示します。 これらのトークンは、ほぼ同じように処理されます。

* トークンのサブジェクトに付与されているアクセス許可を確認するには、`roles` を使用します。
* `oid` または `sub` を使用して、呼び出し元のサービス プリンシパルが想定されているものであることが検証されます。

アプリでアプリ専用アクセス トークンとユーザー用アクセス トークンを区別する必要がある場合は、`idtyp` [ オプション要求](active-directory-optional-claims.md)が使用されます。  `accessToken` フィールドに `idtyp` 要求を追加し、`app` の値を確認することで、アプリ専用のアクセス トークンを検出できます。  ユーザー用の ID トークンとアクセス トークンには、`idtyp` 要求は含まれません。

## <a name="token-revocation"></a>トークンの失効

更新トークンは、さまざまな理由でいつでも無効にしたり、取り消したりできます。 これらはタイムアウトと失効の 2 つに大きく分けることができます。

### <a name="token-timeouts"></a>トークンのタイムアウト

[トークンの有効期間の構成](active-directory-configurable-token-lifetimes.md)を使用すると、更新トークンの有効期間を変更できます。  一部のトークンが使用されない場合 (たとえば、ユーザーがアプリを 3 か月間開いていない場合)、有効期限が切れますが、これは正常です。  アプリでは、ログイン サーバーが期限切れのために更新トークンを拒否するシナリオが発生します。

* MaxInactiveTime:更新トークンが MaxInactiveTime で指示された時間内に使用されなかった場合、更新トークンは無効になります。
* MaxSessionAge:MaxAgeSessionMultiFactor または MaxAgeSessionSingleFactor が既定値 (Until-revoked) 以外に設定されている場合、MaxAgeSession* に設定された時間が経過すると、再認証が必要になります。
* 例 :
  * テナントの MaxInactiveTime が 5 日で、ユーザーが 1 週間の休暇を取った場合、7 日間にわたり Azure AD はユーザーからの新しいトークン要求を認識しません。 ユーザーが次に新しいトークンを要求するとき、更新トークンが失効していることがわかります。資格情報を再び入力する必要があります。
  * 機密性の高いアプリケーションの MaxAgeSessionSingleFactor は 1 日に設定されています。 ユーザーが月曜日にログインし、次に火曜日 (25 時間が経過した後) にログインした場合は、再認証が必要になります。

### <a name="revocation"></a>無効化

更新トークンは、資格情報の変更、または管理者の操作により、サーバーによって取り消される場合があります。  更新トークンは、機密クライアント (右端の列) に対して発行されたクラスと、パブリック クライアント (その他すべての列) に対して発行されたクラスの 2 つのクラスに分類されます。

| Change | パスワードに基づくクッキー | パスワードに基づくトークン | パスワードに基づかないクッキー | パスワードに基づかないトークン | 機密のクライアントのトークン |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| パスワードが期限切れ | 存続 | 存続 | 存続 | 存続 | 存続 |
| ユーザーによるパスワードの変更 | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| ユーザーがSSPRである | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| 管理者によるパスワードのリセット | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| ユーザーが [PowerShellによって、](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken)更新トークンを無効にする | 取り消し | 取り消し | 取り消し | 取り消し | 取り消し |
| 管理者が [PowerShell によって、](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)ユーザーのすべての更新トークンを無効にする | 取り消し | 取り消し |取り消し | 取り消し | 取り消し |
| Web 上でのシングル サインアウト ([v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out)、[v2.0](v2-protocols-oidc.md#single-sign-out)) | 取り消し | 存続 | 取り消し | 存続 | 存続 |

#### <a name="non-password-based"></a>パスワードに基づかない

"*パスワードに基づかない*" ログインは、ユーザーがそれを取得する際にパスワードを入力しないことを意味します。 パスワードに基づかないログインの例を次に示します。

- 顔を使用する Windows Hello
- FIDO2 キー
- SMS
- 音声
- PIN

プライマリ更新トークンの詳細については、[プライマリ更新トークン](../devices/concept-primary-refresh-token.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure AD の `id_tokens`](id-tokens.md) の詳細を確認します。
* アクセス許可と同意 ([v1.0](../azuread-dev/v1-permissions-consent.md)、[v2.0](v2-permissions-and-consent.md)) の詳細を確認します。
