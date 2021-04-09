---
title: カスタム ポリシーを使用したシングル サインオンのセッション管理
titleSuffix: Azure AD B2C
description: Azure AD B2C でカスタム ポリシーを使用して SSO セッションを管理する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f690f4a416e86b02de0d35fc673849c1293df577
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095767"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でのシングル サインオン管理

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[シングル サインオン (SSO) セッション](session-behavior.md)管理では、カスタム ポリシー内のその他の技術プロファイルと同じセマンティクスが使用されます。 オーケストレーション ステップが実行されると、そのステップに関連付けられた技術プロファイルで `UseTechnicalProfileForSessionManagement` が照会されます。 存在する場合、参照されている SSO セッション プロバイダーを調べて、そのユーザーがセッション参加者であるかどうかを確認します。 参加者の場合は、セッションの再設定には SSO セッション プロバイダーが使用されます。 同様に、オーケストレーション ステップの実行が完了すると、SSO セッション プロバイダーが指定されている場合は、プロバイダーを使用してセッションに情報を格納します。

Azure AD B2C では、使用可能な多数の SSO セッション プロバイダーが定義されています。

|セッション プロバイダー  |Scope  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  なし       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C 内部セッション マネージャー。      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Azure AD B2C と OAuth1、OAuth2、または OpenId Connect ID プロバイダーの間。        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | OAuth2 または OpenId Connect 証明書利用者アプリケーションと Azure AD B2C の間。        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Azure AD B2C と SAML ID プロバイダーの間。 および、SAML サービス プロバイダー (証明書利用者アプリケーション) と Azure AD B2C の間。  |        




SSO 管理クラスは、技術プロファイルの `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` 要素を使用して指定されています。

## <a name="input-claims"></a>入力クレーム

`InputClaims` 要素が空か、または存在しません。

## <a name="persisted-claims"></a>永続化された要求

アプリケーションに返す必要のある要求、または後の手順の事前条件で使用される要求は、セッション内で保管されるか、あるいはディレクトリ内のユーザーのプロファイルからの読み取りによって拡張される必要があります。 永続化された要求を使用すると、要求がないことによる認証処理の失敗が確実に防止されます。 セッションに要求を追加するには、技術プロファイルの `<PersistedClaims>` 要素を使用します。 このプロバイダーを使用してセッションを再設定すると、保持されている要求は要求バッグに追加されます。

## <a name="output-claims"></a>出力クレーム

`<OutputClaims>` は、セッションから要求を取得する場合に使用されます。

## <a name="session-providers"></a>セッション プロバイダー

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

名前が示すように、このプロバイダーは何もしません。 このプロバイダーは、特定の技術プロファイルの SSO 動作を抑制するために使用できます。 次の `SM-Noop` 技術プロファイルは、[カスタム ポリシー スターター パック](custom-policy-get-started.md#custom-policy-starter-pack)に含まれています。

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

このプロバイダーは、要求をセッション内に保存するために使用できます。 通常、このプロバイダーは、ローカル アカウントおよびフェデレーション アカウントの管理に使用される技術プロファイル内で参照されます。 次の `SM-AAD` 技術プロファイルは、[カスタム ポリシー スターター パック](custom-policy-get-started.md#custom-policy-starter-pack)に含まれています。

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


次の `SM-MFA` 技術プロファイルは、[カスタム ポリシー スターター パック](custom-policy-get-started.md#custom-policy-starter-pack)`SocialAndLocalAccountsWithMfa`に含まれています。 この技術プロファイルでは、多要素認証セッションを管理します。

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

このプロバイダーは、"ID プロバイダーの選択" 画面とフェデレーション ID プロバイダーからのサインアウトを抑制するために使用されます。 これは通常、フェデレーション ID プロバイダー (Facebook や Azure Active Directory など) 用に構成された技術プロファイルで参照されます。 次の `SM-SocialLogin` 技術プロファイルは、[カスタム ポリシー スターター パック](custom-policy-get-started.md#custom-policy-starter-pack)に含まれています。

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | いいえ | 現在使用されていません。無視してもかまいません。 |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

このプロバイダーは、OAuth2 または OpenId Connect 証明書利用者と Azure AD B2C 間の Azure AD B2C セッションを管理するために使用されます。

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

このプロバイダーは、証明書利用者アプリケーションまたはフェデレーション SAML ID プロバイダー間で、Azure AD B2C SAML セッションを管理するために使用されます。 SAML ID プロバイダー セッションを保存するために SSO プロバイダーを使用する場合、`RegisterServiceProviders` を `false` に設定する必要があります。 次の `SM-Saml-idp` 技術プロファイルは、[SAML ID プロバイダー](identity-provider-generic-saml.md)によって使用されます。

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

B2C SAML セッションを保存するためにプロバイダーを使用する場合は、`RegisterServiceProviders` を `true` に設定する必要があります。 SAML セッションのログアウトを完了するには、`SessionIndex` と `NameID` が必要です。

次の `SM-Saml-issuer` 技術プロファイルは、[SAML 発行者技術プロファイル](saml-service-provider.md)によって使用されます。

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明|
| --- | --- | --- |
| IncludeSessionIndex | いいえ | 現在使用されていません。無視してもかまいません。|
| RegisterServiceProviders | いいえ | アサーションが発行された SAML サービス プロバイダーすべてをプロバイダーが登録する必要があることを示します。 指定できる値は `true`(既定値) または`false`です。|


## <a name="next-steps"></a>次のステップ

[セッション動作を構成](session-behavior.md)する方法について確認します。
