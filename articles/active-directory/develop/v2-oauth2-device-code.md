---
title: Azure AD v2.0 を使用してブラウザーレス デバイスでユーザーをサインインさせる | Microsoft Docs
description: デバイス コード付与を使用して、埋め込み型およびブラウザーレスの認証フローを構築します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f34796a98acb3ed4241edff57e506990479d1a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443268"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-device-code-flow"></a>Azure Active Directory v2.0 と OAuth 2.0 のデバイス コード フロー

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Azure AD では、ユーザーがスマート TV、IoT デバイス、プリンターなどの入力制限のあるデバイスにサインインできるようにする[デバイス コード付与](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12)がサポートされています。  このフローを実現するには、デバイスのユーザーが別のデバイス上のブラウザーを使って Web ページにアクセスしてサインインするようにします。  ユーザーがサインインすると、デバイスは必要に応じてアクセス トークンと更新トークンを取得できます。  

> [!Important] 
> 現時点では、v2.0 エンドポイントでサポートされるのは Azure AD テナントのデバイス フローのみで、個人アカウントはサポートされません。  そのため、テナントとして設定されたエンドポイントまたは組織のエンドポイントを使用する必要があります。  
>
> Azure AD テナントに招待された個人アカウントはデバイス フロー付与を使用できますが、テナントのコン テキストに限定されます。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。 v2.0 エンドポイントを使用するかどうかを判断するには、[v2.0 の制限](active-directory-v2-limitations.md)に関する記事を参照してください。
>

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

全体的なデバイス コード フローは、次のダイアグラムのようになります。 それぞれの手順については、この記事で後述します。

![デバイス コード フロー](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>デバイス承認要求

クライアントはまず、認証を開始するために使用するデバイスとユーザー コードを認証サーバーに確認する必要があります。  クライアントはこの要求を `/devicecode` エンドポイントから収集します。 クライアントは、この要求に、ユーザーから取得する必要のあるアクセス許可も含める必要があります。  ユーザーはこの要求が送信された時点から 15 分以内にサインインしなければならないので (`expires_in` の通常の値)、ユーザーがサインインする準備ができたことを示したときにのみこの要求を行います。

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| tenant |必須 |アクセス許可を要求するディレクトリ テナント。 これは GUID またはフレンドリ名の形式で指定できます。  |
| client_id |必須 |[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)によってアプリに割り当てられたアプリケーション ID。 |
| scope | 推奨 | ユーザーに同意を求める [スコープ](v2-permissions-and-consent.md) の、スペースで区切られたリスト。  |

### <a name="device-authorization-response"></a>デバイス承認応答

正常な応答は、ユーザーがサインインするために必要な情報が含まれている JSON オブジェクトです。  

| パラメーター | 形式 | 説明 |
| ---              | --- | --- |
|`device_code`     |String| クライアントと承認サーバー間のセッションを検証するために使用される長い文字列。  これは、クライアントが承認サーバーにアクセス トークンを要求するために使用されます。 |
|`user_code`       |String| ユーザーに表示される短い文字列。セカンダリ デバイス上のセッションを識別するために使用されます。|
|`verification_uri`|URI| ユーザーがサインインするために `user_code` を使用してアクセスする必要がある URI。 |
|`verification_uri_complete`|URI| `user_code` と `verification_uri` を組み合わせた URI。テキスト以外の形式でユーザーに送信するために使用されます (たとえば、Bluetooth 経由でデバイスに送信したり、QR コードを使ったりします)。  |
|`expires_in`      |int| `device_code` と `user_code` の有効期限か切れるまでの秒数。 |
|`interval`        |int| ポーリング要求の間にクライアントが待機する秒数。 |
| `message`        |String| ユーザーのための指示が含まれている、人間が判読可能な文字列。  これは、`?mkt=xx-XX` という形式で**クエリ パラメーター**を要求に含め、適切な言語カルチャ コードを入力することで、ローカライズできます。 |

## <a name="authenticating-the-user"></a>ユーザーの認証

`user_code` と `verification_uri` を受け取ると、クライアントはこれらをユーザーに表示し、携帯電話または PC のブラウザーを使ってログインするよう指示します。  また、クライアントは QR コードや類似のメカニズムを使って `verfication_uri_complete` を表示することもできます。その場合は、ユーザーの代わりに `user_code` を入力する手順が実行されます。

ユーザーが `verification_uri` で認証している間、クライアントは `device_code` を使用して要求されたトークンを取得するために `/token` エンドポイントをポーリングする必要があります。

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

|パラメーター | 必須 | 説明|
| -------- | -------- | ---------- |
|`grant_type` | 必須| `urn:ietf:params:oauth:grant-type:device_code` である必要があります。|
|`client_id`  | 必須| 最初の要求で使用された `client_id` と一致する必要があります。 |
|`device_code`| 必須| デバイス承認要求に対して返された `device_code`。  |

### <a name="expected-errors"></a>予期されるエラー

デバイス コード フローはポーリング プロトコルなので、クライアントでは、ユーザーが認証を完了する前にエラーが発生することを想定しておく必要があります。  

| Error | 説明 | クライアント側の処理 |
|------ | ----------- | -------------|
| `authorization_pending` |  ユーザーはまだ認証を完了していませんが、フローを取り消していません。 | 少なくとも `interval` 秒後に要求を繰り返します。 |
| `authorization_declined`|  エンド ユーザーが承認要求を拒否しました。| ポーリングを停止し、未認証の状態に戻します。  |
| `bad_verification_code`|`/token` エンドポイントに送信された `device_code` が認識されませんでした。 | クライアントが要求時に正しい `device_code` を送信していることを確認します。 |
| `expired_token`|  少なくとも `expires_in` 秒が経過したので、この `device_code` を使って認証することはもうできません。 | ポーリングを停止し、未認証の状態に戻します。 |


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
|`token_type` | String| 常に "Bearer" です。 |
|`scope` | スペース区切りの文字列 | アクセス トークンが返された場合、アクセス トークンが有効なスコープがリストされます。 |
|`expires_in`| int | 含まれているアクセス トークンが有効になるまでの秒数。 |
|`access_token`| 不透明な文字列 | 要求された[スコープ](v2-permissions-and-consent.md)に対して発行されます。  |
|`id_token`   | JWT | 元の `scope` パラメーターに `openid` スコープが含まれている場合に発行されます。  |
|`refresh_token` | 不透明な文字列 | 元の `scope` パラメーターに `offline_access` が含まれている場合に発行されます。  |

[OAuth コード フローのドキュメント](v2-oauth2-auth-code-flow.md#refresh-the-access-token)で詳しく説明されているのと同じフローに従い、更新トークンを使って、新しいアクセス トークンと更新トークンを取得できます。  
