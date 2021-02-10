---
title: Azure AD B2C を SAML IdP としてアプリケーションに構成する
title-suffix: Azure AD B2C
description: アプリケーション (サービス プロバイダー) に SAML プロトコル アサーションを提供するように Azure AD B2C を構成する方法。 Azure AD B2C は、SAML アプリケーションに対して単一の ID プロバイダー (IdP) として機能します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 96a72dbc0e45ebd50a49000ae66e3713cb28aa9a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916925"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>SAML アプリケーションを Azure AD B2C に登録する

この記事では、アプリケーションに対して Security Assertion Markup Language (SAML) ID プロバイダー (IdP) として機能するように Azure Active Directory B2C (Azure AD B2C) を構成する方法について説明します。

## <a name="scenario-overview"></a>シナリオの概要

顧客 ID およびアクセス管理ソリューションとして Azure AD B2C を使用する組織では、ID プロバイダー、または SAML プロトコルを使用して認証するように構成されたアプリケーションとのやり取りが必要になる場合があります。

Azure AD B2C は、次の 2 つの方法のいずれかで SAML 相互運用性を実現します。

* "*ID プロバイダー*" (IdP) として機能し、SAML ベースのサービス プロバイダー (アプリケーション) でシングル サインオン (SSO) を実現する
* "*サービス プロバイダー*" (SP) として機能し、Salesforce や ADFS など、SAML ベースの ID プロバイダーとやり取りする

![左側が ID プロバイダーとして機能する B2C、右側がサービスプロバイダーとして機能する B2C の図](media/saml-identity-provider/saml-idp-diagram-01.jpg)

SAML を使用した非排他的な 2 つの主要なシナリオの概要:

| シナリオ | Azure AD B2C のロール | 操作方法 |
| -------- | ----------------- | ------- |
| 私のアプリケーションで、SAML アサーションが認証を完了することが想定されている。 | **Azure AD B2C は、ID プロバイダー (IdP) として機能する**<br />Azure AD B2C は、アプリケーションに対する SAML IdP として機能します。 | この記事 |
| 私のユーザーは、ADFS、Salesforce、Shibboleth など、SAML 準拠の ID プロバイダーでシングル サイン オンを行う必要がある。  | **Azure AD B2C は、サービス プロバイダー (SP) として機能する**<br />Azure AD B2C は、SAML ID プロバイダーに接続するときにサービス プロバイダーとして機能します。 アプリケーションと SAML ID プロバイダーの間のフェデレーション プロキシです。  | <ul><li>[カスタム ポリシーを使用して SAML IdP としての ADFS によるサインインを設定する](identity-provider-adfs.md)</li><li>[カスタム ポリシーを使用して Salesforce SAML プロバイダーによるサインインを設定する](identity-provider-salesforce-saml.md)</li></ul> |

## <a name="prerequisites"></a>前提条件

