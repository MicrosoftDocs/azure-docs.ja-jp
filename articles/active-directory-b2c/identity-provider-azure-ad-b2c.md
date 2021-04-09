---
title: 別の Azure AD B2C テナントの Azure AD B2C アカウントを使用したサインアップとサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで、別のテナントの Azure AD B2C アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4b357213f4e552fd791fb575d8b7a287b924c7f9
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489072"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>別の Azure AD B2C テナントの Azure AD B2C アカウントを使用したサインアップとサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>概要

この記事では、別の Azure AD B2C テナントとのフェデレーションを設定する方法について説明します。 アプリケーションが Azure AD B2C で保護されている場合、これにより、他の Azure AD B2C のユーザーが既存のアカウントを使用してログインできるようになります。 次の図では、ユーザーは、"*Contoso*" の Azure AD B2C によって保護されているアプリケーションに、"*Fabrikam*" の Azure AD B2C テナントによって管理されているアカウントを使用してサインインできます。 

![別の Azure AD B2C テナントとの Azure AD B2C のフェデレーション](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

別の Azure AD B2C テナント (例: Fabrikam) のアカウントを持つユーザーが、Azure AD B2C (例: Contoso) にサインインできるようにするには、次のようにします。

1. [ユーザー フロー](tutorial-create-user-flows.md)または[カスタム ポリシー](custom-policy-get-started.md)を作成します。
1. 次に、このセクションの説明に従い、Azure AD B2C でアプリケーションを作成します。 

アプリケーションを作成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 他の Azure AD B2C テナント (例: Fabrikam.com) が含まれるディレクトリを使用していることを確認します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、"*ContosoApp*" などです。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。
1. **[リダイレクト URL]** で **[Web]** を選択し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前 (例: Contoso) に置き換えます。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    たとえば、「 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 」のように入力します。

    [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-domain-name` を実際のカスタム ドメインに、`your-tenant-name` を実際のテナントの名前に置き換えます。

1. [アクセス許可] で、 **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェック ボックスをオンにします。
1. **[登録]** を選択します。
1. **[Azure AD B2C - アプリの登録]** ページで、作成したアプリケーション (例: *ContosoApp*) を選択します。
1. アプリケーションの [概要] ページに表示されている **[アプリケーション (クライアント) ID]** を記録します。 これは、次のセクションで ID プロバイダーを構成するときに必要です。
1. 左側のメニューで、 **[管理]** の **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。
1. **[説明]** ボックスにクライアント シークレットの説明を入力します。 たとえば、*clientsecret1* のようにします。
1. **[有効期限]** で、シークレットが有効な期間を選択してから、 **[追加]** を選択します。
1. シークレットの **値** を記録します。 これは、次のセクションで ID プロバイダーを構成するときに必要です。


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C を ID プロバイダーとして構成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. フェデレーションを構成する Azure AD B2C テナント (例: Contoso) が含まれるディレクトリを使用していることを確認します。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、お使いの Azure AD B2C テナント (例: Contoso) を含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Fabrikam*」と入力します。
1. **[メタデータ URL]** に、次の URL を入力します。`{tenant}` は、お使いの Azure AD B2C テナントのドメイン名 (例: Fabrikam) に置き換えます。 `{policy}` は、もう一方のテナントで構成したポリシー名に置き換えます。

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    たとえば、「 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration` 」のように入力します。

1. **[クライアント ID]** には、前に記録したアプリケーション ID を入力します。
1. **[クライアント シークレット]** には、前に記録したクライアント シークレットを入力します。
1. **[スコープ]** で、`openid` を入力します。
1. **[応答の種類]** および **[応答モード]** の既定値はそのままにします。
1. (省略可能) **[ドメインのヒント]** に、[直接サインイン](direct-signin.md#redirect-sign-in-to-a-social-provider)に使用するドメイン名を入力します。 たとえば、*fabrikam.com* などです。
1. **[ID プロバイダー要求のマッピング]** で、次の要求を入力します。

    - **[ユーザー ID]** : *sub*
    - **[表示名]** : *name*
    - **[名]** : *given_name*
    - **[姓]** : *family_name*
    - **[電子メール]** : *email*

1. **[保存]** を選択します。

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>ユーザー フローに Azure AD B2C ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Azure AD B2C ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** で **[Fabrikam]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで **Fabrikam** を選択して、他の Azure AD B2C テナントを使用してサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

前に作成したアプリケーション キーを Azure AD B2C テナントに格納する必要があります。

1. フェデレーションを構成する Azure AD B2C テナント (例: Contoso) が含まれるディレクトリを使用していることを確認します。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、お使いの Azure AD B2C テナント (例: Contoso) を含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `FabrikamAppSecret` 」のように入力します。  作成時に、プレフィックス `B2C_1A_` がキーの名前に自動的に追加されるため、次のセクションの XML での参照は *B2C_1A_FabrikamAppSecret* になります。
1. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **［作成］** を選択します

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C を ID プロバイダーとして構成する

ユーザーが別の Azure AD B2C テナント (Fabrikam) のアカウントを使用してサインインできるようにするために、エンドポイント経由で Azure AD B2C が通信できるクレーム プロバイダーとして他の Azure AD B2C を定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

ポリシーの拡張ファイル内で Azure AD B2C を **ClaimsProvider** 要素に追加することで、Azure AD B2C をクレーム プロバイダーとして定義できます。

1. *TrustFrameworkExtensions.xml* ファイルを開きます。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 関連する値を使用して、次の XML 要素を更新します。

    |XML 要素  |値  |
    |---------|---------|
    |ClaimsProvider\Domain  | [直接サインイン](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)に使用するドメイン名。 直接サインインに使用するドメイン名を入力します。 たとえば、*fabrikam.com* などです。 |
    |TechnicalProfile\DisplayName|この値は、サインイン画面のサインイン ボタン上に表示されます。 たとえば、"*Fabrikam*" などです。 |
    |Metadata\client_id|ID プロバイダーのアプリケーション識別子。 他の Azure AD B2C テナントで前に作成したアプリケーション ID を使用して、クライアント ID を更新します。|
    |Metadata\METADATA|OpenID の既知の構成エンドポイントとも呼ばれる OpenID Connect ID プロバイダー構成ドキュメントを指す URL。 次の URL を入力します。`{tenant}` は、他の Azure AD B2C テナントのドメイン名 (Fabrikam) に置き換えます。 `{tenant}` は、もう一方のテナントで構成したポリシー名に置き換えます。`{policy]` はポリシー名 `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` に置き換えます。 たとえば、「 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration` 」のように入力します。|
    |CryptographicKeys| **StorageReferenceId** の値を、前に作成したポリシー キーの名前に更新します。 たとえば、「 `B2C_1A_FabrikamAppSecret` 」のように入力します。| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](troubleshoot-custom-policies.md#troubleshoot-the-runtime) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで **Fabrikam** を選択して、他の Azure AD B2C テナントを使用してサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

## <a name="next-steps"></a>次のステップ

[他の Azure AD B2C トークンをアプリケーションに渡す](idp-pass-through-user-flow.md)方法について学習する。
