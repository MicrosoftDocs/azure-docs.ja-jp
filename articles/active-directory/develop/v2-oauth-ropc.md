---
title: Azure AD v2.0 を使用して ROPC でユーザーをサインインさせる | Microsoft Docs
description: リソース所有者のパスワード資格情報 (ROPC) 付与によるブラウザーなしの認証フローをサポートします。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: df9073bbf9789875c373bb7093ab1878a20c399f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274189"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-resource-owner-password-credential"></a>Azure Active Directory v2.0 と OAuth 2.0 リソース所有者のパスワード資格情報

Azure Active Directory (Azure AD) では、[リソース所有者のパスワード資格情報 (ROPC) 付与](https://tools.ietf.org/html/rfc6749#section-4.3)がサポートされています。これにより、アプリケーションでは、ユーザーのパスワードを直接処理することでユーザーをサインインさせることができます。 ROPC フローには、高い度合の信頼が必要であり、また、ユーザーを公開する必要があります。他のより安全なフローを使用できない場合にのみ、開発者はこのフローを使用するべきです。

> [!Important]
> * Azure AD v2.0 エンドポイントでは、Azure AD テナントに対してのみ ROPC をサポートしています。個人アカウントは対象外です。 そのため、テナント固有のエンドポイント (`https://login.microsoftonline.com/{TenantId_or_Name}`) または `organizations` エンドポイントを使用する必要があります。
> * Azure AD テナントに招待された個人アカウントでは、ROPC を使用できません。
> * パスワードがないアカウントでは、ROPC 経由でサインインできません。 このシナリオでは、代わりに、自分のアプリに対して別のフローを使用することをお勧めします。
> * ユーザーが多要素認証 (MFA) を使用してアプリケーションにログインすると、ログインできずにブロックされます。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

次は ROPC フローの図です。

![ROPC フロー](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Authorization request (承認要求)

ROPC フローは 1 件の要求です。クライアント ID とユーザーの資格情報を IDP に送信し、返されたトークンを受信します。 クライアントでは、これを行う前に、ユーザーの電子メール アドレス (UPN) とパスワードを要求する必要があります。 クライアントでは、要求が成功した直後にユーザーの資格情報を安全な方法でメモリから解放する必要があります。 保存は厳禁です。

```
// Line breaks and spaces are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token?

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| `tenant` | 必須 | ユーザーをログインさせるディレクトリ テナント。 これは GUID またはフレンドリ名の形式で指定できます。 このパラメーターは `common` と `consumers` に設定できませんが、`organizations` には設定できます。 |
| `grant_type` | 必須 | `password` に設定する必要があります。 |
| `username` | 必須 | ユーザーの電子メール アドレス。 |
| `password` | 必須 | ユーザーのパスワード。 |
| `scope` | 推奨 | アプリで必要となる[スコープ](v2-permissions-and-consent.md) (アクセス許可) をスペースで区切った一覧。 このようなスコープには、管理者またはユーザーが対話フローで事前に同意する必要があります。 |

### <a name="successful-authentication-response"></a>正常な認証応答

次は成功時のトークン応答の例です。

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
| `token_type` | String | 常に `Bearer` に設定します。 |
| `scope` | スペース区切りの文字列 | アクセス トークンが返された場合、このパラメーターによって、アクセス トークンが有効なスコープの一覧が生成されます。 |
| `expires_in`| int | 含まれているアクセス トークンが有効となる秒数。 |
| `access_token`| 不透明な文字列 | 要求された[スコープ](v2-permissions-and-consent.md)に対して発行されます。 |
| `id_token` | JWT | 元の `scope` パラメーターに `openid` スコープが含まれている場合に発行されます。 |
| `refresh_token` | 不透明な文字列 | 元の `scope` パラメーターに `offline_access` が含まれている場合に発行されます。 |

[OAuth コード フローのドキュメント](v2-oauth2-auth-code-flow.md#refresh-the-access-token)で説明されているのと同じフローに従い、更新トークンを使用して新しいアクセス トークンと更新トークンを取得できます。

### <a name="error-response"></a>エラー応答

ユーザーが正しいユーザー名やパスワードを指定していない場合、あるいは要求した同意がクライアントに届いていない場合、認証は失敗します。

| Error | 説明 | クライアント側の処理 |
|------ | ----------- | -------------|
| `invalid_grant` | 認証に失敗しました | 資格情報が正しくないか、要求したスコープに対してクライアントに同意がありません。 スコープが付与されていない場合、`consent_required` サブエラーが返されます。 その場合、クライアントでは、Web ビューまたはブラウザーを利用し、対話式プロンプトにユーザーを送信する必要があります。 |
| `invalid_request` | 要求が正しく構築されていません | 認証コンテキスト `/common` または `/consumers` では、その付与タイプがサポートされていません。  代わりに `/organizations` を使用してください |
| `invalid_client` | アプリが正しく設定されていません | これは[アプリケーション マニフェスト](reference-app-manifest.md)で `allowPublicClient` プロパティが true に設定されていない場合に発生します。 ROPC 付与ではリダイレクト URI がないため、`allowPublicClient` プロパティが必要になります。 Azure AD では、このプロパティが設定されていない限り、クライアント アプリケーションの種類がパブリックかプライベートかを判断できません。 ROPC はパブリック クライアント アプリでのみサポートされていることに注意してください。 |

## <a name="learn-more"></a>詳細情報

* [サンプル コンソール アプリケーション](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)を利用し、ROPC をご自分でお試しください。
* v2.0 エンドポイントを使用するかどうかを判断するには、[v2.0 の制限](active-directory-v2-limitations.md)に関する記事を参照してください。
