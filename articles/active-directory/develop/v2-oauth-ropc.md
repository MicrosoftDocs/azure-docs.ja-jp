---
title: リソース所有者のパスワード資格情報付与を使用してサインインする | Azure
titleSuffix: Microsoft identity platform
description: リソース所有者のパスワード資格情報 (ROPC) 付与を使用して、ブラウザーなしの認証フローをサポートします。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bf469b79fa532978e904a54f32c80280706ee7cb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174582"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft ID プラットフォームと OAuth 2.0 リソース所有者のパスワード資格情報

Microsoft ID プラットフォームでは、[OAuth 2.0 リソース所有者のパスワード資格情報 (ROPC) 付与](https://tools.ietf.org/html/rfc6749#section-4.3)がサポートされています。これにより、アプリケーションでは、ユーザーのパスワードを直接処理することでユーザーをサインインさせることができます。  この記事では、アプリケーションでプロトコルに対して直接プログラミングする方法について説明します。  可能な場合は、[トークンを取得してセキュリティで保護された Web API を呼び出す](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)代わりに、サポートされている Microsoft 認証ライブラリ (MSAL) を使用することをお勧めします。  また、[MSAL を使用するサンプル アプリ](sample-v2-code.md)も参照してください。

> [!WARNING]
> ROPC フローは "_使用しない_" ことをお勧めします。 ほとんどのシナリオでは、より安全な代替手段を利用でき、推奨されます。 このフローでは、アプリケーションで非常に高い信頼度が要求されるため、他のフローには存在しないリスクが伴います。 他のもっと安全なフローを使用できない場合にのみ、このフローを使用する必要があります。

> [!IMPORTANT]
>
> * Microsoft ID プラットフォームでは、Azure AD テナントに対してのみ ROPC をサポートしています。個人アカウントは対象外です。 そのため、テナント固有のエンドポイント (`https://login.microsoftonline.com/{TenantId_or_Name}`) または `organizations` エンドポイントを使用する必要があります。
> * Azure AD テナントに招待された個人アカウントでは、ROPC を使用できません。
> * パスワードがないアカウントでは、ROPC 経由でサインインできません。 このシナリオでは、代わりに、自分のアプリに対して別のフローを使用することをお勧めします。
> * ユーザーが[多要素認証 (MFA)](../authentication/concept-mfa-howitworks.md) を使用してアプリケーションにログインすると、ログインできずにブロックされます。
> * ROPC は[ハイブリッド ID フェデレーション](../hybrid/whatis-fed.md) シナリオ (たとえば、オンプレミスのアカウントの認証に使用される Azure AD や ADFS) ではサポートされていません。 ユーザーがオンプレミスの ID プロバイダーに全ページ リダイレクトされる場合、Azure AD ではその ID プロバイダーに対してユーザー名とパスワードをテストできません。 ただし、ROPC では[パススルー認証](../hybrid/how-to-connect-pta.md)がサポートされています。
> * ハイブリッド ID フェデレーション シナリオの例外は次のとおりです。AllowCloudPasswordValidation のホーム領域の検出ポリシーを TRUE に設定すると、オンプレミス パスワードがクラウドと同期するときに、ROPC フローはフェデレーション ユーザーに対して機能します。 詳細については、[レガシ アプリケーションに対するフェデレーション ユーザーの直接 ROPC 認証を有効にする方法](../manage-apps/configure-authentication-for-federated-users-portal.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications)に関する記事を参照してください。

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

次は ROPC フローの図です。

![リソース所有者のパスワード資格情報フローを示す図](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Authorization request (承認要求)

ROPC フローは 1 件の要求です。クライアント ID とユーザーの資格情報を IDP に送信し、返されたトークンを受信します。 クライアントでは、これを行う前に、ユーザーの電子メール アドレス (UPN) とパスワードを要求する必要があります。 クライアントでは、要求が成功した直後にユーザーの資格情報を安全な方法でメモリから解放する必要があります。 保存は厳禁です。

> [!TIP]
> を必ず置き換えてください)。
> [![Postman でこの要求を実行してみる](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| `tenant` | 必須 | ユーザーをログインさせるディレクトリ テナント。 これは GUID またはフレンドリ名の形式で指定できます。 このパラメーターは `common` と `consumers` に設定できませんが、`organizations` には設定できます。 |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページでアプリに割り当てられたアプリケーション (クライアント) ID。 |
| `grant_type` | 必須 | `password` に設定する必要があります。 |
| `username` | 必須 | ユーザーの電子メール アドレス。 |
| `password` | 必須 | ユーザーのパスワード。 |
| `scope` | 推奨 | アプリで必要となる[スコープ](v2-permissions-and-consent.md) (アクセス許可) をスペースで区切った一覧。 対話型のフローでは、管理者またはユーザーが事前にこれらのスコープに同意する必要があります。 |
| `client_secret`| 必要な場合あり | アプリがパブリック クライアントである場合、`client_secret` または `client_assertion` を含めることはできません。  アプリが機密クライアントである場合は、それを含める必要があります。 |
| `client_assertion` | 必要な場合あり | 証明書を使用して生成された、`client_secret` の別の形式。  詳細については、[証明書の資格情報](active-directory-certificate-credentials.md)に関する記事を参照してください。 |

### <a name="successful-authentication-response"></a>正常な認証応答

トークンの応答の成功例を次に示します。

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

| パラメーター | Format | 説明 |
| --------- | ------ | ----------- |
| `token_type` | String | 常に `Bearer` に設定します。 |
| `scope` | スペース区切りの文字列 | アクセス トークンが返された場合、このパラメーターによって、アクセス トークンが有効なスコープの一覧が生成されます。 |
| `expires_in`| INT | 含まれているアクセス トークンが有効となる秒数。 |
| `access_token`| 不透明な文字列 | 要求された[スコープ](v2-permissions-and-consent.md)に対して発行されます。 |
| `id_token` | JWT | 元の `scope` パラメーターに `openid` スコープが含まれている場合に発行されます。 |
| `refresh_token` | 不透明な文字列 | 元の `scope` パラメーターに `offline_access` が含まれている場合に発行されます。 |

[OAuth コード フローのドキュメント](v2-oauth2-auth-code-flow.md#refresh-the-access-token)で説明されているのと同じフローに従い、更新トークンを使用して新しいアクセス トークンと更新トークンを取得できます。

### <a name="error-response"></a>エラー応答

ユーザーが正しいユーザー名やパスワードを指定していない場合、あるいは要求した同意がクライアントに届いていない場合、認証は失敗します。

| エラー | 説明 | クライアント側の処理 |
|------ | ----------- | -------------|
| `invalid_grant` | 認証に失敗しました | 資格情報が正しくないか、要求したスコープに対してクライアントに同意がありません。 スコープが付与されていない場合、`consent_required` エラーが返されます。 その場合、クライアントでは、Web ビューまたはブラウザーを利用し、対話式プロンプトにユーザーを送信する必要があります。 |
| `invalid_request` | 要求が正しく構築されていません | この付与タイプは `/common` または `/consumers` 認証ではサポートされていません。  代わりに `/organizations` またはテナント ID を使用してください。 |

## <a name="learn-more"></a>詳細情報

ROPC の使用例については、GitHub で [.NET Core コンソール アプリケーション](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)のコード サンプルをご覧ください。
