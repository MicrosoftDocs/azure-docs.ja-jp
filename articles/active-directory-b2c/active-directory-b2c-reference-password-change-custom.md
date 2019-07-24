---
title: Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの変更を構成する | Microsoft Docs
description: ユーザーが Azure Active Directory B2C でカスタム ポリシーを使用してパスワードを変更できるようにする方法を説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a9e58139d248daed19f4fe35c7d33ede9dfe64b0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195972"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの変更を構成する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C では、ローカル アカウントでサインインしているユーザーが、電子メールでの確認による信頼性の証明なしでパスワードを変更できます。 ユーザーがパスワードの変更フローを終了する前にセッションの有効期限が切れた場合は、もう一度サインインすることを求められます。 この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)にパスワードの変更を構成する方法について説明します。 ユーザー フローの[セルフ サービス パスワード リセット](active-directory-b2c-reference-sspr.md)を構成することもできます。

## <a name="prerequisites"></a>前提条件

「[Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。

## <a name="add-the-elements"></a>要素を追加する 

1. ご自身の *TrustframeworkExtensions.xml* ファイルを開き、識別子が `oldPassword` の次の **ClaimType** 要素を、[ClaimsSchema](claimsschema.md) 要素に追加します。 

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. [ClaimsProvider](claimsproviders.md) 要素には、ユーザーを認証する技術プロファイルが含まれています。 **ClaimsProviders** 要素に次の要素プロバイダーを追加します。

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    `IdentityExperienceFrameworkAppId` を、前提条件のチュートリアルで作成した IdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。 `ProxyIdentityExperienceFrameworkAppId` を、先ほど作成した ProxyIdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。

3. [UserJourney](userjourneys.md) 要素は、ユーザーがアプリケーションとやり取りするときに取るパスを定義します。 `PasswordChange` として識別される **UserJourney** に存在していない場合は、**UserJourneys** 要素を追加します。

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. *TrustFrameworkExtensions.xml* ポリシー ファイルを保存します。
5. スターター パックと一緒にダウンロードした *ProfileEdit.xml* ファイルをコピーして、*ProfileEditPasswordChange.xml* という名前を付けます。
6. 新しいファイルを開き、**PolicyId** 属性を一意の値で更新します。 この値がポリシーの名前になります。 たとえば、*B2C_1A_profile_edit_password_change* です。
7. `<DefaultUserJourney>` の **ReferenceId** 属性を変更して、作成した新しいユーザー体験の ID と一致するようにします。 たとえば、*PasswordChange* です。
8. 変更を保存します。

[こちら](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)でサンプル ポリシーを見つけることができます。 

## <a name="test-your-policy"></a>ポリシーのテスト

Azure AD B2C でアプリケーションをテスト中に、Azure AD B2C トークンを `https://jwt.ms` に返して、その中の要求を見直すことができると便利なことがあります。

### <a name="upload-the-files"></a>ファイルのアップロード

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. [カスタム ポリシー] ページで、**[ポリシーのアップロード]** をクリックします。
6. **[ポリシーが存在する場合は上書きする]** を選択し、*TrustframeworkExtensions.xml* ファイルを検索して選択します。
7. **[アップロード]** をクリックします。
8. その証明書利用者ファイル (*ProfileEditPasswordChange.xml* など) で、手順 5 から 7 を繰り返します。

### <a name="run-the-policy"></a>ポリシーを実行する

1. 変更したポリシーを開きます。 たとえば、*B2C_1A_profile_edit_password_change* です。
2. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 トークンを表示するには、**[応答 URL]** に `https://jwt.ms` が表示される必要があります。
3. **[今すぐ実行]** をクリックします。 前に作成したアカウントでサインインします。 これで、パスワードを変更することができます。 

## <a name="next-steps"></a>次の手順

- [Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの変更を構成する](active-directory-b2c-reference-password-complexity-custom.md)方法について学習します。 
