---
title: "Azure Active Directory B2C: カスタム ポリシーを使用した Salesforce SAML プロバイダーの追加 | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーを作成および管理する方法について説明します。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/11/2017
ms.author: parakhj
ms.openlocfilehash: 269cbd80fb6e861fa8588025eec70b6c6e2890d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: SAML を利用した、Salesforce アカウントでのサインイン

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して特定の Salesforce 組織のユーザーのサインインをセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="azure-ad-b2c-setup"></a>Azure AD B2C のセットアップ

Azure Active Directory B2C (Azure AD B2C) で[カスタム ポリシーを使い始める](active-directory-b2c-get-started-custom.md)にあたっての手順がすべて完了していることを確認します。

チェックの内容は次のとおりです

* Azure AD B2C テナントを作成します。
* Azure AD B2C アプリケーションを作成します。
* 2 つのポリシー エンジン アプリケーションを登録します。
* キーをセットアップします。
* スターター パックをセットアップします。

### <a name="salesforce-setup"></a>Salesforce のセットアップ

この記事では、既に以下の作業が完了していることを前提としています。

* Salesforce アカウントへのサインアップ。 [無料の Developer Edition アカウント](https://developer.salesforce.com/signup)にサインアップできます。
* Salesforce 組織用の ["私のドメイン" のセットアップ](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

## <a name="set-up-salesforce-so-users-can-federate"></a>ユーザーがフェデレーションを実行できるようにするための Salesforce のセットアップ

Azure AD B2C が Salesforce と通信できるようにするには、Salesforce のメタデータ URL を取得する必要があります。

### <a name="set-up-salesforce-as-an-identity-provider"></a>ID プロバイダーとしての Salesforce のセットアップ

> [!NOTE]
> この記事では、[Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ) を使用していることを前提としています。

1. [Salesforce にサインインします](https://login.salesforce.com/)。 
2. 左側のメニューで、**[設定]** の下の **[ID]** を展開し、**[ID プロバイダー]** をクリックします。
3. **[Enable Identity Provider] \(ID プロバイダーを有効にする)** をクリックします。
4. **[Select the certificate] \(証明書の選択)** の下から、Azure AD B2C と通信するときに Salesforce で使用する証明書を選択します。 (既定の証明書を使用できます。)[ **Save**] をクリックします。 

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce での接続されたアプリの作成

1. **[ID プロバイダー]** ページで、**[サービス プロバイダー]** に移動します。
2. **[Service Providers are now created via Connected Apps. Click here.]\(接続されたアプリでサービス プロバイダーが作成されました。ここをクリックしてください。\)** をクリックします。
3. **[基本情報]** に、接続されたアプリの必須の値を入力します。
4. **[Web アプリの設定]** で、**[SAML を有効にする]** チェック ボックスを選択します。
5. **[エンティティ ID]** フィールドに、次の URL を入力します。 その際、`tenantName` の値を置き換えてください。
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. **[ACS URL]** フィールドに、次の URL を入力します。 その際、`tenantName` の値を置き換えてください。
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. その他の設定はすべて、既定値のままにしておきます。
8. リストの下までスクロールし、**[保存]** をクリックします。

### <a name="get-the-metadata-url"></a>メタデータ URL の取得

1. 接続されたアプリの概要ページで **[管理]** をクリックします。
2. **メタデータ検出エンドポイント**の値をコピーし、保存します。 この記事の後半で、その値を使用します。

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce ユーザーがフェデレーションを実行するようセットアップする

1. 接続されたアプリの **[管理]** ページから、**[プロファイル]** に移動します。
2. **[プロファイルの管理]** をクリックします。
3. Azure AD B2C とフェデレーションするプロファイル (またはユーザーのグループ) を選択します。 システム管理者は、Salesforce アカウントを使用してフェデレーションを行うことができるように、**[システム管理者]** のチェック ボックスをオンにします。

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Azure AD B2C の署名証明書の生成

Salesforce に送信された要求は、Azure AD B2C で署名する必要があります。 署名証明書を生成するには、Azure PowerShell を開き、次のコマンドを実行します。

> [!NOTE]
> 先頭の 2 行のテナント名およびパスワードを必ず更新してください。

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Azure AD B2C への SAML 署名証明書の追加

次の手順に従って、Azure AD B2C テナントに署名証明書をアップロードします。 

1. Azure AD B2C テナントに移動します。 **[設定]** > **[Identity Experience Framework]** > **[ポリシー キー]** をクリックします。
2. **[+追加]** をクリックした後、次のようにします。
    1. **[オプション]** > **[アップロード]** をクリックします。
    2. **名前**を入力します (たとえば、SAMLSigningCert)。 プレフィックス *B2C_1A_* がキーの名前に自動的に追加されます。
    3. 証明書を選択するには、**ファイルのアップロード コントロール**を選択します。 
    4. PowerShell スクリプトで設定した証明書のパスワードを入力します。
3. **Create** をクリックしてください。
4. キー (たとえば、B2C_1A_SAMLSigningCert) を作成したことを確認します。 フルネームをメモします (*B2C_1A_* を含む)。 ポリシーで、後でこのキーを参照します。

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>基本ポリシーでの Salesforce SAML 要求プロバイダーの作成

ユーザーが Salesforce を使用してサインインできるようにするには、Salesforce を要求プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用する一連の*要求*を*提供します*。 これを行うには、ポリシーの拡張ファイルで Salesforce の `<ClaimsProvider>` を追加します。

1. 作業ディレクトリから拡張ファイル (TrustFrameworkExtensions.xml) を開きます。
2. セクション `<ClaimsProviders>` を探します。 存在しない場合は、ルート ノードの下に作成します。
3. 新しい `<ClaimsProvider>` を追加します。

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
            <Item Key="RequestsSigned">false</Item>
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
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
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

`<ClaimsProvider>` ノードの下で、以下の操作を実行します。

1. `<Domain>` の値を、他の ID プロバイダーと `<ClaimsProvider>` を区別する一意の値に変更します。
2. `<DisplayName>` の値を、要求プロバイダーの表示名に更新します。 現在、この値は使用されていません。

### <a name="update-the-technical-profile"></a>技術プロファイルの更新

Salesforce から SAML トークンを取得するためには、Azure AD B2C が Azure Active Directory (Azure AD) との通信に使用するプロトコルを定義します。 `<ClaimsProvider>` の `<TechnicalProfile>` 要素でこれを行います。

1. `<TechnicalProfile>` ノードの ID を更新します。 この ID は、ポリシーの他の部分からこの技術プロファイルを参照するために使用します。
2. `<DisplayName>` の値を更新します。 この値は、サインイン ページのサインイン ボタン上に表示されます。
3. `<Description>` の値を更新します。
4. Salesforce では、SAML 2.0 プロトコルが使用されます。 `<Protocol>` の値が **SAML2** であることを確認します。

特定の Salesforce アカウントの設定を反映するように、上の XML の `<Metadata>` セクションを更新する必要があります。 XML で、次のようにメタデータ値を更新します。

1. `<Item Key="PartnerEntity">` の値を、先ほどコピーした Salesforce メタデータ URL で更新します。 形式は次のとおりです。 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. `<CryptographicKeys>` セクションで、`StorageReferenceId` の両方のインスタンスの値を署名証明書のキーの名前 (たとえば、B2C_1A_SAMLSigningCert) に更新します。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

これで、Azure AD B2C が Salesforce と通信する方法を認識するようにポリシーが設定されました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。 ポリシーの拡張ファイルをアップロードするには、次の手順に従います。

1. Azure AD B2C テナントの **[すべてのポリシー]** ブレードに移動します。
2. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
3. 拡張ファイル (TrustFrameworkExtensions.xml) をアップロードします。 検証が失敗しないことを確認します。

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>ユーザー体験への Salesforce SAML 要求プロバイダーの登録

次に、Salesforce SAML の ID プロバイダーをいずれかのユーザー体験に追加します。 この時点では、ID プロバイダーはセットアップされていますが、ユーザーのサインアップまたはサインイン ページで使用することはできません。 サインイン ページに ID プロバイダーを追加するにはまず、既存のテンプレート ユーザー体験の複製を作成します。 次に、テンプレートを変更して、Azure AD ID プロバイダーも含めます。

1. ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2. `<UserJourneys>` 要素を見つけて、Id ="SignUpOrSignIn" を含め、`<UserJourney>` 値全体をコピーします。
3. 拡張ファイル (例: TrustFrameworkExtensions.xml) を開きます。 `<UserJourneys>` 要素を見つけます。 要素が存在しない場合は作成します。
4. コピーした `<UserJourney>` 全体を `<UserJourneys>` 要素の子として貼り付けます。
5. 新しい `<UserJourney>` の ID の名前を変更します (例: SignUpOrSignUsingContoso)。

### <a name="display-the-identity-provider-button"></a>ID プロバイダー ボタンの表示

`<ClaimsProviderSelection>` 要素は、サインアップまたはサインイン ページの ID プロバイダーのボタンに類似しています。 Salesforce の `<ClaimsProviderSelection>` 要素を追加することで、ユーザーがこのページに移動したときに新しいボタンが表示されます。 ID プロバイダー ボタンを表示するには、次の手順に従います。

1. 作成した `<UserJourney>` で `Order="1"` になっている `<OrchestrationStep>` を見つけます。
2. 次の XML を追加します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. `TargetClaimsExchangeId` を論理値に設定します。 他の場合と同じ規則に従うことをお勧めします (例: *\[ClaimProviderName\]Exchange*)。

### <a name="link-the-identity-provider-button-to-an-action"></a>アクションへの ID プロバイダー ボタンのリンク

ID プロバイダー ボタンが所定の位置に配置されたので、ボタンをアクションにリンクします。 この場合のアクションでは、Azure AD B2C が Salesforce と通信して SAML トークンを受信します。 これを行うには、Salesforce SAML 要求プロバイダーの技術プロファイルをリンクします。

1. `<UserJourney>` ノードで `Order="2"` になっている `<OrchestrationStep>` を見つけます。
2. 次の XML を追加します。

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. `Id` を、`TargetClaimsExchangeId` で前に使用したものと同じ値に更新します。
4. `TechnicalProfileReferenceId` を、前に作成した技術プロファイルの `Id` に更新します (例: ContosoProfile)。

### <a name="upload-the-updated-extension-file"></a>更新済み拡張ファイルのアップロード

拡張ファイルの変更が終了しました。 このファイルを保存してアップロードします。 すべての検証が成功することを確認します。

### <a name="update-the-relying-party-file"></a>証明書利用者ファイルの更新

次に、作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに SignUpOrSignIn.xml のコピーを作成します。 次に、名前を変更します (例: SignUpOrSignInWithAAD.xml)。
2. 新しいファイルを開き、`<TrustFrameworkPolicy>` の `PolicyId` 属性を一意の値で更新します。 これがポリシーの名前になります (例: SignUpOrSignInWithAAD)。
3. `<DefaultUserJourney>` の `ReferenceId` 属性を変更して、作成した新しいユーザー体験の `Id` と一致するようにします (例: SignUpOrSignUsingContoso)。
4. 変更内容を保存し、ファイルをアップロードします。

## <a name="test-and-troubleshoot"></a>テストとトラブルシューティング

アップロードしたカスタムのポリシーをテストするには、Azure Portal でポリシー ブレードに移動し、**[今すぐ実行]** をクリックします。 失敗する場合は、「[カスタム ポリシーのトラブルシューティング](active-directory-b2c-troubleshoot-custom.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) にフィードバックを提供します。
