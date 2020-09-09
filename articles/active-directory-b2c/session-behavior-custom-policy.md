---
title: カスタム ポリシーを使用してセッションの動作を構成する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーを使用してセッションの動作を構成する。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2f20a4521efe2806c4bc66e4612b99caf84382a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385265"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用してセッションの動作を構成する

Azure Active Directory B2C (Azure AD B2C) で[シングル サインオン (SSO) セッション](session-overview.md)管理を使用すると、管理者は、ユーザーが認証された後にそのユーザーとの対話を制御できます。 たとえば、管理者は、選択した ID プロバイダーを表示するかどうか、アカウントの詳細を再入力する必要があるかどうかを制御できます。 この記事では、Azure AD B2C の SSO 設定を構成する方法について説明します。

## <a name="session-behavior-properties"></a>セッションの動作のプロパティ

次のプロパティを使用して、Web アプリケーション セッションを管理できます。

- **[Web アプリのセッションの有効期間 (分)]** - 認証の成功時にユーザーのブラウザーに格納される Azure AD B2C のセッション Cookie の有効期間。
    - 既定値 =  86400 秒 (1440 分)。
    - 最小値 (この値を含む) = 900 秒 (15 分)。
    - 最大値 (この値を含む) = 86400 秒 (1440 分)。
- **[Web アプリのセッション タイムアウト]** - [セッションの有効期限の種類](session-overview.md#session-expiry-type)、 *[ローリング]* または *[絶対]* 。 
- **[シングル サインオン構成]** - Azure AD B2C テナント内の複数のアプリとユーザー フローにまたがるシングル サインオン (SSO) の動作の[セッション スコープ](session-overview.md#session-scope)。 

## <a name="configure-the-properties"></a>プロパティを構成する

セッションの動作と SSO の構成を変更するには、[RelyingParty](relyingparty.md) 要素の内部に **UserJourneyBehaviors** 要素を追加します。  **UserJourneyBehaviors** 要素は、**DefaultUserJourney** の直後にする必要があります。 **UserJourneyBehavors** 要素は、次の例のようになります。

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="single-sign-out"></a>シングル サインアウト

### <a name="configure-the-applications"></a>アプリケーションを構成する

Azure AD B2C のサインアウト エンドポイントにユーザーをリダイレクトすると (OAuth2 と SAML プロトコルの両方)、Azure AD B2C によってユーザーのセッションがブラウザーからクリアされます。  [シングル サインアウト](session-overview.md#single-sign-out)を使用できるようにするには、Azure portal からアプリケーションの `LogoutUrl` を設定します。

1. [Azure Portal](https://portal.azure.com) に移動します。
1. ページの右上隅のアカウントをクリックして、Azure AD B2C のディレクトリを選択します。
1. 左側のメニューで **[Azure AD B2C]** 、 **[アプリの登録]** の順に選択し、対象のアプリケーションを選択します。
1. **[設定]** 、 **[プロパティ]** の順に選択して、 **[ログアウト URL]** テキスト ボックスを探します。 

### <a name="configure-the-token-issuer"></a>トークン発行者を構成する 

シングル サインアウトをサポートするには、JWT と SAML の両方のトークン発行者技術プロファイルで次を指定する必要があります。

- プロトコル名 (`<Protocol Name="OpenIdConnect" />` など)
- セッション技術プロファイルへの参照 (`UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` など)。

次の例は、シングル サインアウトでの JWT と SAML のトークン発行者を示しています。

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C セッション](session-overview.md)の詳細について学習します。
