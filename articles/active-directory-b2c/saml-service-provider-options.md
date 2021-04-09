---
title: SAML サービス プロバイダーのオプションを構成する
title-suffix: Azure Active Directory B2C
description: Azure Active Directory B2C SAML サービス プロバイダーのオプションを構成する方法
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 09cfdd026105a34db976118f38b011e2c4578a24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470777"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C に SAML アプリケーションを登録するためのオプション

この記事では、Azure Active Directory (Azure AD B2C) を SAML アプリケーションに接続するときに使用できる構成オプションについて説明します。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>暗号化された SAML アサーション

SAML アサーションが暗号化形式であることをアプリケーションが想定する場合は、Azure AD B2C ポリシーで暗号化が有効になっていることを確認する必要があります。

Azure AD B2C は、サービス プロバイダーの公開キー証明書を使用して SAML アサーションを暗号化します。 公開キーは、次の例に示すように、KeyDescriptor 'use' を 'Encryption' に設定して、SAML アプリケーションのメタデータ エンドポイントにあることが必要です。

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Azure AD B2C が暗号化されたアサーションを送信できるようにするには、次に示すように、[証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)で **WantsEncryptedAssertion** メタデータ項目を `true` に設定します。 SAML アサーションの暗号化に使用されるアルゴリズムも構成できます。

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

### <a name="encryption-method"></a>暗号化方法

SAML アサーション データの暗号化に使用する暗号化方法を構成するには、証明書利用者内で `DataEncryptionMethod` メタデータ キーを設定します。 指定可能な値は `Aes256` (既定値)、`Aes192`、`Sha512`、または `Aes128` です。 このメタデータにより、SAML 応答内の `<EncryptedData>` 要素の値が制御されます。

SAML アサーション データの暗号化に使用されたキーのコピーを暗号化するために使用される暗号化方法を構成するには、証明書利用者内で `KeyEncryptionMethod` メタデータ キーを設定します。 使用可能な値は、`Rsa15` (既定) - RSA 公開鍵暗号化標準 (PKCS) バージョン 1.5 アルゴリズム、および `RsaOaep` - RSA OAEP (Optimal Asymmetric Encryption Padding) 暗号化アルゴリズムです。  このメタデータにより、SAML 応答内の `<EncryptedKey>` 要素の値が制御されます。

次の例は、SAML アサーションの `EncryptedAssertion` セクションを示しています。 暗号化データ メソッドは `Aes128` で、暗号化キー メソッドは `Rsa15` です。

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

暗号化されたアサーションの形式は変更できます。 暗号化形式を構成するには、証明書利用者内で `UseDetachedKeys` メタデータ キーを設定します。 指定できる値: `true` または `false` (既定値)。 値が `true` に設定されている場合、デタッチされたキーは `EncryptedData` ではなく、`EncrytedAssertion` 子として暗号化されたアサーションを追加します。

暗号化の方法と形式を構成し、[証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)内でメタデータ キーを使用します。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>ID プロバイダー開始フロー

アプリケーションが、SAML AuthN 要求を最初に ID プロバイダーに送信することなく SAML アサーションを受け取ることを想定している場合は、ID プロバイダー開始フロー用に Azure AD B2C を構成する必要があります。

ID プロバイダー開始フローでは、サインイン プロセスは ID プロバイダー (Azure AD B2C) によって開始され、このプロバイダーによって、要請されていない SAML 応答がサービス プロバイダー (証明書利用者アプリケーション) に送信されます。

現在、開始側の ID プロバイダーが、Azure AD B2C とフェデレーションされた外部 ID プロバイダー (たとえば [AD-FS](identity-provider-adfs.md) や [Salesforce](identity-provider-salesforce-saml.md)) であるシナリオはサポートされていません。 これは、Azure AD B2C ローカル アカウント認証でのみサポートされています。

ID プロバイダー開始フローを有効にするには、[証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)で、**IdpInitiatedProfileEnabled** メタデータ項目を `true` に設定します。

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
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

次の値を置き換えます。

* **tenant-name** を実際のテナント名に置き換えます
* **policy-name** を SAML 証明書利用者のポリシー名に置き換えます
* **app-identifier-uri** をメタデータ ファイルの `identifierUris` (`https://contoso.onmicrosoft.com/app-name` など) に

### <a name="sample-policy"></a>サンプル ポリシー

SAML テスト アプリでテストに使用できる完全なサンプル ポリシーが用意されています。

1. [SAML SP によって開始されるログインのサンプル ポリシー](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)をダウンロードします。
1. `TenantId` をテナント名に一致するように更新します (例: *contoso.b2clogin.com*)。
1. *B2C_1A_signup_signin_saml* のポリシー名をそのままにします。

