---
title: カスタム ポリシーで JWT 発行者用の技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで JSON Web トークン (JWT) 発行者用の技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4faa7e68b50b83368837b75cd04be566d816f6d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102119809"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで JWT トークン発行者用の技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) は、各認証フローを処理するときにいくつかの種類のセキュリティ トークンを出力します。 JWT トークン発行者用の技術プロファイルは、証明書利用者のアプリケーションに返却される JWT トークンを発行します。 通常では、この技術プロファイルは、ユーザー体験での最後のオーケストレーション手順です。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `OpenIdConnect` に設定する必要があります。 **OutputTokenFormat** 要素を `JWT` に設定します。

次の例は、`JwtIssuer` 用の技術プロファイルを示しています。

```xml
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  <Metadata>
    <Item Key="client_id">{service:te}</Item>
    <Item Key="issuer_refresh_token_user_identity_claim_type">objectId</Item>
    <Item Key="SendTokenResponseBodyWithJsonNumbers">true</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
    <Key Id="issuer_refresh_token_key" StorageReferenceId="B2C_1A_TokenEncryptionKeyContainer" />
  </CryptographicKeys>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-jwt-issuer" />
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>入力、出力、永続化要求

**InputClaims**、**OutputClaims**、**PersistClaims** の要素は空であるか、存在しません。 **InutputClaimsTransformations** と **OutputClaimsTransformations** の要素も存在しません。

## <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | はい | OAuth2 認証コードと更新トークン内で、ユーザー ID の要求として使用されることになる要求。 既定では、別の SubjectNamingInfo 要求タイプを指定しない限り、`objectId` に設定するようにしてください。 |
| SendTokenResponseBodyWithJsonNumbers | いいえ | 常に `true` に設定します。 数値が JSON 番号の代わりに文字列として指定されているレガシー形式の場合は、`false` に設定します。 この属性は、文字列などのプロパティを返す以前の実装に依存しているクライアントに必要です。 |
| token_lifetime_secs | いいえ | アクセス トークンの有効期間。 保護されているリソースへのアクセスを取得するために使用される OAuth 2.0 ベアラー トークンの有効期間です。 既定は 3,600 秒 (1 時間) です。 最短 (包括) は 300 秒 (5 分) です。 最長 (包括) は 86,400 秒 (24 時間) です。 |
| id_token_lifetime_secs | いいえ | ID トークンの有効期間。 既定は 3,600 秒 (1 時間) です。 最短 (包括) は 300 秒 (5 分) です。 最長 (包括) は 86,400 秒 (24 時間) です。 |
| refresh_token_lifetime_secs | いいえ | 更新トークンの有効期間。 アプリケーションが offline_access スコープを許可されていた場合に、新しいアクセス トークンを取得するために更新トークンを使用できる最長期間です。 既定は 120,9600 秒 (14 日) です。 最短 (包括) は 86,400 秒 (24 時間) です。 最長 (包括) は 7,776,000 秒 (90 日) です。 |
| rolling_refresh_token_lifetime_secs | いいえ | 更新トークンのスライディング ウィンドウの有効期間。 この期間が経過すると、アプリケーションによって取得された最新の更新トークンの有効期間にかかわらず、ユーザーは強制的に再認証されます。 スライディング ウィンドウの有効期間を強制しないようにする場合は、allow_infinite_rolling_refresh_token の値を `true` に設定してください。 既定は 7,776,000 秒 (90 日) です。 最短 (包括) は 86,400 秒 (24 時間) です。 最長 (包括) は 31,536,000 秒 (365 日) です。 |
| allow_infinite_rolling_refresh_token | いいえ | `true` に設定される場合、更新トークンのスライディング ウィンドウの有効期間はありません。 |
| IssuanceClaimPattern | いいえ | 発行者 (iss) 要求を制御します。 次のいずれかの値です。<ul><li>AuthorityAndTenantGuid - iss 要求には、`login.microsoftonline` や `tenant-name.b2clogin.com` のようなドメイン名、テナント識別子 https:\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/ などがあります。</li><li>AuthorityWithTfp - iss 要求には、`login.microsoftonline` や `tenant-name.b2clogin.com` のようなドメイン名、テナント識別子、証明書利用者のポリシー名などがあります。 https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> 既定値:AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | いいえ | `acr` の要求値 を制御します。<ul><li>None - Azure AD B2C は acr 要求を発行しない</li><li>PolicyId - `acr` 要求にポリシー名を含める</li></ul>この値を設定する場合のオプションは、TFP (trust framework policy) および ACR (authentication context reference) です。 この値を TFP に設定することをお勧めします。値を設定するには、`<Item>` が `Key="AuthenticationContextReferenceClaimPattern"` と共に存在し、その値が `None` であることを確認してください。 証明書利用者ポリシーで、`<OutputClaims>` 項目を追加し、この要素 `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />` を追加します。 ポリシーには、クレームの種類 `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` が存在することも確認してください。 |
|RefreshTokenUserJourneyId| いいえ | `/token`エンドポイントに対する[アクセス トークンの更新](authorization-code-flow.md#4-refresh-the-token)の POST 要求時に実行される必要がある、ユーザー体験の識別子。 |

## <a name="cryptographic-keys"></a>暗号化キー

CryptographicKeys 要素には次の属性が存在します。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| issuer_secret | はい | JWT トークンを署名するために使用する X509 証明書 (RSA キー セット)。 これは、「[カスタム ポリシーの概要](custom-policy-get-started.md)」で構成した `B2C_1A_TokenSigningKeyContainer` キーです。 |
| issuer_refresh_token_key | はい | 更新トークンを暗号化するために使用する X509 証明書 (RSA キー セット)。 「[カスタム ポリシー作業の開始](custom-policy-get-started.md)」で `B2C_1A_TokenEncryptionKeyContainer` キーを構成しました。 |

## <a name="session-management"></a>セッションの管理

Azure AD B2C と証明書利用者アプリケーションの間の Azure AD B2C セッションを構成するには、`UseTechnicalProfileForSessionManagement` 要素の属性で、[OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider) SSO セッションへの参照を追加します。














