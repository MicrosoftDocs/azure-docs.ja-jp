---
title: ブラウザーなしでユーザーをサインインさせる | Azure
titleSuffix: Microsoft identity platform
description: デバイス許可付与を使用して、埋め込み型およびブラウザーレスの認証フローを構築します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e937955f0b122d3a878141655475f34b051622e7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919241"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft ID プラットフォームと OAuth 2.0 デバイス許可付与フロー

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft ID プラットフォームでは、ユーザーがスマート TV、IoT デバイス、プリンターなどの入力制限のあるデバイスにサインインできるようにする[デバイス許可付与](https://tools.ietf.org/html/rfc8628)がサポートされています。  このフローを実現するには、デバイスのユーザーが別のデバイス上のブラウザーを使って Web ページにアクセスしてサインインするようにします。  ユーザーがサインインすると、デバイスは必要に応じてアクセス トークンと更新トークンを取得できます。  

この記事では、アプリケーションでプロトコルに対して直接プログラミングする方法について説明します。  可能な場合は、[トークンを取得してセキュリティで保護された Web API を呼び出す](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)代わりに、サポートされている Microsoft 認証ライブラリ (MSAL) を使用することをお勧めします。  また、[MSAL を使用するサンプル アプリ](sample-v2-code.md)も参照してください。

> [!NOTE]
> Microsoft ID プラットフォームのエンドポイントでは、すべての Azure Active Directory シナリオや機能がサポートされているわけではありません。 Microsoft ID プラットフォームのエンドポイントを使用する必要があるかどうかを判断するには、[Microsoft ID プラットフォームの制限事項](active-directory-v2-limitations.md)に関する記事を参照してください。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

全体的なデバイス コード フローは、次のダイアグラムのようになります。 それぞれの手順については、この記事で後述します。

![デバイス コード フロー](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>デバイス承認要求

クライアントはまず、認証を開始するために使用するデバイスとユーザー コードを認証サーバーに確認する必要があります。 クライアントはこの要求を `/devicecode` エンドポイントから収集します。 クライアントは、この要求に、ユーザーから取得する必要のあるアクセス許可も含める必要があります。 ユーザーはこの要求が送信された時点から 15 分以内にサインインしなければならないので (`expires_in` の通常の値)、ユーザーがサインインする準備ができたことを示したときにのみこの要求を行います。

> [!TIP]
> を必ず置き換えてください)。
> [![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| `tenant` | 必須 | /common、/consumers、または /organizations が可能です。  GUID またはフレンドリ名の形式でアクセス許可を要求するディレクトリ テナントを指定することもできます。  |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた**アプリケーション (クライアント) ID**。 |
| `scope` | 推奨 | ユーザーに同意を求める [スコープ](v2-permissions-and-consent.md) の、スペースで区切られたリスト。  |

### <a name="device-authorization-response"></a>デバイス承認応答

正常な応答は、ユーザーがサインインするために必要な情報が含まれている JSON オブジェクトです。  

| パラメーター | 形式 | 説明 |
| ---              | --- | --- |
|`device_code`     | string | クライアントと承認サーバー間のセッションを検証するために使用される長い文字列。 クライアントはこのパラメーターを使用して、承認サーバーにアクセス トークンを要求します。 |
|`user_code`       | string | ユーザーに表示される短い文字列。セカンダリ デバイス上のセッションを識別するために使用されます。|
|`verification_uri`| URI | ユーザーがサインインするために `user_code` を使用してアクセスする必要がある URI。 |
|`expires_in`      | int | `device_code` と `user_code` の有効期限か切れるまでの秒数。 |
|`interval`        | int | ポーリング要求の間にクライアントが待機する秒数。 |
| `message`        | string | ユーザーのための指示が含まれている、人間が判読可能な文字列。 これは、`?mkt=xx-XX` という形式で**クエリ パラメーター**を要求に含め、適切な言語カルチャ コードを入力することで、ローカライズできます。 |

> [!NOTE]
> `verification_uri_complete` 応答フィールドは現時点では含まれておらず、サポートされていません。  このことに触れる理由は、[標準](https://tools.ietf.org/html/rfc8628)を読むと、デバイス コード フロー標準のオプションの部分として `verification_uri_complete` が示されているためです。

## <a name="authenticating-the-user"></a>ユーザーの認証

`user_code` と `verification_uri` を受け取ると、クライアントによりこれらがユーザーに表示され、携帯電話または PC のブラウザーを使ってサインインするよう指示されます。

ユーザーが個人のアカウント (/common または /consumers) で認証された場合は、認証状態をデバイスに転送するためにもう一度サインインするように求められます。  また、アクセス許可が付与されることを承知していることを確認するために、同意を求めるメッセージも表示されます。  これは、認証に使用される職場または学校のアカウントには適用されません。 

ユーザーが `verification_uri` で認証している間、クライアントは `device_code` を使用して要求されたトークンを取得するために `/token` エンドポイントをポーリングする必要があります。

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| パラメーター | 必須 | 説明|
| -------- | -------- | ---------- |
| `tenant`  | 必須 | 初期要求で使用されているのと同じテナントまたはテナント エイリアス。 | 
| `grant_type` | 必須 | `urn:ietf:params:oauth:grant-type:device_code` である必要があります。|
| `client_id`  | 必須 | 最初の要求で使用された `client_id` と一致する必要があります。 |
| `device_code`| 必須 | デバイス承認要求に対して返された `device_code`。  |

### <a name="expected-errors"></a>予期されるエラー

デバイス コード フローはポーリング プロトコルなので、クライアントでは、ユーザーが認証を完了する前にエラーが発生することを想定しておく必要があります。  

| Error | 説明 | クライアント側の処理 |
| ------ | ----------- | -------------|
| `authorization_pending` | ユーザーはまだ認証を完了していませんが、フローを取り消していません。 | 少なくとも `interval` 秒後に要求を繰り返します。 |
| `authorization_declined` | エンド ユーザーが承認要求を拒否しました。| ポーリングを停止し、未認証の状態に戻します。  |
| `bad_verification_code`| `/token` エンドポイントに送信された `device_code` が認識されませんでした。 | クライアントが要求時に正しい `device_code` を送信していることを確認します。 |
| `expired_token` | 少なくとも `expires_in` 秒が経過したので、この `device_code` を使って認証することはもうできません。 | ポーリングを停止し、未認証の状態に戻します。 |   

### <a name="successful-authentication-response"></a>正常な認証応答

正常なトークン応答は次のようになります。

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| パラメーター | 形式 | 説明 |
| --------- | ------ | ----------- |
| `token_type` | string| 常に "Bearer" です。 |
| `scope` | スペース区切りの文字列 | アクセス トークンが返された場合、アクセス トークンが有効なスコープがリストされます。 |
| `expires_in`| int | 含まれているアクセス トークンが有効になるまでの秒数。 |
| `access_token`| 不透明な文字列 | 要求された[スコープ](v2-permissions-and-consent.md)に対して発行されます。  |
| `id_token`   | JWT | 元の `scope` パラメーターに `openid` スコープが含まれている場合に発行されます。  |
| `refresh_token` | 不透明な文字列 | 元の `scope` パラメーターに `offline_access` が含まれている場合に発行されます。  |

[OAuth コード フローのドキュメント](v2-oauth2-auth-code-flow.md#refresh-the-access-token)で説明されているのと同じフローに従い、更新トークンを使用して新しいアクセス トークンと更新トークンを取得できます。  
