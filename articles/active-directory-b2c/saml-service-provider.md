---
title: Azure Active Directory B2C を SAML IdP としてアプリケーションに構成する
title-suffix: Azure Active Directory B2C
description: アプリケーション (サービス プロバイダー) に SAML プロトコル アサーションを提供するように Azure Active Directory B2C を構成する方法。 Azure AD B2C は、SAML アプリケーションに対して単一の ID プロバイダー (IdP) として機能します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107077"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>SAML アプリケーションを Azure AD B2C に登録する

この記事では、Security Assertion Markup Language (SAML) アプリケーション (サービス プロバイダー) を Azure Active Directory B2C (Azure AD B2C) に接続して認証を行う方法について説明します。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>概要

顧客 ID およびアクセス管理ソリューションとして Azure AD B2C を使用する組織では、SAML プロトコルを使用して認証するアプリケーションと統合することが必要になる場合があります。 次の図は、Azure AD B2C が "*ID プロバイダー*" (IdP) として機能し、SAML ベースのアプリケーションでシングルサインオン (SSO) を実現する方法を示しています。

![左側に ID プロバイダーとして機能する B2C、右側にサービスプロバイダーとして機能する B2C を示す図](media/saml-service-provider/saml-service-provider-integration.png)

1. アプリケーションで、Azure AD B2C の SAML ログイン エンドポイントに送信される SAML AuthN 要求が作成されます。
2. ユーザーは、Azure AD B2C ローカル アカウント、または他のフェデレーション ID プロバイダー (構成されている場合) を使用して認証を行うことができます。
3. ユーザーがフェデレーション ID プロバイダーを使用してサインインすると、トークン応答は Azure AD B2C に送信されます。
4. Azure AD B2C により、SAML アサーションが生成され、アプリケーションに送信されます。

## <a name="prerequisites"></a>前提条件

