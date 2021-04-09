---
title: カスタム ポリシーを使用して AD FS を SAML ID プロバイダーとして追加する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で SAML プロトコルとカスタム ポリシーを使用して AD FS 2016 を設定する
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
ms.openlocfilehash: 292a244a4804f97e8622d6841c33b153af373290
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489170"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して SAML ID プロバイダーとして AD FS を追加する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) で[カスタム ポリシー](custom-policy-overview.md)を使用して AD FS ユーザー アカウントのサインインを有効にする方法について説明します。 サインインを有効にするには、[SAML ID プロバイダー](identity-provider-generic-saml.md)をカスタム ポリシーに追加します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

証明書を Azure AD B2C テナントに格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション** については、`Upload`を選択します。
7. ポリシー キーの **名前** を入力します。 たとえば、「 `SAMLSigningCert` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. 秘密キーが含まれている証明書の .pfx ファイルを参照して選択します。
9. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが AD FS アカウントを使用してサインインするようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

AD FS アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。 詳細については、[SAML ID プロバイダーの定義](identity-provider-generic-saml.md)に関するページをご覧ください。

1. *TrustFrameworkExtensions.xml* を開きます。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. `your-AD-FS-domain` を AD FS ドメインの名前に置き換え、**identityProvider** 出力要求の値を DNS (ドメインを示す任意の値) に置き換えます。

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

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>AD FS 証明書利用者信頼を構成する

Azure AD B2C で ID プロバイダーとして AD FS を使用するには、Azure AD B2C SAML メタデータを使用して AD FS 証明書利用者信頼を作成する必要があります。 次の例は、Azure AD B2C テクニカル プロファイルの SAML メタデータへの URL アドレスを示しています。

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

[カスタム ドメイン](custom-domain.md)を使用する場合は、次の形式を使用します。

