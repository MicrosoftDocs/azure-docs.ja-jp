---
title: Azure Active Directory B2C でカスタム ポリシーを使用して SSO とトークンのカスタマイズを管理する | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーを使用して SSO とトークンのカスタマイズを管理する方法を説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: f3621b176e4bbfdfbd171339d6d01a1f91ed0ae7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509296"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して SSO とトークンのカスタマイズを管理する

この記事では、Azure Active Directory (Azure AD) B2C で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用してトークン、セッション、シングル サインオン (SSO) の構成を管理する方法について説明します。

## <a name="token-lifetimes-and-claims-configuration"></a>トークンの有効期間と要求の構成

トークンの有効期間の設定を変更するには、対象となるポリシーの証明書利用者ファイルに [ClaimsProviders](claimsproviders.md) 要素を追加します。  **ClaimsProviders** 要素は、[TrustFrameworkPolicy](trustframeworkpolicy.md) 要素の子です。 

証明書利用者ファイルのBasePolicy 要素と RelyingParty 要素間に ClaimsProviders 要素を挿入します。

内部にトークンの有効期間に影響を与える情報を指定する必要があります。 XML は、この例のようになります。

```XML
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
    
    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```
    
    **OutputClaims** 要素に、次の要素を追加します。
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    ACR の場合は、**AuthenticationContextReferenceClaimPattern** 項目を削除します。

- **サブジェクト (サブ) 要求**: このオプションは、既定で ObjectID に設定されています。この設定を `Not Supported` に切り替える場合は、次の行を置き換えます。 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    置き換えた後の行は次のとおりです。
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>セッションの動作と SSO

セッションの動作と SSO の構成を変更するには、[RelyingParty](relyingparty.md) 要素の内部に **UserJourneyBehaviors** 要素を追加します。  **UserJourneyBehaviors** 要素は、**DefaultUserJourney** の直後にする必要があります。 **UserJourneyBehavors** 要素の内部は、次の例のようにする必要があります。

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

以下の値は前の例で構成されています。

- **シングル サインオン (SSO)** : シングル サインオンは、**SingleSignOn** で構成します。 指定できる値は、`Tenant`、`Application`、`Policy`、`Suppressed` です。 
- **Web アプリのセッションの有効期間 (分)** : Web アプリのセッションの有効期間は、**SessionExpiryInSeconds** 要素で設定します。 既定値は 86,400 秒 (1,440 分) です。
- **Web アプリのセッション タイムアウト**: Web アプリのセッション タイムアウトは、**SessionExpiryType** 要素で設定します。 指定できる値は、`Absolute` および `Rolling` です。
