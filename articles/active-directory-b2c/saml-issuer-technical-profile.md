---
title: カスタム ポリシーで SAML 発行者用の技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで Security Assertion Markup Language (SAML) トークン発行者用の技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 54869c14cf7c5a7e43f34102f5c95e37689dfee8
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095342"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで SAML トークン発行者用の技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) は、各認証フローを処理するときにいくつかの種類のセキュリティ トークンを出力します。 SAML トークン発行者の技術プロファイルは、証明書利用者アプリケーション (サービス プロバイダー) に返される SAML トークンを出力します。 通常では、この技術プロファイルは、ユーザー体験での最後のオーケストレーション手順です。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `SAML2` に設定する必要があります。 **OutputTokenFormat** 要素を `SAML2` に設定します。

次の例は、`Saml2AssertionIssuer` 用の技術プロファイルを示しています。

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>入力、出力、永続化要求

**InputClaims**、**OutputClaims**、**PersistClaims** の要素は空であるか、存在しません。 **InutputClaimsTransformations** と **OutputClaimsTransformations** の要素も存在しません。

## <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| IssuerUri | いいえ | SAML 応答に表示される発行者名。 この値は、証明書利用者アプリケーションに構成されているものと同じ名前にする必要があります。 |
| XmlSignatureAlgorithm | いいえ | SAML アサーションに署名するために Azure AD B2C で使用されるメソッド。 指定できる値: `Sha256`、`Sha384`、`Sha512`、または `Sha1`。 両方の側で同じ値の署名アルゴリズムを構成するようにします。 証明書でサポートされているアルゴリズムのみを使用してください。 SAML 応答を構成するには、[SAML アプリケーションを登録するためのオプション](saml-service-provider.md)に関するページを参照してください。|
|TokenNotBeforeSkewInSeconds| いいえ| 有効期間の開始をマークするタイムスタンプの傾斜を整数で指定します。 この数値が大きいほど、証明書利用者に対して要求が発行された時間について、有効期間がより遡って開始されます。 たとえば、TokenNotBeforeSkewInSeconds が 60 秒に設定されていて、トークンが 13:05:10 UTC に発行された場合、トークンは 13:04:10 UTC から有効になります。 既定値は 0 です。 最大値は 3600 (1 時間) です。 |
|TokenLifeTimeInSeconds| いいえ| SAML アサーションの有効期間を指定します。 この値は、上記の NotBefore 値からの秒数です。既定値は 300 秒 (5 分) です。 |


## <a name="cryptographic-keys"></a>暗号化キー

CryptographicKeys 要素には次の属性が存在します。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| MetadataSigning | はい | SAML データを署名するために使用する X509 証明書 (RSA キー セット)。 Azure AD B2C では、このキーを使用して、メタデータに署名します。 |
| SamlMessageSigning| はい| SAML メッセージに署名するために使用する X509 証明書 (RSA キー セット) を指定します。 Azure AD B2C は、このキーを使用して、証明書利用者に送信する `<samlp:Response>` 応答に署名します。|

## <a name="session-management"></a>セッションの管理

証明書利用者アプリケーション間の Azure AD B2C SAML セッションを構成するには、`UseTechnicalProfileForSessionManagement` 要素の属性で [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO セッションを参照します。

## <a name="next-steps"></a>次のステップ

SAML 発行者の技術プロファイルの使用例については、次の記事を参照してください。

- [SAML アプリケーションを Azure AD B2C に登録する](saml-service-provider.md)