* 「[Azure AD B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」の手順を完了します。 この記事で説明されているカスタム ポリシー スターター パックの *SocialAndLocalAccounts* カスタム ポリシーが必要です。
* SAML プロトコルに関する基本的な理解、およびアプリケーションの SAML 実装に関する知識。
* SAML アプリケーションとして構成された Web アプリケーション。 このチュートリアルでは、用意されている [SAML テスト アプリケーション][samltest]を使用できます。

## <a name="components"></a>コンポーネント

このシナリオでは、次の 3 つの主要なコンポーネントが必要です。

* SAML AuthN 要求を送信し、Azure AD B2C から SAML 応答を受信、デコード、検証する機能を備えた SAML **アプリケーション**。 SAML アプリケーションは、証明書利用者アプリケーションまたはサービス プロバイダーとも呼ばれます。
* SAML アプリケーションの一般公開されている SAML **メタデータ エンドポイント** または XML ドキュメント。
* [Azure AD B2C テナント](tutorial-create-tenant.md)

SAML アプリケーションと関連のメタデータ エンドポイントがまだない場合は、テスト向けに用意されているこのサンプル SAML アプリケーションを使用できます。

[SAML テスト アプリケーション][samltest]

## <a name="set-up-certificates"></a>証明書を設定する

アプリケーションと Azure AD B2C の間で信頼関係を構築するには、両方のサービスが相互の署名を作成および検証できる必要があります。 Azure AD B2C とアプリケーションで X509 証明書を構成します。

**アプリケーション証明書**

| 使用法 | 必須 | 説明 |
| --------- | -------- | ----------- |
| SAML 要求の署名  | いいえ | Azure AD B2C に送信される SAML 要求に署名するためにアプリケーションで使用される、Web アプリに保存された秘密キーを持つ証明書。 Web アプリでは、SAML メタデータ エンドポイントを使用して公開キーを公開する必要があります。 Azure AD B2C では、アプリケーション メタデータから公開キーを使用して、SAML 要求の署名を検証します。|
| SAML アサーションの暗号化  | いいえ | 秘密キーが Web アプリに保存されている証明書。 Web アプリでは、SAML メタデータ エンドポイントを使用して公開キーを公開する必要があります。 Azure AD B2C では、公開キーを使用してアプリケーションへのアサーションを暗号化できます。 アプリケーションで、秘密キーを使用してアサーションの暗号化を解除します。|

**Azure AD B2C の証明書**

| 使用法 | 必須 | 説明 |
| --------- | -------- | ----------- |
| SAML 応答の署名 | はい | 秘密キーが Azure AD B2C に保存されている証明書。 この証明書は、アプリケーションに送信される SAML 応答に署名するために Azure AD B2C によって使用されます。 アプリケーションで、Azure AD B2C メタデータの公開キーを読み取り、SAML 応答の署名を検証します。 |

運用環境では、パブリック証明機関によって発行された証明書を使用することをお勧めします。 ただし、自己署名証明書を使用してこの手順を実行することもできます。

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>SAML 応答の署名用の自己署名証明書を準備する

アプリケーションが Azure AD B2C からのアサーションを信頼できるように、SAML 応答の署名証明書を作成する必要があります。

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>SAML アプリケーションと接続するためのポリシーを有効にする

SAML アプリケーションに接続するには、Azure AD B2C が SAML 応答を作成できる必要があります。

カスタム ポリシー スターター パックの `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** を開きます。

`<ClaimsProviders>` セクションを見つけて、次の XML スニペットを追加して、SAML 応答ジェネレーターを実装します。

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
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
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

#### <a name="configure-the-issueruri-of-the-saml-response"></a>SAML 応答の IssuerUri を構成する

SAML トークン発行者の技術プロファイルで、`IssuerUri` メタデータ項目の値を変更できます。 この変更は、Azure AD B2C からの SAML 応答で返される `issuerUri` 属性に反映されます。 SAML 応答の検証時に同じ `issuerUri` を受け入れるようにアプリケーションを構成する必要があります。

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
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>Azure AD B2C IdP SAML メタデータに署名する (省略可能)

アプリケーションで必要な場合は、Azure AD B2C に対して、SAML IdP メタデータ ドキュメントに署名するように指示できます。 このためには、「[SAML 応答の署名用の自己署名証明書を準備する](#prepare-a-self-signed-certificate-for-saml-response-signing)」で示すように、SAML IdP メタデータ署名用のポリシー キーを生成してアップロードします。 次に、SAML トークン発行者の技術プロファイルで `MetadataSigning` メタデータ項目を構成します。 `StorageReferenceId` では、ポリシー キー名を参照する必要があります。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>Azure AD B2C IdP SAML 応答要素に署名する (省略可能)

SAML メッセージに署名するために使用される証明書を指定することができます。 メッセージは、アプリケーションに送信される SAML 応答内の `<samlp:Response>` 要素です。

証明書を指定するには、「[SAML 応答の署名用の自己署名証明書を準備する](#prepare-a-self-signed-certificate-for-saml-response-signing)」で示すように、ポリシー キーを生成してアップロードします。 次に、SAML トークン発行者の技術プロファイルで `SamlMessageSigning` メタデータ項目を構成します。 `StorageReferenceId` では、ポリシー キー名を参照する必要があります。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>SAML 応答を発行するようにポリシーを構成する

ポリシーで SAML 応答を作成できるようになったので、アプリケーションに対する既定の JWT 応答ではなく SAML 応答を発行するようにポリシーを構成する必要があります。

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>SAML 用に構成されたサインアップまたはサインイン ポリシーを作成する

1. スターター パックの作業ディレクトリに *SignUpOrSignin* ファイルのコピーを作成して、新しい名前で保存します。 たとえば、*SignUpOrSigninSAML.xml* です。 このファイルは、証明書利用者ポリシーファイルであり、既定で JWT 応答を発行するように構成されています。

1. 任意のエディターで *SignUpOrSigninSAML* ファイルを開きます。

1. 次に示すように、ポリシーの `PolicyId` と `PublicPolicyUri` を _B2C_1A_signup_signin_saml_ と `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` に変更します。

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. ユーザー体験の終了時に、Azure AD B2C には、`SendClaims` ステップが含まれます。 このステップでは、トークン発行者の技術プロファイルが参照されます。 既定の JWT 応答ではなく SAML 応答を発行するには、新しい SAML トークン発行者の技術プロファイル `Saml2AssertionIssuer` を参照するように、`SendClaims` ステップを変更します。

`<RelyingParty>` 要素の直前に次の XML スニペットを追加します。 この XML により、_SignUpOrSignIn_ ユーザー体験のオーケストレーション ステップ番号 7 が上書きされます。 スターター パックの別のフォルダーから開始した場合、またはオーケストレーション ステップを追加または削除してユーザー体験をカスタマイズした場合は、`order` 要素の番号がトークン発行者ステップのユーザー体験で指定された番号に対応していることを確認してください。 たとえば、他のスターター パック フォルダーでは、対応するステップ番号は、`LocalAccounts` の場合は 4、`SocialAccounts` の場合は 6、`SocialAndLocalAccountsWithMfa` の場合は 9 です。

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

証明書利用者要素によって、アプリケーションで使用されるプロトコルが決まります。 既定では、 `OpenId`です。 `Protocol` 要素を `SAML` に変更する必要があります。 出力要求により、SAML アサーションに対する要求のマッピングが作成されます。

`<RelyingParty>` 要素の `<TechnicalProfile>` 要素全体を次のテクニカル プロファイル XML に置き換えます。 Azure AD B2C テナントの名前で `tenant-name` を更新します。

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
> この同じプロセスに従って、他の種類のユーザー フロー (サインイン、パスワード リセット、プロファイル編集フローなど) を実装できます。

### <a name="upload-your-policy"></a>ポリシーをアップロードする

変更を保存して、新しい **TrustFrameworkExtensions.xml** と **SignUpOrSigninSAML.xml** の各ポリシー ファイルを Azure portal にアップロードします。

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Azure AD B2C IdP SAML メタデータをテストする

ポリシー ファイルをアップロードすると、Azure AD B2C では、構成情報を使用して、アプリケーションで使用される ID プロバイダーの SAML メタデータ ドキュメントが生成されます。 SAML メタデータ ドキュメントには、サインインやログアウトの方法、証明書などのサービスの場所が含まれます。

Azure AD B2C ポリシー メタデータは、次の URL から入手できます。

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

`<tenant-name>` をご利用の Azure AD B2C テナントの名前に、`<policy-name>` をポリシーの名前 (ID) に置き換えます。たとえば、次のようになります。

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>SAML アプリケーションを Azure AD B2C に登録する

Azure AD B2C にアプリケーションを信頼させるには、アプリケーションのメタデータ エンドポイントなどの構成情報を含む Azure AD B2C アプリケーションの登録を作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*SAMLApp1* です。
1. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
1. **[リダイレクト URI]** で **[Web]** を選択し、「`https://localhost`」と入力します。 後で、この値をアプリケーションの登録のマニフェストで変更します。
1. **[登録]** を選択します。

### <a name="configure-your-application-in-azure-ad-b2c"></a>Azure AD B2C でアプリケーションを構成する

SAML アプリの場合、アプリケーションの登録のマニフェストでいくつかのプロパティを構成する必要があります。

1. [Azure portal](https://portal.azure.com) で、前のセクションで作成したアプリケーションの登録に移動します。
1. **[管理]** で **[マニフェスト]** を選択して、マニフェスト エディターを開き、次のセクションで説明するプロパティを変更します。

#### <a name="add-the-identifier"></a>識別子を追加する

SAML アプリケーションで Azure AD B2C に対して要求が行われると、SAML AuthN 要求に `Issuer` 属性が含まれます。これは、通常、アプリケーションのメタデータ `entityID` と同じ値です。 Azure AD B2C では、この値を使用して、ディレクトリ内でアプリケーションの登録を検索し、構成を読み取ります。 この検索を成功させるには、アプリケーションの登録内の `identifierUri` に、`Issuer` 属性と一致する値を設定する必要があります。

登録マニフェストで、`identifierURIs` パラメーターを見つけて、適切な値を追加します。 この値は、アプリケーションによって EntityId の SAML AuthN 要求内で構成された値、およびアプリケーションのメタデータ内の `entityID` の値と同じ値になります。

次の例は、SAML メタデータ内の `entityID` を示します。

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris` プロパティは、ドメイン `tenant-name.onmicrosoft.com` 上の URL のみを受け入れます。

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>アプリケーションのメタデータを Azure AD B2C と共有する

アプリケーションの登録がその `identifierUri` によって読み込まれると、Azure AD B2C では、アプリケーションのメタデータを使用して、SAML AuthN 要求が検証され、応答方法が決定されます。

アプリケーションで、パブリックにアクセスできるメタデータ エンドポイントを公開することをお勧めします。

SAML メタデータ URL とアプリケーションの登録のマニフェストの "*両方*" に指定されたプロパティがある場合は、"*統合*" されます。 メタデータ URL に指定されたプロパティが最初に処理され、優先されます。

SAML テスト アプリケーションを例として使用すると、アプリケーション マニフェスト内の `samlMetadataUrl` に次の値を使用します。

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>アサーション コンシューマーの URL をオーバーライドまたは設定する (省略可能)

Azure AD B2C が SAML 応答を送信する応答 URL を構成することができます。 応答 URL は、アプリケーション マニフェストで構成できます。 この構成は、アプリケーションで、パブリックにアクセスできるメタデータ エンドポイントを公開しない場合に役立ちます。

SAML アプリケーションの応答 URL は、アプリケーションが SAML 応答の受信を想定するエンドポイントです。 次に示すように、アプリケーションでは、通常、メタデータ ドキュメント内の `AssertionConsumerServiceUrl` 属性で、この URL を指定します。

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

`AssertionConsumerServiceUrl` 属性で指定されたメタデータをオーバーライドする場合、または URL がメタデータ ドキュメント内にない場合、マニフェスト内の `replyUrlsWithType` プロパティで URL を構成できます。 `BindingType` は、`HTTP POST` に設定されます。

SAML テスト アプリケーションを例として使用すると、`replyUrlsWithType` の `url` プロパティを、次の JSON スニペットに示す値に設定します。

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>ログアウト URL をオーバーライドまたは設定する (省略可能)

ログアウト要求後に Azure AD B2C によってユーザーに送信されるログアウト URL を構成することができます。 応答 URL は、アプリケーション マニフェストで構成できます。

`SingleLogoutService` 属性で指定されたメタデータをオーバーライドする場合、または URL がメタデータ ドキュメント内にない場合、マニフェスト内の `Logout` プロパティで URL を構成できます。 `BindingType` は、`Http-Redirect` に設定されます。

次に示すように、アプリケーションでは、通常、メタデータ ドキュメント内の `AssertionConsumerServiceUrl` 属性で、この URL を指定します。

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

SAML テスト アプリケーションを例として使用すると、`logoutUrl` を `https://samltestapp2.azurewebsites.net/logout` に設定したままにします。

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> `samlMetadataUrl` プロパティを使用してアプリケーションのメタデータ エンドポイントを設定せずにアプリケーション マニフェストで応答 URL とログアウト URL を構成することを選択した場合、Azure AD B2C では、SAML 要求の署名は検証されず、SAML 応答も暗号化されません。

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>SAML アプリケーションで Azure AD B2C を SAML IdP として構成する

最後のステップとして、SAML アプリケーションで Azure AD B2C を SAML IdP として有効にします。 アプリケーションはそれぞれ異なり、ステップもさまざまです。 詳細については、アプリのドキュメントを参照してください。

アプリケーションでは、メタデータが "*静的メタデータ*" または "*動的メタデータ*" として構成されている可能性があります。 静的モードでは、メタデータのすべてまたは一部を Azure AD B2C ポリシー メタデータからコピーします。 動的モードでは、メタデータへの URL を指定するか、アプリケーションでメタデータを動的に読み込むことができるようにします。

通常、次の一部またはすべてが必要です。

* **メタデータ**: 形式 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` を使用します。
* **発行者**:SAML 要求の `issuer` 値は、アプリケーション登録マニフェストの `identifierUris` 要素で構成された URI のいずれかと一致する必要があります。 SAML 要求の `issuer` の名前が `identifierUris` 要素に存在しない場合は、[アプリケーション登録マニフェストに追加します](#add-the-identifier)。 たとえば、`https://contoso.onmicrosoft.com/app-name` のようにします。 
* **ログイン URL、SAML エンドポイント、または SAML URL**: Azure AD B2C SAML ポリシー メタデータ ファイル内の `<SingleSignOnService>` XML 要素の値を確認します。
* **証明書**: この証明書は *B2C_1A_SamlIdpCert* ですが、秘密キーは含まれません。 証明書の公開キーを取得するには、次のようにします。

    1. 上で指定したメタデータ URL にアクセスします。
    1. `<X509Certificate>` 要素の値をコピーします。
    1. テキスト ファイルに貼り付けます。
    1. テキスト ファイルを *.cer* ファイルとして保存します。

### <a name="test-with-the-saml-test-app"></a>SAML テスト アプリでテストする

[SAML テスト アプリケーション][samltest]を使用して、構成をテストすることができます。

* テナント名を更新します。
* ポリシー名を更新します (例 *B2C_1A_signup_signin_saml*)。
* 発行者 URI を指定します。 アプリケーション登録マニフェストの `identifierUris` 要素にある URI のいずれか (たとえば `https://contoso.onmicrosoft.com/app-name`) を使用します。

**[ログイン]** を選択すると、ユーザーのサインイン画面が表示されます。 サインインすると、サンプル アプリケーションに SAML 応答が発行されます。

## <a name="supported-and-unsupported-saml-modalities"></a>サポートされている SAML モダリティとサポートされていない SAML モダリティ

次の SAML アプリケーション シナリオは、独自のメタデータ エンドポイントを使用してサポートされます。

* アプリケーションまたはサービス プリンシパル オブジェクト内のログアウト URL に対する複数のログアウト URL または POST バインド。
* アプリケーションまたはサービス プリンシパル オブジェクト内に、証明書利用者 (RP) 要求を検証するための署名キーを指定する。
* アプリケーションまたはサービス プリンシパル オブジェクト内にトークン暗号化キーを指定する。
* ID プロバイダー提供のサインオン。この場合、ID プロバイダーは Azure AD B2C です。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C GitHub コミュニティ リポジトリ](https://github.com/azure-ad-b2c/saml-sp-tester)から SAML テスト Web アプリを取得します。
- [Azure AD B2C に SAML アプリケーションを登録するためのオプション](saml-service-provider-options.md)を参照してください

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
