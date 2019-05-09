---
title: Azure Active Directory B2C でカスタム ポリシーを使用して Salesforce SAML プロバイダーでのサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーを使用して Salesforce SAML プロバイダーでのサインインを設定します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ca96dbd2073540d47fe09caaa8a337e292445590
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486769"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して Salesforce SAML プロバイダーでのサインインを設定する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して Salesforce 組織からのユーザーのサインインを有効にする方法について説明します。 [SAML 技術プロファイル](saml-technical-profile.md)をカスタム ポリシーに追加することで、サインインを有効にします。

## <a name="prerequisites"></a>前提条件

- 「[Azure Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。
- まだ行っていない場合は、[無料の Developer Edition アカウント](https://developer.salesforce.com/signup)にサインアップします。 この記事では、[Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ) を使用します。
- Salesforce 組織用の ["私のドメイン" のセットアップ](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

### <a name="set-up-salesforce-as-an-identity-provider"></a>ID プロバイダーとしての Salesforce のセットアップ

1. [Salesforce にサインインします](https://login.salesforce.com/)。 
2. 左側のメニューで、**[設定]** の下の **[ID]** を展開し、**[ID プロバイダー]** を選択します。
3. **[Enable Identity Provider]\(ID プロバイダーを有効にする\)** を選択します。
4. **[Select the certificate] \(証明書の選択)** の下から、Azure AD B2C と通信するときに Salesforce で使用する証明書を選択します。 既定の証明書を使用できます。 
5. **[Save]** をクリックします。 

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce での接続されたアプリの作成

1. **[Identity Provider]\(ID プロバイダー\)** ページで、**[Service Providers are now created via Connected Apps.]\(接続されたアプリでサービス プロバイダーが作成されました。ここをクリックしてください。\)** をクリックします。
2. **[基本情報]** に、接続されたアプリの必須の値を入力します。
3. **[Web App Settings]\(Web アプリの設定\)** で、**[Enable SAML]\(SAML を有効にする\)** ボックスをオンにします。
4. **[エンティティ ID]** フィールドに、次の URL を入力します。 `your-tenant` の値を、Azure AD B2C テナントの名前に置き換えます。

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. **[ACS URL]** フィールドに、次の URL を入力します。 `your-tenant` の値を、Azure AD B2C テナントの名前に置き換えます。
      
      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. リストの下までスクロールし、**[保存]** をクリックします。

### <a name="get-the-metadata-url"></a>メタデータ URL の取得

1. 接続されたアプリの概要ページで **[管理]** をクリックします。
2. **メタデータ検出エンドポイント**の値をコピーし、保存します。 この記事の後半で、その値を使用します。

### <a name="set-up-salesforce-users-to-federate"></a>フェデレーションのための Salesforce ユーザーの設定

1. 接続されたアプリの **[Manage]\(管理\)** ページで、**[Manage Profiles]\(プロファイルの管理\)** をクリックします。
2. Azure AD B2C とフェデレーションするプロファイル (またはユーザーのグループ) を選択します。 システム管理者は、Salesforce アカウントを使用してフェデレーションを行うことができるように、**[システム管理者]** のチェック ボックスをオンにします。

## <a name="generate-a-signing-certificate"></a>署名証明書を生成する

Salesforce に送信された要求は、Azure AD B2C で署名する必要があります。 署名証明書を生成するには、Azure PowerShell を開き、次のコマンドを実行します。

> [!NOTE]
> 先頭の 2 行のテナント名およびパスワードを必ず更新してください。

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

作成した証明書を Azure AD B2C テナントに格納する必要があります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、**[Identity Experience Framework - プレビュー]** を選択します。
5. **[ポリシー キー]** を選択し、**[追加]** を選択します。
6. **オプション**については、`Upload`を選択します。
7. ポリシーの**名前**を入力します。 たとえば、SAMLSigningCert などです。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. 作成した B2CSigningCert.pfx 証明書を参照して選択します。 
9. 証明書の **[パスワード]** を入力します。
3. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが Salesforce アカウントを使用してサインインするようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。 

Salesforce アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **PartnerEntity** の値を、先ほどコピーした Salesforce メタデータ URL で更新します。
5. **StorageReferenceId** の両方のインスタンスの値を、署名証明書のキーの名前に更新します。 たとえば、B2C_1A_SAMLSigningCert です。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C が Salesforce アカウントと通信する方法を認識できるようにポリシーを構成しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、**[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点で、ID プロバイダーは設定されていますが、まだどのサインアップまたはサインイン画面でも使用できません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、Salesforce ID プロバイダーも含まれるようにそれを変更します。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInSalesforce` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップまたはサインイン画面の ID プロバイダーのボタンに類似しています。 LinkedIn アカウントのために **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`SalesforceExchange` など) に設定します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 ここでのアクションは、Azure AD B2C が Salesforce アカウントと通信してトークンを受信するためのアクションです。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。**TargetClaimsExchangeId** に使用した **Id** と同じ値を必ずご使用ください。

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```
    
    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの **Id** に更新します。 たとえば、「 `LinkedIn-OAUTH` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

Azure AD B2C との通信は、テナントで作成したアプリケーション経由で行われます。 このセクションでは、テスト アプリケーションをまだ作成していない場合にそれを作成するための省略可能な手順を紹介します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、**[追加]** を選択します。
5. アプリケーションの名前を入力します (*testapp1* など)。
6. **[Web アプリ / Web API]** には `Yes` を選択し、**[応答 URL]** に `https://jwt.ms` を入力します。
7. **Create** をクリックしてください。

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、*SignUpSignInSalesforce.xml* に名前を変更します。
2. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInSalesforce` 」のように入力します。
3. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_salesforce` にします。
4. **DefaultUserJourney** の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignInSalesforce) と一致するように更新します。
5. 変更を保存し、ファイルをアップロードし、一覧から新しいポリシーを選択します。
6. 作成した Azure AD B2C アプリケーションが **[アプリケーションの選択]** フィールドで選択されていることを確認し、**[今すぐ実行]** をクリックしてテストします。
