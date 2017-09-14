---
title: "Azure AD での証明書資格情報 | Microsoft Docs"
description: "この記事では、アプリケーションを認証するための証明書資格情報の登録と使用について説明します"
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 08bb5140bb35bbd120aaa506afeab8ad247f81e1
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---

# <a name="certificate-credentials-for-application-authentication"></a>アプリケーションを認証するための証明書資格情報

Azure Active Directory では、OAuth 2.0 クライアント資格情報の付与フローや On-Behalf-Of フローなどで、アプリケーションが認証用の独自の資格情報を使用することを許可しています。
使用できる資格情報の 1 つの形式は、アプリケーションが所有している証明書で署名された JSON Web トークン(JWT) アサーションです。

## <a name="format-of-the-assertion"></a>アサーションの形式
アサーションを計算するために、多数の [JSON Web トークン](https://jwt.io/) ライブラリの中から好きな言語を選択して使用できます。 トークンで伝達する情報は次のとおりです。

#### <a name="header"></a>ヘッダー

| パラメーター |  注記 |
| --- | --- | --- |
| `alg` | **RS256** です |
| `typ` | **JWT** です |
| `x5t` | X.509 証明書 SHA-1 サムプリントです |

#### <a name="claims-payload"></a>要求 (ペイロード)

| パラメーター |  注記 |
| --- | --- | --- |
| `aud` | 対象: 必ず **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | 有効期限: トークンの有効期限が切れる日付。 日時は、UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) からトークンが有効期限切れになるまでの秒数で表されます。|
| `iss` | 発行者: client_id (クライアント サービスのアプリケーション ID) です |
| `jti` | GUID: JWT ID |
| `nbf` | 期間の開始時刻: トークンの使用開始日。 日時は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、トークンが発行された日時までの秒数で表されます。 |
| `sub` | 件名: `iss` については、client_id (クライアント サービスのアプリケーション ID) です |

#### <a name="signature"></a>署名
署名は、[JSON Web トークン RFC7519 仕様](https://tools.ietf.org/html/rfc7519)の説明に従って、証明書を適用することで計算されます

### <a name="example-of-a-decoded-jwt-assertion"></a>デコードされた JWT アサーションの例
```
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

### <a name="example-of-an-encoded-jwt-assertion"></a>エンコードされた JWT アサーションの例
次の文字列は、エンコードされたアサーションの例です。 よく見ると、ピリオド (.) で区切られた 3 つのセクションがあることがわかります。
最初のセクションはヘッダーを、2 番目のセクションはペイロードをエンコードします。最後のセクションは、最初の 2 つのセクションのコンテンツの証明書によって計算された署名です。
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Azure AD に証明書を登録する
Azure AD で証明書資格情報をクライアント アプリケーションに関連付けるには、アプリケーション マニフェストを編集する必要があります。
証明書を入手したら、次を計算する必要があります。
- `$base64Thumbprint`。証明書ハッシュの base64 エンコード
- `$base64Value`。証明書生データの base64 エンコード

また、GUID を指定して、アプリケーション マニフェストでキーを特定する必要もあります (`$keyId`)。

クライアント アプリケーションの Azure アプリ登録で、アプリケーション マニフェストを開き、次のスキーマを使用して *keyCredentials* プロパティを新しい証明書情報に置き換えます。
```
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

編集内容をアプリケーション マニフェストに保存し、Azure AD にアップロードします。 keyCredentials プロパティは複数値であるため、複数の証明書をアップロードして、高度なキー管理を行うこともできます。

