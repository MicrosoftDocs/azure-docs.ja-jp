---
title: SAML ID プロバイダーでのサインアップおよびサインインを設定する
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C で任意の SAML ID プロバイダー (IdP) でのサインアップとサインインを設定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a66486d791968f5752b96ed00374f8662b9c30fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580047"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して SAML ID プロバイダーでのサインアップとサインインを設定する

Azure Active Directory B2C (Azure AD B2C) では、SAML 2.0 ID プロバイダーとのフェデレーションがサポートされています。 この記事では、SAML ID プロバイダー ユーザー アカウントでのサインインを有効にし、ユーザーが既存のソーシャルまたはエンタープライズの ID ([ADFS](./identity-provider-adfs.md)、[Salesforce](identity-provider-salesforce-saml.md) など) を使用してサインインできるようにする方法について説明します。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>シナリオの概要

ユーザーが外部のソーシャルまたはエンタープライズの SAML ID プロバイダー (IdP) の資格情報を使用してアプリケーションにサインインできるように、Azure AD B2C を構成できます。 Azure AD B2C が SAML ID プロバイダーとフェデレーションを行う場合、SAML **ID プロバイダー** への SAML 要求を開始して、SAML 応答を待機する **サービス プロバイダー** として機能します。 次の図で説明します。

1. アプリケーションが Azure AD B2C への認可要求を開始します。 このアプリケーションは、[OAuth 2.0](protocols-overview.md) または [OpenId Connect](openid-connect.md) アプリケーション、または [SAML サービス プロバイダー](saml-service-provider.md)である場合があります。 
1. Azure AD B2C のサインイン ページで、ユーザーは SAML ID プロバイダー アカウント (*Contoso* など) でサインインすることを選択します。 Azure AD B2C が SAML 認可要求を開始し、サインインを完了するためにユーザーを SAML ID プロバイダーに移動させます。
1. SAML ID プロバイダーが SAML 応答を返します。
1. Azure AD B2C が SAML トークンを検証し、要求を抽出して、独自のトークンを発行し、ユーザーをアプリケーションに戻します。  