## <a name="saml-response-signature-algorithm"></a>SAML 応答署名アルゴリズム

SAML アサーションの署名に使用する署名アルゴリズムを構成できます。 指定できる値は、`Sha256`、`Sha384`、`Sha512`、および `Sha1` です。 技術プロファイルとアプリケーションで同じ署名アルゴリズムが使用されていることを確認します。 証明書でサポートされているアルゴリズムのみを使用してください。

証明書利用者の `XmlSignatureAlgorithm` メタデータ要素内のメタデータ キーを使用して署名アルゴリズムを構成します。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>SAML 応答の有効期間

SAML 応答を有効にする期間を構成できます。 SAML トークン発行者技術プロファイル内の `TokenLifeTimeInSeconds` メタデータ項目を使用して有効期間を設定します。 この値は、トークンの発行時に計算された `NotBefore` タイムスタンプからの経過時間を秒数で示します。 有効期間の既定値は 300 秒 (5 分) です。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>SAML 応答で許容される時間のずれ

SAML 応答の `NotBefore` タイムスタンプに適用される時間のずれを構成できます。 この構成により、2 つのプラットフォーム間の時間が同期していないときにこの時間のずれがあっても、SAML アサーションは有効と見なされます。

時間のずれは、SAML トークン発行者技術プロファイル内の `TokenNotBeforeSkewInSeconds` メタデータ項目を使用して設定します。 時間のずれは秒単位で指定します。既定値は 0 です。 最大値は 3600 (1 時間) です。

たとえば、`TokenNotBeforeSkewInSeconds` が `120` 秒に設定されている場合、次のようになります。

- トークンは 13:05:10 UTC に発行されます
- トークンは 13:03:10 UTC から有効です

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>日付と時刻からミリ秒を削除する

SAML 応答内の datetime の値からミリ秒を削除するかどうかを指定できます (これには、IssueInstant、NotBefore、NotOnOrAfter、および AuthnInstant が含まれます)。 ミリ秒を削除するには、証明書利用者内で `RemoveMillisecondsFromDateTime
` メタデータ キーを設定します。 指定できる値: `false` (既定値) または `true`。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>Azure AD B2C の発行者 ID

異なる `entityID` 値に依存する複数の SAML アプリケーションがある場合は、証明書利用者ファイルの `issueruri` 値をオーバーライドできます。 発行者の URI をオーバーライドするには、基本ファイルの "Saml2AssertionIssuer" ID を使用して技術プロファイルをコピーし、`issueruri` 値をオーバーライドします。

> [!TIP]
> 基本ファイルから `<ClaimsProviders>` セクションをコピーし、これらの要素を要求プロバイダー `<DisplayName>Token Issuer</DisplayName>`、`<TechnicalProfile Id="Saml2AssertionIssuer">`、および `<DisplayName>Token Issuer</DisplayName>` 内に保存します。
 
例:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>セッションの管理

`UseTechnicalProfileForSessionManagement` 要素と [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) を使用して、Azure AD B2C と SAML 証明書利用者アプリケーション間のセッションを管理できます。

## <a name="force-users-to-re-authenticate"></a>ユーザーに再認証を強制する 

ユーザーに再認証を強制するために、アプリケーションで SAML 認証要求に `ForceAuthn` 属性を含めることができます。 `ForceAuthn` 属性はブール値です。 true に設定すると、ユーザー セッションは Azure AD B2C で無効になり、ユーザーは再認証するように強制されます。 次の SAML 認証要求では、`ForceAuthn` 属性を true に設定する方法を示しています。 


```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="debug-the-saml-protocol"></a>SAML プロトコルをデバッグする

サービス プロバイダーとの統合の構成とデバッグを支援するために、SAML プロトコル用のブラウザー拡張機能を使用できます。たとえば、Chrome 用の [SAML DevTools 拡張機能](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio)、FireFox 用の [SAML トレーサー](https://addons.mozilla.org/es/firefox/addon/saml-tracer/)、[Edge または IE 開発者ツール](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)などです。

これらのツールを使用すると、アプリケーションと Azure AD B2C の統合を確認できます。 次に例を示します。

* SAML 要求に署名が含まれているかどうかを確認し、認可要求へのサインインに使用するアルゴリズムを決定します。
* Azure AD B2C からエラー メッセージが返されるかどうかを確認します。
* アサーション セクションが暗号化されているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

- SAML プロトコルの詳細については、[OASIS の Web サイト](https://www.oasis-open.org/)を参照してください。
- [Azure AD B2C GitHub コミュニティ リポジトリ](https://github.com/azure-ad-b2c/saml-sp-tester)から SAML テスト Web アプリを取得してください。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
