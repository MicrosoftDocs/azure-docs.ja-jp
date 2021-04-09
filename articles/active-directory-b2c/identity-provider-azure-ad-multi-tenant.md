---
title: カスタム ポリシーによるマルチテナント Azure AD のサインインの設定
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーを使用してマルチテナント Azure AD ID プロバイダーを追加します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5089a289e617aa8c2ec153320763647fc8afac9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489038"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のカスタム ポリシーを使用してマルチテナント Azure Active Directory を設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

この記事では、Azure Active Directory (Azure AD) のマルチテナント エンドポイントによってユーザーのサインインを有効にする方法について説明します。 Azure AD テナントごとに ID プロバイダーを構成せずに、複数のテナントのユーザーが Azure AD B2C を使用してサインインできるようにします。 ただし、これらのテナントのいずれのゲスト メンバーもサインインは **できません**。 そのため、[各テナントを個別に構成する](identity-provider-azure-ad-single-tenant.md)必要があります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>アプリケーションを登録する

Azure Active Directory B2C (Azure AD B2C) で Azure AD アカウントを持つユーザーのサインインを有効にするには、[Azure portal](https://portal.azure.com) でアプリケーションを作成する必要があります。 詳細については、[Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)方法に関するページを参照してください。


1. [Azure portal](https://portal.azure.com) にサインインします。
1. 組織の Azure AD テナント (contoso.com など) が含まれているディレクトリを使用していることを確認します。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
1. このアプリケーションに **[任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント)]** を選択します。
1. **[リダイレクト URL]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    たとえば、「 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp` 」のように入力します。

    [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-domain-name` を実際のカスタム ドメインに、`your-tenant-name` を実際のテナントの名前に置き換えます。

1. **[登録]** を選択します。 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。
1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。
1. シークレットの **説明** を入力し、有効期限を選択して、 **[追加]** を選択します。 後の手順で使用するために、シークレットの **値** を記録しておきます。

## <a name="configuring-optional-claims"></a>省略可能な要求の構成

Azure AD から `family_name` および `given_name` 要求を取得する場合は、Azure portal UI またはアプリケーション マニフェストでアプリケーションの省略可能な要求を構成できます。 詳細については、[Azure AD アプリに省略可能な要求を提供する方法](../active-directory/develop/active-directory-optional-claims.md)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。 **Azure Active Directory** を検索して選択します。
1. **[管理]** セクションで、 **[アプリの登録]** を選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から選択します。
1. **[管理]** セクションで、 **[トークン構成]** を選択します。
1. **[省略可能な要求を追加]** を選択します。
1. **[トークンの種類]** で、 **[ID]** を選択します。
1. 追加する省略可能な要求 (`family_name` と `given_name`) を選択します。
1. **[追加]** をクリックします。

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

作成したアプリケーション キーを Azure AD B2C テナントに格納する必要があります。

1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション] フィルター** を選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `AADAppSecret` 」のように入力します。  作成時に、プレフィックス `B2C_1A_` がキーの名前に自動的に追加されるため、次のセクションの XML での参照は *B2C_1A_AADAppSecret* になります。
1. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **［作成］** を選択します

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

ユーザーが Azure AD アカウントを使用してサインインできるようにするには、エンドポイント経由で Azure AD B2C が通信できるクレーム プロバイダーとして Azure AD を定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

ポリシーの拡張ファイル内で Azure AD を **ClaimsProvider** 要素に追加することで、Azure AD をクレーム プロバイダーとして定義できます。

1. *TrustFrameworkExtensions.xml* ファイルを開きます。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. **ClaimsProvider** 要素の下で、**Domain** の値を、他の ID プロバイダーと区別するために使用できる一意の値に更新します。
1. **TechnicalProfile** 要素の下で、**DisplayName** の値を更新します (例: `Contoso Employee`)。 この値は、サインイン ページのサインイン ボタン上に表示されます。
1. **client_id** は、前に登録した Azure AD マルチテナント アプリケーションのアプリケーション ID に設定します。
1. **CryptographicKeys** で、**StorageReferenceId** の値を、前に作成したポリシー キーの名前に更新します。 たとえば、「 `B2C_1A_AADAppSecret` 」のように入力します。

### <a name="restrict-access"></a>アクセスを制限する

**ValidTokenIssuerPrefixes** の値として `https://login.microsoftonline.com/` を使用すると、すべての Azure AD ユーザーがアプリケーションにサインインできるようになります。 有効なトークン発行者の一覧を更新し、サインインできる Azure AD テナント ユーザーの特定の一覧へのアクセスを制限します。

値を取得するには、サインインさせるユーザーの Azure AD テナントごとに、OpenID Connect Discovery のメタデータを調べます。 メタデータの URL の形式は、`https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` のようになっています。`your-tenant` は Azure AD テナントの名前です。 次に例を示します。

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

サインインに使用する Azure AD テナントごとに次の手順を実行します。

1. ブラウザーを開き、そのテナントに対応する OpenID Connect のメタデータ URL に移動します。 **発行者** オブジェクトを探し、その値を記録します。 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` のようになっていると思います。
1. その値をコピーして **ValidTokenIssuerPrefixes** キーに貼り付けます。 複数の発行者は、コンマで区切ります。 前出の `ClaimsProvider` XML サンプルでは、発行者が 2 つ存在する例を確認できます。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](troubleshoot-custom-policies.md#troubleshoot-the-runtime) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Common AAD]\(共通 AAD\)** を選択し、Azure AD アカウントでサインインします。

マルチテナントのサインイン機能をテストするために、別の Azure AD テナントに存在するユーザーの資格情報を使用して、最後の 2 つの手順を実行してください。 **[今すぐ実行のエンドポイント]** をコピーし、プライベート ブラウザー ウィンドウ (Google のシークレット モード、Microsoft Edge の InPrivate ウィンドウなど) で開きます。 プライベート ブラウザー ウィンドウで開くと、現在キャッシュされている Azure AD の資格情報を使用せずに、ユーザー体験を全体的にテストできます。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

## <a name="next-steps"></a>次のステップ

カスタム ポリシーを操作する場合、開発過程でポリシーのトラブルシューティングを行っているときに、追加情報が必要になることがあります。

問題の診断に役立てるために、ポリシーを一時的に "開発者モード" にして、Azure Application Insights を使用してログを収集できます。 その方法については、[Azure Active Directory B2C: ログの収集](troubleshoot-with-application-insights.md)に関するページを参照してください。

::: zone-end
