---
title: Amazon アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで Amazon アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 01/27/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 23867ac6eb6941e2d132ae885fccd0e938fef907
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953108"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Amazon アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>Amazon 開発者コンソールでアプリを作成する

Azure Active Directory B2C (Azure AD B2C) で Amazon アカウントを持つユーザーのサインインを有効にするために、[Amazon Developer Services and Technologies](https://developer.amazon.com) でアプリケーションを作成する必要があります。 詳細については、[Amazon でのログインのための登録](https://developer.amazon.com/docs/login-with-amazon/register-web.html)に関する記事を参照してください。 まだ Amazon アカウントを持っていない場合は、[https://www.amazon.com/](https://www.amazon.com/) でサインアップできます。

> [!NOTE]  
> 以下の **手順 8** で次の URL を使用します。`your-tenant-name` はご利用のテナントの名前に置き換えてください。 テナント名を入力するときに、テナントが Azure AD B2C に大文字で定義されている場合でも、すべてに小文字を使用します。
> - **[許可されたオリジン]** には、「`https://your-tenant-name.b2clogin.com`」を入力します 
> - **[Allowed Return URLs]\(許可された戻り先 URL\)** には、「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>ID プロバイダーとして Amazon を構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Amazon]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Amazon*」とします。
1. **[クライアント ID]** には、前に作成した Amazon アプリケーションのクライアント ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>ユーザー フローに Amazon ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Amazon ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[Amazon]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録したクライアント シークレットを格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション** については、`Manual`を選択します。
7. ポリシー キーの **名前** を入力します。 たとえば、「 `AmazonSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として [`Signature`] を選択します。
10. **Create** をクリックしてください。

## <a name="configure-amazon-as-an-identity-provider"></a>ID プロバイダーとして Amazon を構成する

ユーザーが Amazon アカウントを使用してサインインできるようにするには、そのアカウントをクレーム プロバイダーとして定義する必要があります。 Azure AD B2C はエンドポイント経由でこれと通信できます。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

Amazon アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。


1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **client_id** を、アプリケーションの登録で取得したアプリケーション ID に設定します。
5. ファイルを保存します。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
