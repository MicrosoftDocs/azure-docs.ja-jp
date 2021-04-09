---
title: SAML プロトコルを使用して Salesforce SAML プロバイダーでのサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で SAML プロトコルを使用して、Salesforce SAML プロバイダーでのサインインを設定します。
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
ms.openlocfilehash: e740fdb9cd232892dadfe98c4d739759be66bf55
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488722"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で SAML プロトコルを使用して Salesforce SAML プロバイダーでのサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) で[カスタム ポリシー](custom-policy-overview.md)を使用して Salesforce 組織からのユーザーのサインインを有効にする方法について説明します。 サインインを有効にするには、[SAML ID プロバイダー](identity-provider-generic-saml.md)をカスタム ポリシーに追加します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- まだ行っていない場合は、[無料の Developer Edition アカウント](https://developer.salesforce.com/signup)にサインアップします。 この記事では、[Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ) を使用します。
- Salesforce 組織用の ["私のドメイン" のセットアップ](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

## <a name="set-up-salesforce-as-an-identity-provider"></a>ID プロバイダーとしての Salesforce のセットアップ

1. [Salesforce にサインインします](https://login.salesforce.com/)。
2. 左側のメニューで、 **[設定]** の下の **[ID]** を展開し、 **[ID プロバイダー]** を選択します。
3. **[Enable Identity Provider]\(ID プロバイダーを有効にする\)** を選択します。
4. **[Select the certificate] \(証明書の選択)** の下から、Azure AD B2C と通信するときに Salesforce で使用する証明書を選択します。 既定の証明書を使用できます。
5. **[保存]** をクリックします。

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce での接続されたアプリの作成

1. **[Identity Provider]\(ID プロバイダー\)** ページで、 **[Service Providers are now created via Connected Apps.]\(接続されたアプリでサービス プロバイダーが作成されました。ここをクリックしてください。\)** をクリックします。
2. **[基本情報]** に、接続されたアプリの必須の値を入力します。
3. **[Web App Settings]\(Web アプリの設定\)** で、 **[Enable SAML]\(SAML を有効にする\)** ボックスをオンにします。
4. **[エンティティ ID]** フィールドに、次の URL を入力します。 `your-tenant` の値を、Azure AD B2C テナントの名前に置き換えます。

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      [カスタム ドメイン](custom-domain.md)を使用する場合は、次の形式を使用します。

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. **[ACS URL]** フィールドに、次の URL を入力します。 `your-tenant` の値を、Azure AD B2C テナントの名前に置き換えます。

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      [カスタム ドメイン](custom-domain.md)を使用する場合は、次の形式を使用します。

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. リストの下までスクロールし、 **[保存]** をクリックします。

### <a name="get-the-metadata-url"></a>メタデータ URL の取得

1. 接続されたアプリの概要ページで **[管理]** をクリックします。
2. **メタデータ検出エンドポイント** の値をコピーし、保存します。 この記事の後半で、その値を使用します。

### <a name="set-up-salesforce-users-to-federate"></a>フェデレーションのための Salesforce ユーザーの設定

1. 接続されたアプリの **[Manage]\(管理\)** ページで、 **[Manage Profiles]\(プロファイルの管理\)** をクリックします。
2. Azure AD B2C とフェデレーションするプロファイル (またはユーザーのグループ) を選択します。 システム管理者は、Salesforce アカウントを使用してフェデレーションを行うことができるように、 **[システム管理者]** のチェック ボックスをオンにします。

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

作成した証明書を Azure AD B2C テナントに格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション** については、`Upload`を選択します。
7. ポリシーの **名前** を入力します。 たとえば、SAMLSigningCert などです。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. 作成した B2CSigningCert.pfx 証明書を参照して選択します。
9. 証明書の **[パスワード]** を入力します。
3. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが Salesforce アカウントを使用してサインインするようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

Salesforce アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。 詳細については、[SAML ID プロバイダーの定義](identity-provider-generic-saml.md)に関するページをご覧ください。

1. *TrustFrameworkExtensions.xml* を開きます。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **PartnerEntity** の値を、先ほどコピーした Salesforce メタデータ URL で更新します。
1. **StorageReferenceId** の両方のインスタンスの値を、署名証明書のキーの名前に更新します。 たとえば、B2C_1A_SAMLSigningCert です。
1. `<ClaimsProviders>` セクションを見つけて、次の XML スニペットを追加します。 ポリシーに `SM-Saml-idp` 技術プロファイルが既に含まれている場合、次の手順に進みます。 詳細については、[シングル サインオン セッション管理](custom-policy-reference-sso.md)に関するページを参照してください。

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. ファイルを保存します。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](troubleshoot-custom-policies.md#troubleshoot-the-runtime) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Salesforce]** を選択して、Salesforce アカウントを使用してサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end