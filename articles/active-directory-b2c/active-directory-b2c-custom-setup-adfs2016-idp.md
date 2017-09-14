---
title: "Azure Active Directory B2C: カスタム ポリシーを使って ADFS を SAML ID プロバイダーとして追加する"
description: "SAML プロトコルとカスタム ポリシーを使用した ADFS 2016 の設定方法に関する記事"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 6cd0d19e5fd90cb9fb6d3fc4c17119476d7b4f62
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使って ADFS を SAML ID プロバイダーとして追加する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使って ADFS アカウントからのユーザーのサインインを有効にする方法を示します。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

その手順は次のとおりです。

1.  ADFS 証明書利用者信頼を作成する。
2.  Azure AD B2C に ADFS 証明書利用者信頼の証明書を追加する。
3.  ポリシーにクレーム プロバイダーを追加する。
4.  ユーザー体験に ADFS アカウント クレーム プロバイダーを登録する。
5.  Azure AD B2C テナントにポリシーをアップロードしてテストする。

## <a name="to-create-a-claims-aware-relying-party-trust"></a>要求に対応する証明書利用者信頼を作成するには

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして ADFS を使用するには、ADFS 証明書利用者信頼を作成し、適切なパラメーターを提供する必要があります。

AD FS 管理スナップインを使用して新しい証明書利用者信頼を追加するには、手動で設定を構成して、フェデレーション サーバーで次の手順を実行します。