![SAML ID プロバイダーでのサインインのフロー](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>ソリューションのコンポーネント

このシナリオに必要なコンポーネントは次のとおりです。

* Azure AD B2C から SAML 要求を受信、デコード、応答する機能を持つ SAML **ID プロバイダー**。
* お使いの ID プロバイダー向けに公開されている SAML **メタデータ エンドポイント**。
* [Azure AD B2C テナント](tutorial-create-tenant.md)。
 

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C と SAML ID プロバイダー間の信頼関係を確立するには、有効な X509 証明書と秘密キーを提供する必要があります。 Azure AD B2C は、証明書の秘密キーを使用して SAML 要求に署名します。 ID プロバイダーは、証明書の公開キーを使用して要求を検証します。 公開キーには、技術プロファイル メタデータを介してアクセスできます。 代わりに、.cer ファイルを SAML ID プロバイダーに手動でアップロードすることもできます。

自己署名証明書は、ほとんどのシナリオで受け入れ可能です。 運用環境では、証明機関が発行した X509 証明書を使用することをお勧めします。 また、このドキュメントの後半で説明する、非運用環境に無効では、両側で SAML の署名を無効にできます。

### <a name="obtain-a-certificate"></a>証明書を取得する

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>証明書をアップロードする

証明書を Azure AD B2C テナントに格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Upload`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `SAMLSigningCert` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. 秘密キーが含まれている証明書の .pfx ファイルを参照して選択します。
1. **Create** をクリックしてください。

## <a name="configure-the-saml-technical-profile"></a>SAML 技術プロファイルを構成する

SAML ID プロバイダーを定義するには、それをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。 クレーム プロバイダーには、SAML ID プロバイダーと通信するために必要なエンドポイントとプロトコルを決定する SAML 技術プロファイルが含まれています。 SAML 技術プロファイルを持つクレーム プロバイダーを追加するには、次のようにします。

1. *TrustFrameworkExtensions.xml* を開きます。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

関連する値を使用して、次の XML 要素を更新します。

|XML 要素  |値  |
|---------|---------|
|ClaimsProvider\Domain  | [直接サインイン](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)に使用するドメイン名。 直接サインインに使用するドメイン名を入力します。 たとえば、*Contoso.com* などです |
|TechnicalProfile\DisplayName|この値は、サインイン画面のサインイン ボタン上に表示されます。 たとえば、*Contoso* です。 |
|Metadata\PartnerEntity|SAML ID プロバイダーのメタデータの URL です。 または、ID プロバイダーのメタデータをコピーし、CDATA 要素 `<![CDATA[Your IDP metadata]]>` 内に追加します。|

## <a name="map-the-claims"></a>要求をマップする

**OutputClaims** 要素には、SAML ID プロバイダーにより返される要求の一覧が存在します。 お使いのポリシーに定義されている要求の名前を、ID プロバイダーで定義されているアサーション名にマップします。 要求 (アサーション) の一覧については、お使いの ID プロバイダーを確認してください。 詳細については、[要求のマッピング](identity-provider-generic-saml-options.md#claims-mapping)に関する記事を参照してください。

上の例では、*Contoso-SAML2* には、SAML ID プロバイダーから返された要求が含まれています。

* **issuerUserId** 要求は、**assertionSubjectName** 要求にマップされます。
* **givenName** 要求にマップされている **first_name** 要求。
* **surname** 要求にマップされている **last_name** 要求。
* **displayName** 要求は、`http://schemas.microsoft.com/identity/claims/displayname` 要求にマップされます。
* どの名前にもマップされていない **email** 要求。

また、技術プロファイルは、ID プロバイダーにより返されない要求も返します。

* ID プロバイダーの名前を保持する **identityProvider** 要求。
* 既定値の **socialIdpAuthentication** である **authenticationSource** 要求。
 
### <a name="add-the-saml-session-technical-profile"></a>SAML セッション技術プロファイルを追加する

まだ `SM-Saml-idp` SAML セッション技術プロファイルがない場合は、お使いの拡張ポリシーに追加します。 `<ClaimsProviders>` セクションを見つけて、次の XML スニペットを追加します。 ポリシーに `SM-Saml-idp` 技術プロファイルが既に含まれている場合、次の手順に進みます。 詳細については、[シングル サインオン セッション管理](custom-policy-reference-sso.md)に関するページを参照してください。

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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>SAML ID プロバイダーを構成する

ポリシーを構成したら、Azure AD B2C SAML メタデータを使用して SAML ID プロバイダーを構成する必要があります。 SAML メタデータは、ポリシー (**サービス プロバイダー**)の構成を公開するために SAML プロトコルで使用される情報です。 それは、サインインとサインアウト、証明書、サインイン方法など、サービスの場所を定義します。

SAML ID プロバイダーごとに、サービス プロバイダーを設定する手順が異なります。 SAML ID プロバイダーによっては、Azure AD B2C メタデータを要求するものもあれば、ユーザーがメタデータ ファイルを手動で調べて情報を提供する必要があるものもあります。 ガイダンスについては、ID プロバイダーのドキュメントを参照してください。

次の例は、Azure AD B2C 技術プロファイルの SAML メタデータの URL アドレスを示しています。

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

[カスタム ドメイン](custom-domain.md)を使用する場合は、次の形式を使用します。

```
https://your-domain-name/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

次の値を置き換えます。

- **your-tenant-name** を実際のテナント名 (your-tenant.onmicrosoft.com など) に。
- **your-domain-name** を実際のカスタム ドメイン名 (login.contoso.com) に。
- **your-policy** は、実際のポリシー名に置き換えます。 たとえば、「B2C_1A_signup_signin_adfs」とします。
- **your-technical-profile** は、お使いの SAML ID プロバイダー技術プロファイルの名前に置き換えます。 たとえば、「Contoso-SAML2」とします。

ブラウザーを開き、この URL に移動します。 正しい URL を入力し、XML メタデータ ファイルにアクセスできることを確認します。

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](troubleshoot-custom-policies.md#troubleshoot-the-runtime) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso]** を選択し、Contoso アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2C を使用して SAML ID プロバイダー オプションを構成する](identity-provider-generic-saml-options.md)

::: zone-end