```
https://your-domain-name/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

次の値を置き換えます。

- **your-tenant-name** を実際のテナント名 (your-tenant.onmicrosoft.com など) に。
- **your-domain-name** を実際のカスタム ドメイン名 (login.contoso.com) に。
- **your-policy** は、実際のポリシー名に置き換えます。 たとえば、「B2C_1A_signup_signin_adfs」とします。
- **your-technical-profile** は、お使いの SAML ID プロバイダー技術プロファイルの名前に置き換えます。 たとえば、「Contoso-SAML2」とします。

ブラウザーを開き、この URL に移動します。 正しい URL を入力し、XML メタデータ ファイルにアクセスできることを確認します。 AD FS 管理スナップインを使用して新しい証明書利用者信頼を追加するには、手動で設定を構成して、フェデレーション サーバーで次の手順を実行します。 この手順を完了するためには、ローカル コンピューター上の **管理者** のメンバーシップ、またはそれと同等であることが最低限求められます。

1. [サーバー マネージャー] で、 **[ツール]** を選択し、 **[AD FS Management]\(AD FS 管理\)** を選択します。
2. **[証明書利用者信頼の追加]** を選択します。
3. **[ようこそ]** ページで **[要求に対応する]** を選択し、**[開始]** をクリックします。
4. **[データ ソースの選択]** ページで、**[オンラインまたはローカル ネットワークで公開されている証明書利用者についてのデータをインポートする]** を選択し、Azure AD B2C メタデータ URL を入力し、**[次へ]** をクリックします。
5. **[表示名の指定]** ページで、**[表示名]** を入力し、**[メモ]** にこの証明書利用者の説明を入力して、**[次へ]** をクリックします。
6. **[アクセス制御ポリシーの選択]** ページで、ポリシーを選択して、**[次へ]** をクリックします。
7. [**信頼の追加の準備完了**] ページで、設定を確認し、[**次へ**] をクリックして、証明書利用者信頼情報を保存します。
8. **[完了]** ページで、**[閉じる]** をクリックすると、この操作によって、**[要求規則の編集]** ダイアログ ボックスが自動的に表示されます。
9. **[規則の追加]** を選択します。
10. **[要求規則テンプレート]** で、**[LDAP 属性を要求として送信]** を選択します。
11. **[要求規則名]** を指定します。 **[属性ストア]** で、 **[Active Directory の選択]** を選択し、次の要求を追加し、 **[完了]** 、 **[OK]** の順にクリックします。

    | LDAP 属性 | 出力方向の要求の種類 |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |

    これらの名前は、[出力方向の要求の種類] ボックスの一覧には表示されないので注意してください。 手動で入力する必要があります  (ドロップダウンは実際に編集可能です)。

12.  証明書の種類によっては、HASH アルゴリズムを設定する必要があります。 証明書利用者信頼 (B2C デモ) のプロパティ ウィンドウで、**[詳細]** タブを選択して、**[セキュア ハッシュ アルゴリズム]** を `SHA-256` に変更し、**[OK]** をクリックします。
13. [サーバー マネージャー] で、 **[ツール]** を選択し、 **[AD FS Management]\(AD FS 管理\)** を選択します。
14. 作成した証明書利用者信頼を選択し、**[フェデレーション メタデータから更新]** を選択し、**[更新]** をクリックします。

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](tutorial-register-applications.md) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso AD FS]** を選択して Contoso AD FS ID プロバイダーでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

## <a name="troubleshooting-ad-fs-service"></a>AD FS サービスのトラブルシューティング  

AD FS は、Windows アプリケーション ログを使用するように構成されています。 Azure AD B2C でカスタム ポリシーを使用して SAML ID プロバイダーとして AD FS を設定するときに問題が発生した場合は、次のように AD FS イベント ログを確認してください。

1. Windows の **[検索バー]** に「**イベント ビューアー**」と入力し、**イベント ビューアー** デスクトップ アプリを選択します。
1. 別のコンピューターのログを表示するには、 **[イベント ビューアー (ローカル)]** を右クリックします。 **[別のコンピューターへ接続]** を選択し、フィールドに入力して **[コンピューターの選択]** ダイアログ ボックスの設定を完了します。
1. **イベント ビューアー** で、 **[アプリケーションとサービス ログ]** を開きます。
1. **[AD FS]** 、 **[管理者]** の順に選択します。 
1. イベントについての詳細情報を表示するには、イベントをダブルクリックします。  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>SAML 要求が、必要な署名アルゴリズム イベントで署名されていない

このエラーは、Azure AD B2C によって送信された SAML 要求が、AD FS で構成されている必要な署名アルゴリズムで署名されていないことを示します。 たとえば、SAML 要求は署名アルゴリズム `rsa-sha256` で署名されていますが、必要な署名アルゴリズムは `rsa-sha1` です。 この問題を解決するには、Azure AD B2C と AD FS の両方が同じ署名アルゴリズムで構成されていることを確認してください。

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>オプション 1: Azure AD B2C で署名アルゴリズムを設定する  

Azure AD B2C で SAML 要求に署名する方法を構成できます。 [XmlSignatureAlgorithm](identity-provider-generic-saml.md) メタデータは、SAML 要求の `SigAlg` パラメーター (クエリ文字列または post パラメーター) の値を制御します。 次の例では、`rsa-sha256` 署名アルゴリズムを使用するように Azure AD B2C を構成します。

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>オプション 2:AD FS で署名アルゴリズムを設定する 

または、AD FS で必要な SAML 要求署名アルゴリズムを構成することもできます。

1. [サーバー マネージャー] で、 **[ツール]** を選択し、 **[AD FS Management]\(AD FS 管理\)** を選択します。
1. 先ほど作成した **証明書利用者信頼** を選択します。
1. **[プロパティ]** 、 **[詳細]** の順に選択します。
1. **[セキュリティで保護されたハッシュ アルゴリズム]** を構成し、 **[OK]** を選択して変更を保存します。

::: zone-end
