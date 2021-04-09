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
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfbcc8523ff1d5858317a3654b58ec7b2d23607a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582028"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft ID プラットフォーム アプリケーションの認証証明書資格情報

Microsoft ID プラットフォームでは、OAuth 2.0 [クライアント資格情報付与](v2-oauth2-client-creds-grant-flow.md)フローや [On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) (OBO) フローなど、クライアント シークレットを使用できるあらゆる場所で、アプリケーションが認証用の独自の資格情報を使用することが許可されています。

アプリケーションが認証を行うために使用できる資格情報の 1 つの形式は、アプリケーションが所有する証明書を使用して署名された [JSON Web トークン](./security-tokens.md#json-web-tokens-and-claims) (JWT) アサーションです。

## <a name="assertion-format"></a>アサーションの形式

アサーションを計算するには、自分が選択した言語の多数ある JWT ライブラリの中からいずれかを使用できます。[MSAL は、`.WithCertificate()` のこのような使用をサポートしています](msal-net-client-assertions.md)。 この情報は、トークンによって[ヘッダー](#header)、[要求](#claims-payload)、および[署名](#signature)で伝達されます。

### <a name="header"></a>ヘッダー

| パラメーター |  注記 |
| --- | --- |
| `alg` | **RS256** です |
| `typ` | **JWT** です |
| `x5t` | Base64url 文字列値としてエンコードされた X.509 証明書ハッシュ (証明書の SHA-1 "*サムプリント*" とも呼ばれる) の 16 進数表現。 たとえば、X.509 証明書ハッシュ `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (Hex) を指定した場合、`x5t` 要求は `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url) になります。 |

### <a name="claims-payload"></a>要求 (ペイロード)

要求の種類 | 値 | 説明
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | "aud" (対象ユーザー) 要求では、JWT が意図する受信者 (ここでは Azure AD) を指定します。[[RFC 7519、セクション 4.1.3]](https://tools.ietf.org/html/rfc7519#section-4.1.3) を参照してください。  この場合、その受信者はログイン サーバー (login.microsoftonline.com) です。
exp | 1601519414 | "exp" (有効期限) 要求は、JWT の処理を受け入れることができなくなる時刻を指定します。 [[RFC 7519、セクション 4.1.4]](https://tools.ietf.org/html/rfc7519#section-4.1.4) を参照してください。  これにより、そのときまでアサーションを使用できるようになるため、間隔を短く (最大でも `nbf` の 5 ～ 10 分後に) してください。  Azure AD では、現在 `exp` の時刻に制限は設定されていません。 
iss | {ClientID} | "iss" (発行者) 要求では、JWT を発行したプリンシパル (この場合はクライアント アプリケーション) を指定します。  GUID (アプリケーション ID) を使用します。
jti | (Guid) | "jti" (JWT ID) 要求は、JWT の一意の識別子を提供します。 識別子の値は、同じ値が誤って異なるデータ オブジェクトに割り当てられる可能性が無視できるほど低くなる方法で割り当てる必要があります。複数の発行者を使用するアプリケーションの場合は、異なる発行者が生成した値が競合しないように防ぐ必要があります。 "jti" 値は大文字と小文字が区別される文字列です。 [[RFC 7519、セクション 4.1.7]](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | "nbf" (指定時刻よりも後) 要求では、指定した時刻よりも後に JWT の処理を受け入れることができるようになります。 [[RFC 7519、セクション 4.1.5]](https://tools.ietf.org/html/rfc7519#section-4.1.5)  現在の時刻を使用するのが適切です。 
sub | {ClientID} | "sub" (主題) 要求では、JWT の件名 (この場合はお使いのアプリケーション) を指定します。 `iss` と同じ値を使用します。 

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

証明書を取得した後、これらの値を計算します。

- `$base64Thumbprint` - Base64 でエンコードされた証明書ハッシュの値
- `$base64Value` - Base64 でエンコードされた証明書生データの値

GUID を指定して、アプリケーション マニフェストでキーを特定します (`$keyId`)。

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
   
## <a name="using-a-client-assertion"></a>クライアント アサーションの使用

クライアント アサーションは、クライアント シークレットが使用されるあらゆる場所で使用できます。  そのため、たとえば、[認証コード フロー](v2-oauth2-auth-code-flow.md)では、`client_secret` を渡して、要求がアプリから送信されていることを証明することができます。 これを `client_assertion` パラメーターと `client_assertion_type` パラメーターに置き換えることができます。 

| パラメーター | 値 | 説明|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| これは固定値であり、証明書の資格情報を使用していることを示します。 |
|`client_assertion`| JWT |これは、上記で作成した JWT です。 |

## <a name="next-steps"></a>次のステップ

1 行のコードで、[このシナリオを MSAL.NET ライブラリで処理](msal-net-client-assertions.md)します。

GitHub の [Microsoft ID プラットフォームを使用した .NET Core デーモン コンソール アプリケーション](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)のコード サンプルは、アプリケーションで独自の資格情報が認証に使用される方法を示しています。 また、`New-SelfSignedCertificate` PowerShell コマンドレットを使用した[自己署名証明書の作成](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script)方法も示しています。 さらに、サンプル リポジトリ内の[アプリ作成スクリプト](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md)を使用して、証明書の作成やサムプリントの計算などを実行することもできます。
