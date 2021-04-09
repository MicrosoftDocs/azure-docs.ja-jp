---
title: Azure AD 組織用のサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 490880e4a37711a92b44a0ffe01315edfa6ddb26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580128"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定する

この記事では、Azure AD B2C のユーザー フローを使用して、特定の Azure AD 組織のユーザーがサインインできるようにする方法について説明します。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Azure AD アプリの登録

Azure Active Directory B2C (Azure AD B2C) で、特定の Azure AD 組織の Azure AD アカウントを持つユーザーのサインインを有効にするには、[Azure portal](https://portal.azure.com) でアプリケーションを作成する必要があります。 詳細については、[Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)方法に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 組織の Azure AD テナント (contoso.com など) が含まれているディレクトリを使用していることを確認します。 上部のメニューで **[ディレクトリ + サブスクリプション] フィルター** を選択し、Azure AD テナントが含まれているディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
1. このアプリケーションには、 **[この組織のディレクトリ内のアカウントのみ]** という既定の選択をそのまま使用します。
1. **[リダイレクト URL]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    たとえば、「 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp` 」のように入力します。

    [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-domain-name` を実際のカスタム ドメインに、`your-tenant-name` を実際のテナントの名前に置き換えます。

1. **[登録]** を選択します。 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。
1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。
1. シークレットの **説明** を入力し、有効期限を選択して、 **[追加]** を選択します。 後の手順で使用するために、シークレットの **値** を記録しておきます。

### <a name="configuring-optional-claims"></a>省略可能な要求の構成

Azure AD から `family_name` および `given_name` 要求を取得する場合は、ご利用のアプリケーションに対して省略可能な要求を Azure portal UI またはアプリケーション マニフェストで構成できます。 詳細については、[Azure AD アプリに省略可能な要求を提供する方法](../active-directory/develop/active-directory-optional-claims.md)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。 **Azure Active Directory** を検索して選択します。
1. **[管理]** セクションで、 **[アプリの登録]** を選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から選択します。
1. **[管理]** セクションで、 **[トークン構成]** を選択します。
1. **[省略可能な要求を追加]** を選択します。
1. **[トークンの種類]** で、 **[ID]** を選択します。
1. 追加する省略可能な要求 (`family_name` と `given_name`) を選択します。
1. **[追加]** をクリックします。

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

1. Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば､「*Contoso Azure AD*」と入力します。
1. **[メタデータ URL]** には、次の URL を入力します。`{tenant}` は、利用する Azure AD テナントのドメイン名に置き換えます。

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    たとえば、「 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` 」のように入力します。
    たとえば、「 `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration` 」のように入力します。

1. **[クライアント ID]** には、前に記録したアプリケーション ID を入力します。
1. **[クライアント シークレット]** には、前に記録したクライアント シークレットを入力します。
1. **[スコープ]** には、「`openid profile`」と入力します。
1. **[応答の種類]** および **[応答モード]** の既定値はそのままにします。
1. (省略可能) **[ドメインのヒント]** に、「`contoso.com`」と入力します。 詳しくは、「[Azure Active Directory B2C を使用した直接サインインの設定](direct-signin.md#redirect-sign-in-to-a-social-provider)」をご覧ください。
1. **[ID プロバイダー要求のマッピング]** で、次の要求を入力します。

    - **[ユーザー ID]** : *oid*
    - **[表示名]** : *name*
    - **[名]** : *given_name*
    - **[姓]** : *family_name*
    - **[電子メール]** : *preferred_username*

1. **[保存]** を選択します。

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>ユーザー フローに Azure AD ID プロバイダーを追加する 

この時点では、Azure AD ID プロバイダーはセットアップされていますが、サインイン ページではまだ使用できません。 ユーザー フローに Azure AD ID プロバイダーを追加するには:

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Azure AD ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[Contoso Azure AD]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso Azure AD]** を選択し、Azure AD Contoso アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

作成したアプリケーション キーを Azure AD B2C テナントに格納する必要があります。

1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション] フィルター** を選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `ContosoAppSecret` 」のように入力します。  作成時に、プレフィックス `B2C_1A_` がキーの名前に自動的に追加されるため、次のセクションの XML での参照は *B2C_1A_ContosoAppSecret* になります。
1. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **［作成］** を選択します

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

ユーザーが Azure AD アカウントを使用してサインインできるようにするには、エンドポイント経由で Azure AD B2C が通信できるクレーム プロバイダーとして Azure AD を定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

ポリシーの拡張ファイル内で Azure AD を **ClaimsProvider** 要素に追加することで、Azure AD をクレーム プロバイダーとして定義できます。

1. *TrustFrameworkExtensions.xml* ファイルを開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **ClaimsProvider** 要素の下で、**Domain** の値を、他の ID プロバイダーと区別するために使用できる一意の値に更新します。 たとえば、「 `Contoso` 」のように指定します。 `.com` はこのドメイン設定の最後に配置しません。
5. **ClaimsProvider** 要素の下で、**DisplayName** の値を、クレーム プロバイダーを表すわかりやすい名前に更新します。 この値は現在使用されていません。

### <a name="update-the-technical-profile"></a>技術プロファイルの更新

Azure AD エンドポイントからトークンを取得するには、Azure AD B2C で Azure AD との通信に使用するプロトコルを定義する必要があります。 これは、**ClaimsProvider** の **TechnicalProfile** 要素の中で実行します。

1. **TechnicalProfile** 要素の ID を更新します。 この ID は、`AADContoso-OpenIdConnect` など、ポリシーの他の部分からこの技術プロファイルを参照するために使用します。
1. **DisplayName** の値を更新します。 この値は、サインイン画面のサインイン ボタン上に表示されます。
1. **Description** の値を更新します。
1. Azure AD では OpenID Connect プロトコルを使用するため、**Protocol** の値が `OpenIdConnect` になっていることを確認してください。
1. **METADATA** の値を `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` に設定します。`tenant-name` は Azure AD テナント名です。 たとえば、`https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` のように指定します。
1. **client_id** を、アプリケーションの登録で取得したアプリケーション ID に設定します。
1. **CryptographicKeys** で、**StorageReferenceId** の値を、前に作成したポリシー キーの名前に更新します。 たとえば、「 `B2C_1A_ContosoAppSecret` 」のように入力します。


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](troubleshoot-custom-policies.md#troubleshoot-the-runtime) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso Employee]** を選択し、Azure AD Contoso アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

## <a name="next-steps"></a>次のステップ

カスタム ポリシーを操作する場合、開発過程でポリシーのトラブルシューティングを行っているときに、追加情報が必要になることがあります。

問題の診断に役立てるために、ポリシーを一時的に "開発者モード" にして、Azure Application Insights を使用してログを収集できます。 その方法については、[Azure Active Directory B2C: ログの収集](troubleshoot-with-application-insights.md)に関するページを参照してください。

::: zone-end
