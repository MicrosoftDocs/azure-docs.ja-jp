---
title: カスタム ポリシーを使用して SSO とトークンのカスタマイズを管理する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーを使用して SSO とトークンのカスタマイズを管理する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9f3cd5c3280308f6da15a52361857fa02567d595
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505463"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して SSO とトークンのカスタマイズを管理する

この記事では、Azure Active Directory B2C (Azure AD B2C) で[カスタム ポリシー](custom-policy-overview.md)を使用してトークン、セッション、シングル サインオン (SSO) の構成を管理する方法について説明します。

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>JWT トークンの有効期間と要求の構成

トークンの有効期間の設定を変更するには、対象となるポリシーの証明書利用者ファイルに [ClaimsProviders](claimsproviders.md) 要素を追加します。  **ClaimsProviders** 要素は、[TrustFrameworkPolicy](trustframeworkpolicy.md) 要素の子です。

証明書利用者ファイルのBasePolicy 要素と RelyingParty 要素間に ClaimsProviders 要素を挿入します。

内部にトークンの有効期間に影響を与える情報を指定する必要があります。 XML は、この例のようになります。

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

以下の値は前の例で設定されています。

- **アクセス トークンの有効期間**: アクセス トークンの有効期間の値は、**token_lifetime_secs** メタデータ項目で設定します。 既定値は 3,600 秒 (60 分) です。
- **ID トークンの有効期間**: ID トークンの有効期間の値は、**id_token_lifetime_secs** メタデータ項目で設定します。 既定値は 3,600 秒 (60 分) です。
- **更新トークンの有効期間**: 更新トークンの有効期間の値は、**refresh_token_lifetime_secs** メタデータ項目で設定します。 既定値は 1,209,600 秒 (14 日) です。
- **更新トークン スライディング ウィンドウの有効期間**: 更新トークンにスライディング ウィンドウの有効期間を設定する場合は、**rolling_refresh_token_lifetime_secs** メタデータ項目の値を設定します。 既定値は 7,776,000 秒 (90 日) です。 スライディング ウィンドウの有効期間を指定しない場合は、項目を `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` に置き換えます。
- **発行者 (iss) 要求**: 発行者 (iss) 要求は、**IssuanceClaimPattern** メタデータ項目で設定します。 指定できる値は、`AuthorityAndTenantGuid` および `AuthorityWithTfp` です。
- **ポリシー ID を表す要求の設定**: この値を設定するためのオプションは、`TFP` (Trust Framework Policy) および `ACR` (Authentication Context Reference) です。 `TFP` が推奨値です。 **AuthenticationContextReferenceClaimPattern** に値 `None` を設定します。

    **ClaimsSchema** 要素に、次の要素を追加します。

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    **OutputClaims** 要素に、次の要素を追加します。

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    ACR の場合は、**AuthenticationContextReferenceClaimPattern** 項目を削除します。

- **サブジェクト (サブ) 要求**: このオプションは、既定で ObjectID に設定されています。この設定を `Not Supported` に切り替える場合は、次の行を置き換えます。

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    置き換えた後の行は次のとおりです。

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C セッション](session-overview.md)の詳細について学習します。
- [カスタム ポリシーでセッションの動作を構成する](session-behavior-custom-policy.md)方法を学習します。
- リファレンス: [JwtIssuer](jwt-issuer-technical-profile.md)。
