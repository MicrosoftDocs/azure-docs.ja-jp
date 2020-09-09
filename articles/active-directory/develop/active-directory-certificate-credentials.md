---
title: Microsoft ID プラットフォームの証明書資格情報
titleSuffix: Microsoft identity platform
description: この記事では、アプリケーションを認証するための証明書資格情報の登録と使用について説明します。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6330621aac78d5e9df52f2cd3ad9c3968bb0120d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853376"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft ID プラットフォーム アプリケーションの認証証明書資格情報

Microsoft ID プラットフォームでは、OAuth 2.0 [クライアント資格情報付与](v2-oauth2-client-creds-grant-flow.md)フローや [On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) (OBO) フローなどで、アプリケーションが認証用の独自の資格情報を使用することが許可されています。

アプリケーションが認証を行うために使用できる資格情報の 1 つの形式は、アプリケーションが所有する証明書を使用して署名された [JSON Web トークン](./security-tokens.md#json-web-tokens-jwts-and-claims) (JWT) アサーションです。

## <a name="assertion-format"></a>アサーションの形式

アサーションを計算するには、自分が選択した言語の多数の JWT ライブラリの中からいずれかを使用できます。 この情報は、トークンによって[ヘッダー](#header)、[要求](#claims-payload)、および[署名](#signature)で伝達されます。

### <a name="header"></a>ヘッダー

| パラメーター |  注記 |
| --- | --- |
| `alg` | **RS256** です |
| `typ` | **JWT** です |
| `x5t` | Base64 文字列値としてエンコードされた X.509 証明書ハッシュ (証明書の SHA-1 "*サムプリント*" とも呼ばれる) の 16 進数表現。 たとえば、X.509 証明書ハッシュ `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (Hex) を指定した場合、`x5t` 要求は `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64) になります。 |

### <a name="claims-payload"></a>要求 (ペイロード)

| パラメーター |  解説 |
| --- | --- |
| `aud` | Audience:`https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` である必要があります。 |
| `exp` | 有効期限: トークンの有効期限が切れる日付。 日時は、UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) からトークンが有効期限切れになるまでの秒数で表されます。 短い有効期限 (10 分から 1 時間) を使用することをお勧めします。|
| `iss` | 発行者:client_id (クライアント サービスの "*アプリケーション (クライアント) ID*") である必要があります。 |
| `jti` | GUID: JWT ID |
| `nbf` | 期間の開始日時: トークンの使用開始日。 時刻は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、アサーションが作成された時刻までの秒数で表されます。 |
| `sub` | 件名:`iss` の場合、client_id (クライアント サービスの "*アプリケーション (クライアント) ID*") である必要があります。 |

### <a name="signature"></a>署名

署名は、[JSON Web トークン RFC7519 仕様](https://tools.ietf.org/html/rfc7519)の説明に従って証明書を適用することによって計算されます。

## <a name="example-of-a-decoded-jwt-assertion"></a>デコードされた JWT アサーションの例

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>エンコードされた JWT アサーションの例

次の文字列は、エンコードされたアサーションの例です。 よく見ると、ドット (`.`) で区切られた 3 つのセクションがあることがわかります。

* 最初のセクションは、"*ヘッダー*" をエンコードしています。
* 2 番目のセクションは、"*要求*" (ペイロード) をエンコードしています。
* 最後のセクションは、最初の 2 つのセクションのコンテンツからの証明書を使用して計算された "*署名*" です。

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Microsoft ID プラットフォームに証明書を登録する

次のいずれかの方法を使用して、Azure Portal 経由で Microsoft ID プラットフォームのクライアント アプリケーションに証明書資格情報を関連付けることができます。

### <a name="uploading-the-certificate-file"></a>証明書ファイルのアップロード

クライアント アプリケーションの Azure アプリ登録で、以下を実行します。
1. **[証明書とシークレット]** を選択します。
2. **[証明書のアップロード]** をクリックし、アップロードする証明書ファイルを選択します。
3. **[追加]** をクリックします。
  証明書がアップロードされると、サムプリント、開始日、有効期限の値が表示されます。

### <a name="updating-the-application-manifest"></a>アプリケーション マニフェストの更新

証明書を入手したら、次を計算する必要があります。

- `$base64Thumbprint` - Base64 でエンコードされた証明書ハッシュの値
- `$base64Value` - Base64 でエンコードされた証明書生データの値

また、GUID を指定して、アプリケーション マニフェストでキーを特定する必要もあります (`$keyId`)。

クライアント アプリケーションの Azure アプリ登録で、以下を実行します。
1. **[マニフェスト]** を選択して、アプリケーション マニフェストを開きます。
2. 次のスキーマを使用して、*keyCredentials* プロパティを新しい証明書情報に置き換えます。

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. 編集内容をアプリケーション マニフェストに保存した後、そのマニフェストを Microsoft ID プラットフォームにアップロードします。

   `keyCredentials` プロパティは複数値であるため、複数の証明書をアップロードして、高度なキー管理を行うこともできます。

## <a name="next-steps"></a>次の手順

GitHub の [Microsoft ID プラットフォームを使用した .NET Core デーモン コンソール アプリケーション](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)のコード サンプルは、アプリケーションで独自の資格情報が認証に使用される方法を示しています。 また、`New-SelfSignedCertificate` PowerShell コマンドレットを使用した[自己署名証明書の作成](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script)方法も示しています。 さらに、サンプル リポジトリ内の[アプリ作成スクリプト](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md)を使用して、証明書の作成やサムプリントの計算などを実行することもできます。
