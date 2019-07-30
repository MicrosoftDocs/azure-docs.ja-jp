---
title: Azure Active Directory B2C のカスタム ポリシーを使って ADFS を SAML ID プロバイダーとして追加する | Microsoft Docs
description: Azure Active Directory B2C で SAML プロトコルとカスタム ポリシーを使用して ADFS 2016 を設定する
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2c469b333c6896d33b440bfadf0ebbdbeece71a3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272137"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して SAML ID プロバイダーとして ADFS を追加する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して ADFS ユーザー アカウントのサインインを有効にする方法について説明します。 [SAML 技術プロファイル](saml-technical-profile.md)をカスタム ポリシーに追加することで、サインインを有効にします。

## <a name="prerequisites"></a>前提条件

- 「[Azure Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。
- 秘密キーで証明書 .pfx ファイルにアクセスできることを確認してください。 独自の署名付き証明書を生成し、それを Azure AD B2C にアップロードできます。 Azure AD B2C は、この証明書を使用して、SAML ID プロバイダーに送信された SAML 要求に署名します。

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

証明書を Azure AD B2C テナントに格納する必要があります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリとサブスクリプション] フィルター**を選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション**については、`Upload`を選択します。
7. ポリシー キーの**名前**を入力します。 たとえば、「 `SamlCert` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. 秘密キーが含まれている証明書の .pfx ファイルを参照して選択します。
9. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが ADFS アカウントを使用してサインインするようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。 

ADFS アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. `your-ADFS-domain` を ADFS ドメインの名前に置き換え、**identityProvider** 出力要求の値を DNS (ドメインを示す任意の値) に置き換えます。
5. ファイルを保存します。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C が ADFS アカウントと通信する方法を認識できるようにポリシーを構成しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

> [!NOTE]
> Visual Studio Code B2C 拡張機能では、"socialIdpUserId" が使用されます。 ADFS にはソーシャル ポリシーも必要です。
>

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点で、ID プロバイダーは設定されていますが、まだどのサインアップまたはサインイン画面でも使用できません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、それを ADFS ID プロバイダーも含まれるように変更します。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInADFS` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップまたはサインイン画面の ID プロバイダーのボタンに類似しています。 ADFS アカウントのために **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelections** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`ContosoExchange` など) に設定します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションは、Azure AD B2C がトークンを受信するために ADFS アカウントと通信することです。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。ID には、**TargetClaimsExchangeId** に使用したのと同じ値を使用するようにしてください。

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。 たとえば、「 `Contoso-SAML2` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。


## <a name="configure-an-adfs-relying-party-trust"></a>ADFS 証明書利用者信頼を構成する

Azure AD B2C で ID プロバイダーとして ADFS を使用するには、Azure AD B2C SAML メタデータを使用して ADFS 証明書利用者信頼を作成する必要があります。 次の例は、Azure AD B2C テクニカル プロファイルの SAML メタデータへの URL アドレスを示しています。

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

次の値を置き換えます。

- **your-tenant** は、実際のテナント名 (your-tenant.onmicrosoft.com など) に置き換えます。
- **your-policy** は、実際のポリシー名に置き換えます。 たとえば、「B2C_1A_signup_signin_adfs」とします。
- **your-technical-profile** は、お使いの SAML ID プロバイダー技術プロファイルの名前に置き換えます。 たとえば、「Contoso-SAML2」とします。
 
ブラウザーを開き、この URL に移動します。 正しい URL を入力し、XML メタデータ ファイルにアクセスできることを確認します。 ADFS 管理スナップインを使用して新しい証明書利用者信頼を追加するには、手動で設定を構成して、フェデレーション サーバーで次の手順を実行します。 この手順を完了するためには、ローカル コンピューター上の**管理者**のメンバーシップ、またはそれと同等であることが最低限求められます。

1. [サーバー マネージャー] で、 **[ツール]** を選択し、 **[ADFS Management]\(ADFS 管理\)** を選択します。
2. **[証明書利用者信頼の追加]** を選択します。
3. **[ようこそ]** ページで **[要求に対応する]** を選択し、 **[開始]** をクリックします。
4. **[データ ソースの選択]** ページで、 **[オンラインまたはローカル ネットワークで公開されている証明書利用者についてのデータをインポートする]** を選択し、Azure AD B2C メタデータ URL を入力し、 **[次へ]** をクリックします。
5. **[表示名の指定]** ページで、 **[表示名]** を入力し、 **[メモ]** にこの証明書利用者の説明を入力して、 **[次へ]** をクリックします。
6. **[アクセス制御ポリシーの選択]** ページで、ポリシーを選択して、 **[次へ]** をクリックします。
7. **[信頼の追加の準備完了]** ページで、設定を確認し、 **[次へ]** をクリックして証明書利用者信頼の情報を保存します。
8. **[完了]** ページで、 **[閉じる]** をクリックすると、この操作によって、 **[要求規則の編集]** ダイアログ ボックスが自動的に表示されます。
9. **[規則の追加]** を選択します。  
10. **[要求規則テンプレート]** で、 **[LDAP 属性を要求として送信]** を選択します。
11. **[要求規則名]** を指定します。 **[属性ストア]** で、 **[Active Directory の選択]** を選択し、次の要求を追加し、 **[完了]** 、 **[OK]** の順にクリックします。

    | LDAP 属性 | 出力方向の要求の種類 |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | 名前 |
    
12.  証明書の種類によっては、HASH アルゴリズムを設定する必要があります。 証明書利用者信頼 (B2C デモ) のプロパティ ウィンドウで、 **[詳細]** タブを選択して、 **[セキュア ハッシュ アルゴリズム]** を `SHA-256` に変更し、 **[OK]** をクリックします。  
13. [サーバー マネージャー] で、 **[ツール]** を選択し、 **[ADFS Management]\(ADFS 管理\)** を選択します。
14. 作成した証明書利用者信頼を選択し、 **[フェデレーション メタデータから更新]** を選択し、 **[更新]** をクリックします。 

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

Azure AD B2C との通信は、テナントで作成したアプリケーション経由で行われます。 このセクションでは、テスト アプリケーションをまだ作成していない場合にそれを作成するための省略可能な手順を紹介します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、 **[追加]** を選択します。
5. アプリケーションの名前を入力します (*testapp1* など)。
6. **[Web アプリ / Web API]** には `Yes` を選択し、 **[応答 URL]** に `https://jwt.ms` を入力します。
7. **Create** をクリックしてください。

### <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、その名前を *SignUpSignInADFS.xml* に変更します。
2. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInADFS` 」のように入力します。
3. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_adfs` にします。
4. **DefaultUserJourney** 内の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignInADFS) に一致するように更新します。
5. 変更を保存し、ファイルをアップロードしてから、一覧内の新しいポリシーを選択します。
6. 作成した Azure AD B2C アプリケーションが **[アプリケーションの選択]** フィールドで選択されていることを確認し、 **[今すぐ実行]** をクリックしてテストします。