* 「[Azure AD B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」の手順を完了します。 この記事で説明されているカスタム ポリシー スターター パックの *SocialAndLocalAccounts* カスタム ポリシーが必要です。
* Security Assertion Markup Language (SAML) プロトコルに関する基本的な知識。
* SAML サービス プロバイダー (SP) として構成された Web アプリ。 このチュートリアルでは、用意されている [SAML テスト アプリケーション][samltest]を使用できます。

## <a name="components-of-the-solution"></a>ソリューションのコンポーネント

このシナリオでは、次の 3 つの主要なコンポーネントが必要です。

* SAML 要求を送信し、Azure AD B2C から SAML アサーションを受信、デコード、応答する機能を持つ SAML **サービス プロバイダー**。 サービス プロバイダーは、証明書利用者アプリケーションとも呼ばれます。
* サービス プロバイダー向けに公開されている SAML **メタデータ エンドポイント**。
* [Azure AD B2C テナント](tutorial-create-tenant.md)

SAML サービス プロバイダーと関連のメタデータ エンドポイントがまだない場合は、テスト向けに用意されているこのサンプル SAML アプリケーションを使用できます。

[SAML テスト アプリケーション][samltest]

## <a name="1-set-up-certificates"></a>1.証明書を設定する

サービス プロバイダーと Azure AD B2C の間に信頼関係を構築するには、Web アプリの X509 証明書を用意する必要があります。

* **サービス プロバイダーの証明書**
  * 秘密キーが Web アプリに保存されている証明書。 この証明書は、Azure AD B2C に送信される SAML 要求に署名するためにサービス プロバイダーによって使用されます。 Azure AD B2C では、サービス プロバイダーのメタデータから公開キーを読み取り、署名が検証されます。
  * (省略可能) 秘密キーが Web アプリに保存されている証明書。 Azure AD B2C では、サービス プロバイダーのメタデータから公開キーを読み取り、SAML アサーションが暗号化されます。 次に、サービス プロバイダーにより、秘密キーを使用してアサーションの暗号化が解除されます。
* **Azure AD B2C の証明書**
  * Azure AD B2C に秘密キーがある証明書。 この証明書は、サービス プロバイダーに送信される SAML 応答に署名するために Azure AD B2C によって使用されます。 サービス プロバイダーでは、Azure AD B2C メタデータの公開キーを読み取り、SAML 応答の署名が検証されます。

公開証明機関によって発行された証明書、またはこのチュートリアルでは自己署名証明書を使用できます。

### <a name="11-create-a-self-signed-certificate"></a>1.1 自己署名証明書を作成する

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="12-upload-the-certificate"></a>1.2 証明書をアップロードする

次に、SAML アサーションと応答署名証明書を Azure AD B2C にアップロードします。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure AD B2C テナントを参照します。
1. **[ポリシー]** で、 **[Identity Experience Framework]** 、 **[ポリシー キー]** の順に選択します。
1. **[追加]** を選択してから、 **[オプション]**  >  **[アップロード]** を選択します。
1. **[名前]** に「*SamlIdpCert*」などを入力します。 プレフィックス *B2C_1A_* がキーの名前に自動的に追加されます。
1. ファイルのアップロード コントロールを使用して証明書をアップロードします。
1. 証明書のパスワードを入力します。
1. **［作成］** を選択します
1. キーが想定どおりに表示されることを確認します。 たとえば、*B2C_1A_SamlIdpCert* です。

## <a name="2-prepare-your-policy"></a>2.ドライブを準備する

### <a name="21-create-the-saml-token-issuer"></a>2.1 SAML トークン発行者を作成する

次に、[SAML トークン発行者](saml-issuer-technical-profile.md)と [SAML セッション プロバイダー](custom-policy-reference-sso.md#samlssosessionprovider)技術プロファイルを使用して、SAML トークンを発行するためのテナントの機能を追加します。

カスタム ポリシー スターター パックの `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** を開きます。

`<ClaimsProviders>` セクションを見つけて、次の XML スニペットを追加します。

`IssuerUri` メタデータの値を変更できます。 これは Azure AD B2C からの SAML 応答で返される発行者の URI です。 SAML アサーションの検証中に発行者の URI を受け入れるように証明書利用者アプリケーションを構成する必要があります。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3.SAML 証明書利用者ポリシーを追加する

テナントが SAML アサーションを発行できるようになったので、SAML 証明書利用者ポリシーを作成し、JWT ではなく SAML アサーションを発行するようにユーザー体験を変更する必要があります。

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 サインアップまたはサインイン ポリシーを作成する

1. スターター パックの作業ディレクトリに *SignUpOrSignin* ファイルのコピーを作成して、新しい名前で保存します。 たとえば、*SignUpOrSigninSAML.xml* です。 これが自分の証明書利用者ポリシー ファイルです。

1. 任意のエディターで *SignUpOrSigninSAML* ファイルを開きます。

1. 次に示すように、ポリシーの `PolicyId` と `PublicPolicyUri` を _B2C_1A_signup_signin_saml_ と `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` に変更します。

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. `<RelyingParty>` 要素の下に次の XML スニペットを追加します。 この XML により、_SignUpOrSignIn_ ユーザー体験のオーケストレーション ステップ番号 7 が上書きされます。 スターター パックの別のフォルダーから開始した場合、またはオーケストレーション ステップを追加または削除してユーザー体験をカスタマイズした場合は、番号 (`order` 要素内) が、ユーザー体験でトークン発行者ステップに対して指定されている番号と一致していることを確認します (たとえば、スターター パックの別のフォルダーで、`LocalAccounts` に対してステップ番号 4、`SocialAccounts` に対して 6、`SocialAndLocalAccountsWithMfa` に対して 9)。

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. `<RelyingParty>` 要素の `<TechnicalProfile>` 要素全体を次のテクニカル プロファイル XML に置き換えます。

    ```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Azure AD B2C テナントの名前で `tenant-name` を更新します。

証明書利用者ポリシー ファイルは最終的に次の XML コードのようになります。

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> 他の種類のユーザー フローを実装するとき (サインイン、パスワード リセット、プロファイル編集など)、このプロセスは基本的にこのセクションで説明するものと同じになります。 上記の手順 4 では、ユーザー体験の最後の手順を `JWTIssuer` から `Saml2AssertionIssuer` に変更します。 また、上記の手順 6 では、[証明書利用者] セクションで、**プロトコル** を `OpenIdConnect` から `SAML2` に変更します。

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 ポリシー メタデータをアップロードしてテストする

変更を保存し、新しいポリシー ファイルをアップロードします。 両方のポリシー (拡張機能ファイルと証明書利用者ファイル) をアップロードした後、Web ブラウザーを開き、ポリシー メタデータに移動します。

Azure AD B2C ポリシー IDP メタデータは、SAML ID プロバイダーの構成を公開するために SAML プロトコルで使用される情報です。 メタデータは、サインインとサインアウト、証明書、サインイン方法など、サービスの場所を定義します。 Azure AD B2C ポリシーのメタデータは、次の URL から入手できます。 `tenant-name` を Azure AD B2C テナントの名前に置き換え、`policy-name` にポリシーの名前 (ID) を付けます。たとえば、.../B2C_1A_signup_signin_saml/samlp/metadata のようにします。

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

これで、カスタム ポリシーと Azure AD B2C テナントの準備ができました。 次に、Azure AD B2C でアプリケーション登録を作成します。

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4.Azure AD B2C ディレクトリでアプリケーションをセットアップする

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4.1 Azure AD B2C にアプリケーションを登録する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*SAMLApp1* です。
1. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
1. **[リダイレクト URI]** で **[Web]** を選択し、「`https://localhost`」と入力します。 この値は、後でアプリケーションの登録のマニフェストで変更します。
1. **[登録]** を選択します。

### <a name="42-update-the-app-manifest"></a>4.2 アプリ マニフェストを更新する

SAML アプリの場合、アプリケーションの登録のマニフェストで構成する必要があるいくつかのプロパティがあります。

1. [Azure portal](https://portal.azure.com) で、前のセクションで作成したアプリケーションの登録に移動します。
1. **[管理]** で、 **[マニフェスト]** を選択してマニフェスト エディターを開きます。 次のセクションで、いくつかのプロパティを変更します。

#### <a name="identifieruris"></a>identifierUris

`identifierUris` は、Azure AD B2C テナント内の Web アプリを一意に識別するユーザー定義 URI が含まれる文字列コレクションです。 URI は、SAML 要求の `Issuer` の名前と一致している必要があります。 ユーザー定義 URI は、通常、サービス プロバイダーのメタデータ `entityID` と同じ値です。

#### <a name="samlmetadataurl"></a>samlMetadataUrl

このプロパティは、サービス プロバイダーの公開されているメタデータ URL を表します。 このメタデータ URL により、匿名でアクセスできる任意のエンドポイント (BLOB ストレージなど) にアップロードされたメタデータ ファイルを指すことができます。

メタデータは、サービス プロバイダーなど、SAML パーティーの構成を公開するために SAML プロトコルで使用される情報です。 メタデータで、サインインとサインアウト、証明書、サインイン方法などのサービスの場所を定義します。 Azure AD B2C は、サービス プロバイダーのメタデータを読み取り、それに応じて動作します。 メタデータは必須ではありません。 アプリ マニフェストで直接、応答 URI やログアウト URI などの一部の属性を指定することもできます。

SAML メタデータ URL とアプリケーションの登録のマニフェストの "*両方*" に指定されたプロパティがある場合は、**マージ** されます。 メタデータ URL に指定されたプロパティが最初に処理され、優先されます。

SAML テスト アプリケーションを使用するこのチュートリアルでは、`samlMetadataUrl` に次の値を使用します。

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (省略可能)

メタデータ URI を設定しない場合は、応答 URL を明示的に指定できます。 この省略可能なプロパティは `AssertionConsumerServiceUrl` (サービス プロバイダーのメタデータ内の `SingleSignOnService` URL) を表し、`BindingType` は `HTTP POST` であると見なされます。

サービス プロバイダーのメタデータを使用せずにアプリケーション マニフェストで応答 URL とログアウト URL を構成することを選択した場合、Azure AD B2C では、SAML 要求署名は検証されず、SAML 応答も暗号化されません。

このチュートリアルでは、SAML テスト アプリケーションを使用して、`replyUrlsWithType` の `url` プロパティを次の JSON スニペット内で示される値に設定します。

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (省略可能)

この省略可能なプロパティは `Logout` URL (証明書利用者のメタデータ内の `SingleLogoutService` URL) を表し、この `BindingType` は `Http-Redirect` であると見なされます。

SAML テスト アプリケーションを使用するこのチュートリアルでは、`logoutUrl` を `https://samltestapp2.azurewebsites.net/logout` に設定したままにします。

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5.アプリケーション コードを更新する

最後の手順では、SAML 証明書利用者アプリケーションで Azure AD B2C を SAML IdP として有効にします。 アプリケーションはそれぞれ異なり、そのための手順もさまざまです。 詳細については、アプリのドキュメントを参照してください。

メタデータは、ご利用のサービス プロバイダーで "静的メタデータ" または "動的メタデータ" として構成されている可能性があります。 静的モードでは、メタデータのすべてまたは一部を Azure AD B2C ポリシー メタデータからコピーします。 動的モードでは、URL をメタデータに設定し、アプリケーションでメタデータを動的に読み取ります。

通常、次の一部またはすべてが必要です。

* **メタデータ**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **発行者**:SAML 要求の `issuer` 値は、アプリケーション登録マニフェストの `identifierUris` 要素で構成された URI のいずれかと一致する必要があります。 SAML 要求の `issuer` の名前が `identifierUris` 要素に存在しない場合は、[アプリケーション登録マニフェストに追加します](#identifieruris)。 たとえば、`https://contoso.onmicrosoft.com/app-name` のようにします。 
* **ログイン Url/SAML エンドポイント/SAML Url**:Azure AD B2C SAML ポリシー メタデータ ファイルの `<SingleSignOnService>` XML 要素の値を確認します
* **Certificate**: これは *B2C_1A_SamlIdpCert* ですが、秘密キーはありません。 証明書の公開キーを取得するには、次のようにします。

    1. 上で指定したメタデータ URL にアクセスします。
    1. `<X509Certificate>` 要素の値をコピーします。
    1. テキスト ファイルに貼り付けます。
    1. テキスト ファイルを *.cer* ファイルとして保存します。

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 SAML テスト アプリでテストする (省略可能)

[SAML テスト アプリケーション][samltest]を使用してこのチュートリアルを完了するには、次のようにします。

* テナント名を更新します
* ポリシー名を更新します (例 *B2C_1A_signup_signin_saml*)
* 発行者 URI を指定します。 アプリケーション登録マニフェストの `identifierUris` 要素にある URI のいずれか (たとえば `https://contoso.onmicrosoft.com/app-name`) を使用します。

**[ログイン]** を選択すると、ユーザーのサインイン画面が表示されます。 サインインすると、サンプル アプリケーションに SAML アサーションが発行されます。

## <a name="enable-encrypted-assertions-optional"></a>暗号化されたアサーションを有効にする (省略可能)

サービス プロバイダーに送り返された SAML アサーションを暗号化するために、Azure AD B2C ではサービス プロバイダーの公開キー証明書が使用されます。 公開キーは、上記の「[samlMetadataUrl](#samlmetadataurl)」で説明した SAML メタデータに、'Encryption' の用途の KeyDescriptor として含まれている必要があります。

次の XML コードは、"use" を "Encryption" に設定した SAML メタデータの KeyDescriptor の例です。

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Azure AD B2C が暗号化されたアサーションを送信できるようにするには、次に示すように、[証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)で **WantsEncryptedAssertion** メタデータ項目を `true` に設定します。 SAML アサーションの暗号化に使用されるアルゴリズムも構成できます。 詳細については、[証明書利用者の技術プロファイルのメタデータ](relyingparty.md#metadata)を参照してください。 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>ID プロバイダー開始フローを有効にする (省略可能)

ID プロバイダー開始フローでは、サインイン プロセスは ID プロバイダー (Azure AD B2C) によって開始され、このプロバイダーによって、要請されていない SAML 応答がサービス プロバイダー (証明書利用者アプリケーション) に送信されます。 現在、開始側の ID プロバイダーが外部 ID プロバイダー (たとえば [AD-FS](identity-provider-adfs.md) や [Salesforce](identity-provider-salesforce-saml.md)) であるシナリオはサポートされていません。

ID プロバイダー (Azure AD B2C) 開始フローを有効にするには、[証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)で、**IdpInitiatedProfileEnabled** メタデータ項目を `true` に設定します。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

ID プロバイダー開始フローを通じてユーザーをサインインまたはサインアップするには、次の URL を使用します。

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login?EntityId=app-identifier-uri 
```

次の値を置き換えます。

* **tenant-name** を実際のテナント名に置き換えます
* **policy-name** を SAML 証明書利用者のポリシー名に置き換えます
* **app-identifier-uri** をメタデータ ファイルの `identifierUris` (`https://contoso.onmicrosoft.com/app-name` など) に
## <a name="sample-policy"></a>サンプル ポリシー

SAML テスト アプリでテストに使用できる完全なサンプル ポリシーが用意されています。

1. [SAML SP によって開始されるログインのサンプル ポリシー](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)をダウンロードします
1. `TenantId` をテナント名に一致するように更新します (例 *contoso.b2clogin.com*)
1. *B2C_1A_signup_signin_saml* のポリシー名をそのままにします

## <a name="supported-and-unsupported-saml-modalities"></a>サポートされている SAML モダリティとサポートされていない SAML モダリティ

次の SAML 証明書利用者 (RP) シナリオは、独自のメタデータ エンドポイント経由でサポートされます。

* アプリケーションまたはサービス プリンシパル オブジェクト内のログアウト URL に対する複数のログアウト URL または POST バインド。
* アプリケーションまたはサービス プリンシパル オブジェクト内に RP 要求を検証するための署名キーを指定します。
* アプリケーションまたはサービス プリンシパル オブジェクト内にトークン暗号化キーを指定します。
* ID プロバイダーがサインオンを開始しました。この場合、ID プロバイダーは Azure AD B2C です。

## <a name="saml-token"></a>SAML トークン

SAML トークンは、サインインが成功した後に Azure AD B2C によって発行されるセキュリティ トークンです。 これには、ユーザー、トークンの対象となるサービス プロバイダー、署名、有効期間に関する情報が含まれます。 次の表は、Azure AD B2C によって発行される SAML トークンで予期できる要求とプロパティの一覧です。

|要素  |プロパティ  |Notes  |
|---------|---------|---------|
|`<Response>`| `ID` | 応答の自動生成一意識別子。 | 
|`<Response>`| `InResponseTo` | このメッセージの応答先となる SAML 要求の ID。 | 
|`<Response>` | `IssueInstant` | 応答の発行の瞬間。 時刻値は UTC でエンコードされます。    トークンの有効期間の設定を変更するには、SAML トークン発行者の技術プロファイルの `TokenNotBeforeSkewInSeconds` [メタデータ](saml-issuer-technical-profile.md#metadata)を設定します。 | 
|`<Response>` | `Destination`| この応答の送信先のアドレスを示す URI 参照。 値は、SAML 要求 `AssertionConsumerServiceURL` と同じです。 | 
|`<Response>` `<Issuer>` | |トークン発行者を識別します。 これは、SAML トークン発行者の `IssuerUri` [メタデータ](saml-issuer-technical-profile.md#metadata)によって定義される任意の URI です     |
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     |         |トークンによって情報がアサートされるプリンシパルです (ユーザー オブジェクト ID など)。 この値は変更不可で、再割り当ても再利用もできません。 そのため、この値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。 既定では、サブジェクト要求には、ディレクトリ内のユーザーのオブジェクト ID が設定されます。|
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     | `Format` | 文字列ベースの識別子情報の分類を表す URI 参照。 既定では、このプロパティは省略されます。 証明書利用者 [SubjectNamingInfo](relyingparty.md#subjectnaminginfo) を設定して、`urn:oasis:names:tc:SAML:2.0:nameid-format:transient` などの `NameID` 形式を指定できます。 |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` |`NotBefore` |トークンが有効になる時刻。 時刻値は UTC でエンコードされます。 アプリケーションでは、この要求を使用してトークンの有効期間の有効性を確認する必要があります。 トークンの有効期間の設定を変更するには、SAML トークン発行の技術プロファイルの `TokenNotBeforeSkewInSeconds` [メタデータ](saml-issuer-technical-profile.md#metadata)を設定します。 |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` | `NotOnOrAfter` | トークンが無効になる時刻。 アプリケーションでは、この要求を使用してトークンの有効期間の有効性を確認する必要があります。 既定値は `NotBefore` の 5 分後で、SAML トークン発行の技術プロファイルの `TokenLifeTimeInSeconds` [メタデータ](saml-issuer-technical-profile.md#metadata)を追加することによって更新できます。|
|`<Response>` `<Assertion>` `<Conditions>` `<AudienceRestriction>` `<Audience>` | |対象ユーザーを識別する URI 参照。 トークンの受信者を示します。 値は、SAML 要求 `AssertionConsumerServiceURL` と同じです。|
|`<Response>` `<Assertion>` `<AttributeStatement>` `<Attribute>` の コレクション | | [証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)出力要求で構成されているアサーション コレクション (要求)。 出力要求の `PartnerClaimType` を設定することにより、アサーションの名前を構成できます。 |

## <a name="next-steps"></a>次のステップ

- [SAML プロトコルの詳細については、OASIS の Web サイト](https://www.oasis-open.org/)を参照してください。
- [Azure AD B2C GitHub コミュニティ リポジトリ](https://github.com/azure-ad-b2c/saml-sp-tester)から SAML テスト Web アプリを取得します。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