この手順を完了するためには、ローカル コンピューター上の**管理者**のメンバーシップ、またはそれと同等の権限が最低限必要です。 適切なアカウントとグループ メンバーシップの使用に関する詳細については、[ローカルおよびドメインの既定のグループ](http://go.microsoft.com/fwlink/?LinkId=83477)に関する記事をご覧ください。

1.  [サーバー マネージャー] で、**[ツール]** をクリックし、**[ADFS Management\(ADFS 管理\)]** を選びます。

2.  **[証明書利用者信頼の追加]** をクリックします。
    ![証明書利用者信頼の追加](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  **[ようこそ]** ページで **[要求に対応する]** を選び、**[開始]** をクリックします。
    ![[ようこそ] ページで、[要求に対応する] を選択する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  **[データ ソースの選択]** ページで、**[証明書利用者に関するデータを手動で入力する]** をクリックし、**[次へ]** をクリックします。
    ![証明書利用者に関するデータを入力する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  **[表示名の指定]** ページで、**[表示名]**に名前を入力し、**[メモ]**にこの証明書利用者の説明を入力して、**[次へ]** をクリックします。
    ![表示名とメモを指定する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  省略可能。 省略可能なトークン暗号化証明書がある場合、**[証明書の構成]** ページで、**[参照]** をクリックして証明書ファイルを検索し、**[次へ]** をクリックします。
    ![証明書を構成する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  **[URL の構成]** ページで、**[SAML 2.0 WebSSO プロトコルのサポートを有効にする]** チェック ボックスをオンにします。 **[証明書利用者 SAML 2.0 SSO サービスの URL]**で、この証明書利用者信頼の Security Assertion Markup Language (SAML) サービス エンドポイントの URL を入力し、**[次へ]** をクリックします。  **[証明書利用者 SAML 2.0 SSO サービスの URL]** に、`https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}` を貼り付けます。 {tenant} を使用しているテナントの名前 (contosob2c.onmicrosoft.com など) に置き換えて、{policy} を拡張ポリシーの名前 (B2C_1A_TrustFrameworkExtensions など) に置き換えます。
    > [!IMPORTANT]
    >ポリシーの名前は、signup_ または _signin ポリシーが継承しているものです。この場合は、`B2C_1A_TrustFrameworkExtensions` になります。
    >例として、URL は https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase** のようになります。

    ![証明書利用者 SAML 2.0 SSO サービスの URL](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. **[識別子の構成]** ページで、前の手順と同じ URL を指定して、**[追加]** をクリックして一覧に追加し、**[次へ]**をクリックします。
    ![証明書利用者信頼の識別子](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  **[アクセス制御ポリシーの選択]** で、ポリシーを選択して、**[次へ]** をクリックします。
    ![アクセス制御ポリシーを選択する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  **[信頼の追加の準備完了]** ページで、設定を確認し、**[次へ]** をクリックして証明書利用者信頼の情報を保存します。
    ![証明書利用者信頼の情報を保存する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  **[完了]** ページで、**[閉じる]** をクリックすると、この操作によって、**[要求規則の編集]** ダイアログ ボックスが自動的に表示されます。
    ![要求規則を編集する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. **[規則の追加]**をクリックします。  
      ![新しい規則を追加する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  **[要求規則テンプレート]** で、**[LDAP 属性を要求として送信]** を選択します。
    ![[LDAP 属性を要求として送信] テンプレート規則を選択する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  **[要求規則名]** を指定します。 **[属性ストア]**で、**[Active Directory] を選択して**次の要求を追加し、**[完了]**、**[OK]** の順にクリックします。
    ![規則のプロパティを設定する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  サーバー マネージャーで、**[証明書利用者信頼]** を選択し、作成した証明書利用者信頼を選んで **[プロパティ]** をクリックします。
    ![証明書利用者の編集プロパティ](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  証明書利用者信頼 (B2C デモ) のプロパティ ウィンドウで、**[署名]** タブをクリックして、**[追加]** をクリックします。  
    ![署名を設定する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  使用している署名証明書 (.cert ファイル、秘密キーなし) を追加します。  
    ![署名証明書を追加する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  証明書利用者信頼 (B2C デモ) のプロパティ ウィンドウで、**[詳細]** タブをクリックして、**[セキュア ハッシュ アルゴリズム]** を **[SHA-1]** に変更して、**[Ok]** をクリックします。  
    ![セキュア ハッシュ アルゴリズムを SHA-1 に設定する](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Azure AD B2C に ADFS アカウント アプリケーション キーを追加する
ADFS アカウントでのフェデレーションには、アプリケーションに代わって Azure AD B2C を信頼するために、ADFS アカウント用のクライアント シークレットが必要になります。 Azure AD B2C テナントに ADFS 証明書を格納する必要があります。 

1.  Azure AD B2C テナントに移動し、**[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  **[+ 追加]** をクリックします。
4.  **[オプション]** には **[アップロード]** を使用します。
5.  **[名前]** には `ADFSSamlCert` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。
6.  [ファイルのアップロード] で、**秘密キーを備えた証明書の .pfx ファイルを選択します。 注: この証明書 (秘密キーを備えている) は、ADFS 証明書利用者に発行され使用されているものと同一にする必要があります。
![ポリシー キーをアップロードする](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  **[作成]**
8.  キー `B2C_1A_ADFSSamlCert` を作成したことを確認します。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>拡張ポリシーにクレーム プロバイダーを追加する
ユーザーが ADFS アカウントを使ってサインインできるようにするには、ADFS アカウントをクレーム プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

拡張ポリシー ファイルに `<ClaimsProvider>` ノードを追加することで、ADFS をクレーム プロバイダーとして定義します。

1. 作業ディレクトリから拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。
2. `<ClaimsProviders>` セクションを探します。
3. 次の XML スニペットを `ClaimsProviders` 要素に追加し、`identityProvider` を DNS (使用しているドメインを示す任意の値) と置き換えて、ファイルを保存します。 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>サインアップまたはサインイン ユーザー体験に ADFS アカウントクレーム プロバイダーを登録する
この時点で、ID プロバイダーが設定されました。  ただし、いずれのサインアップ/サインイン画面でも、使用可能ではありません。 ADFS アカウント ID プロバイダーをユーザーの `SignUpOrSignIn` ユーザー体験に追加する必要があります。 使用可能にするには、既存のテンプレート ユーザー体験の複製を作成します。  その後、ADFS ID プロバイダーを含むように、この複製を編集します。
    >[!NOTE]
    >If you previously copied the `<UserJourneys>` element from base file of your policy to the extension file (TrustFrameworkExtensions.xml) you can skip this section.
1.  ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2.  `<UserJourneys>` 要素を見つけて、`<UserJourneys>` ノードのコンテンツ全体をコピーします。
3.  拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
4.  コピーした `<UserJournesy>` ノードのコンテンツ全体を `<UserJourneys>` 要素の子として貼り付けます。

### <a name="display-the-button"></a>ボタンを表示する
`<ClaimsProviderSelections>` 要素は、クレーム プロバイダーの選択オプションとその順序の一覧を定義します。  `<ClaimsProviderSelection>` 要素は、サインアップ/サインイン ページの ID プロバイダーのボタンに類似しています。 ADFS アカウントのために `<ClaimsProviderSelection>` 要素を追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 この要素を追加するには、次の手順を実行します。

1.  コピーしたばかりのユーザー体験内で、`Id="SignUpOrSignIn"` を含む `<UserJourney>` ノードを見つけます。
2.  `Order="1"` を含む `<OrchestrationStep>` ノードを特定する
3.  `<ClaimsProviderSelections>` ノード下に次の XML スニペットを追加します。

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が ADFS アカウントと通信してトークンを受信します。 ADFS アカウント クレーム プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  `<ClaimsExchanges>` ノード下に次の XML スニペットを追加します。

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * `Id` が前のセクションの `TargetClaimsExchangeId` と同じ値であることを確認します。
> * `TechnicalProfileReferenceId` が前に作成した技術プロファイル (Contoso-SAML2) に設定されていることを確認します。

## <a name="upload-the-policy-to-your-tenant"></a>ポリシーをテナントにアップロードする
1.  [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、**[Azure AD B2C]** ブレードを開きます。
2.  **[Identity Experience Framework]** を選択します。
3.  **[すべてのポリシー]** ブレードを開きます。
4.  **[ポリシーのアップロード]** を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。

## <a name="test-the-custom-policy-by-using-run-now"></a>[今すぐ実行] を使用してカスタム ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。
3.  ADFS アカウントを使ってサインインすることができます。

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[省略可能] プロファイル編集ユーザー体験に ADFS アカウント クレーム プロバイダーを登録する
ADFS アカウント ID プロバイダーをユーザーの `ProfileEdit` ユーザー体験に追加することもできます。 利用できるようには、最後の 2 つの手順を繰り返します。

### <a name="display-the-button"></a>ボタンを表示する
1.  ポリシーの拡張ファイル (例: TrustFrameworkExtensions.xml) を開きます。
2.  コピーしたばかりのユーザー体験内で、`Id="ProfileEdit"` を含む `<UserJourney>` ノードを見つけます。
3.  `Order="1"` を含む `<OrchestrationStep>` ノードを見つける
4.  `<ClaimsProviderSelections>` ノード下に次の XML スニペットを追加します。

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク
1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  `<ClaimsExchanges>` ノード下に次の XML スニペットを追加します。

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>[今すぐ実行] を使ってカスタム プロファイル編集ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_ProfileEdit** を開きます。 **[今すぐ実行]** を選択します。
3.  ADFS アカウントを使ってサインインすることができます。

## <a name="download-the-complete-policy-files"></a>完全なポリシー ファイルをダウンロードする
省略可能: カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシー ファイルを使ってシナリオを構築することをお勧めします。 [参照専用のポリシー サンプル ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)

