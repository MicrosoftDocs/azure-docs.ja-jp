---
title: "Azure Active Directory B2C: カスタム ポリシーで SSO とトークンのカスタマイズを管理する | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/02/2017
ms.author: sama
ms.openlocfilehash: 8f5703d15766f221517cd89352d41685652d32d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーで SSO とトークンのカスタマイズを管理する
カスタム ポリシーを使用すると、組み込みポリシーを使用する場合と同じように、トークン、セッション、シングル サインオン (SSO) 構成を制御できます。  各設定の機能については、[こちら](#active-directory-b2c-token-session-sso)のドキュメントを参照してください。

## <a name="token-lifetimes-and-claims-configuration"></a>トークンの有効期間と要求の構成
トークンの有効期間の設定を変更するには、対象となるポリシーの証明書利用者ファイルに `<ClaimsProviders>` 要素を追加する必要があります。  `<ClaimsProviders>` 要素は、`<TrustFrameworkPolicy>` の子です。  トークンの有効期間に影響を与える情報を内部に指定する必要があります。  XML は、次のようになります。

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

**アクセス トークンの有効期間** アクセス トークンの有効期間を変更するには、Key="token_lifetime_secs" が設定された `<Item>` 内の値 (秒単位) を変更します。  組み込みの既定値は、3,600 秒 (60 分) です。

**ID トークンの有効期間** ID トークンの有効期間を変更するには、Key="id_token_lifetime_secs" が設定された `<Item>` 内の値 (秒単位) を変更します。  組み込みの既定値は、3,600 秒 (60 分) です。

**更新トークンの有効期間** 更新トークンの有効期間を変更するには、Key="refresh_token_lifetime_secs" が設定された `<Item>` 内の値 (秒単位) を変更します。  組み込みの既定値は、1,209,600 秒 (14 日) です。

**更新トークン スライディング ウィンドウの有効期間** 更新トークンにスライディング ウィンドウの有効期間を設定する場合は、Key="rolling_refresh_token_lifetime_secs" が設定された `<Item>` 内の値 (秒単位) を変更します。  組み込みの既定値は、7,776,000 (90 日) です。  スライディング ウィンドウの有効期間を指定しない場合は、この行を次のように置き換えます。
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**発行者 (iss) 要求** 発行者 (iss) 要求を変更する場合は、Key="IssuanceClaimPattern" が設定された `<Item>` 内の値を変更します。  指定できる値は、`AuthorityAndTenantGuid` および `AuthorityWithTfp` です。

**ポリシー ID を表す要求の設定** この値を設定するためのオプションは、TFP (Trust Framework Policy) および ACR (Authentication Context Reference) です。  
これを TFP に設定することをお勧めします。そのためには、Key="AuthenticationContextReferenceClaimPattern" が設定された `<Item>` が存在し、その値が `None` であることを確認してください。
`<OutputClaims>` 項目に、次の要素を追加します。
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
ACR に設定する場合は、Key="AuthenticationContextReferenceClaimPattern" が設定された `<Item>` を削除します。

**サブジェクト (サブ) 要求** このオプションは、既定で ObjectID に設定されています。これを `Not Supported` に切り替える場合は、次のようにします。

置き換える行は次のとおりです。 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
置き換えた後の行は次のとおりです。
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>セッションの動作と SSO
セッションの動作と SSO の構成を変更するには、`<RelyingParty>` 要素の内部に `<UserJourneyBehaviors>` 要素を追加する必要があります。  `<UserJourneyBehaviors>` 要素は、`<DefaultUserJourney>` の直後に追加する必要があります。  `<UserJourneyBehavors>` 要素の内部は、次のようになります。

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**シングル サインオン (SSO) 構成** シングル サインオン構成を変更するには、`<SingleSignOn>` の値を変更する必要があります。  指定できる値は、`Tenant`、`Application`、`Policy`、`Disabled` です。 

**Web アプリ セッションの有効期間 (分)** Web アプリ セッションの有効期間を変更するには、`<SessionExpiryInSeconds>` 要素の値を変更する必要があります。  組み込みのポリシーの既定値は、86,400 秒 (1,440 分) です。

**Web アプリのセッション タイムアウト** Web アプリのセッションのタイムアウトを変更するには、`<SessionExpiryType>` の値を変更する必要があります。  指定できる値は、`Absolute` および `Rolling` です。
