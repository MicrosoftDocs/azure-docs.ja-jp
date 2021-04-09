---
title: SAML ID プロバイダー オプションを使用してサインインを設定する
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C でサインイン SAML ID プロバイダー (IdP) オプションを構成します。
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
ms.openlocfilehash: 43c57950d317de42df666ddd25cbcb2e9a4c9611
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488875"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して SAML ID プロバイダー オプションを構成する

Azure Active Directory B2C (Azure AD B2C) では、SAML 2.0 ID プロバイダーとのフェデレーションがサポートされています。 この記事では、SAML ID プロバイダーを使用したサインインを有効にするときに使用できる構成オプションについて説明します。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>要求のマッピング

**OutputClaims** 要素には、SAML ID プロバイダーにより返される要求の一覧が存在します。 お使いのポリシーに定義されている要求の名前を、ID プロバイダーで定義されている名前にマップする必要があります。 要求 (アサーション) の一覧については、ID プロバイダーを確認してください。 また、ID プロバイダーから返される SAML 応答の内容を確認することもできます。 詳細については、[SAML メッセージのデバッグ](#debug-saml-protocol)に関するページを参照してください。 要求を追加するには、まず[要求を定義](claimsschema.md)してから、要求を出力要求コレクションに追加します。

`DefaultValue` 属性を設定している限り、ID プロバイダーにより返されない要求を追加することもできます。 既定値は、[コンテキスト要求](#enable-use-of-context-claims)を使用して静的または動的にすることができます。

出力要求要素には、次の属性が含まれています。

- **ClaimTypeReferenceId** は要求の種類への参照です。 
- **PartnerClaimType** - SAML アサーションで表示されるプロパティの名前です。 
- **DefaultValue** は、事前に定義された既定値です。 要求が空白の場合は、既定値が使用されます。 また、関連付け ID やユーザー IP アドレスなどのコンテキスト値を含む[要求リゾルバー](claim-resolver-overview.md)を使用することもできます。

### <a name="subject-name"></a>サブジェクト名

**[件名]** の SAML アサーション **NameId** を正規化された要求として読み取るには、要求 **PartnerClaimType** を `SPNameQualifier` 属性の値に設定します。 `SPNameQualifier` 属性が表示されない場合は、**PartnerClaimType** 要求を `NameQualifier` 属性の値に設定します。

SAML アサーション:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

出力要求：

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

SAML アサーションに `SPNameQualifier` または `NameQualifier` の両方の属性が表示されない場合は、**PartnerClaimType** 要求を `assertionSubjectName` に設定します。 **NameId** がアサーション XML 内の最初の値であることを確認します。 複数のアサーションを定義した場合、Azure AD B2C は、最後のアサーションからサブジェクト値を取得します。


## <a name="configure-saml-protocol-bindings"></a>SAML プロトコル バインドを構成する

SAML 要求は、ID プロバイダーのメタデータ `SingleSignOnService` 要素で指定されているように、ID プロバイダーに送信されます。 ID プロバイダーのほとんどの認可要求は、HTTP GET 要求の URL クエリ文字列で直接伝達されます (メッセージが比較的短いため)。 両方の SAML 要求のバインドを構成する方法については、ID プロバイダーのドキュメントを参照してください。

2 つのバインドを持つ Azure AD メタデータ シングル サインオン サービスの例を次に示します。 `HTTP-Redirect` は、SAML ID プロバイダー メタデータに最初に表示されるため、`HTTP-POST` よりも優先されます。

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

SAML 応答は、HTTP POST バインドを介して Azure AD B2C に送信されます。 Azure AD B2C ポリシー メタデータによって、`AssertionConsumerService` バインドが `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST` に設定されます。

Azure AD B2C ポリシー メタデータの Assertion Consumer Service 要素の例を次に示します。

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>SAML 要求署名を構成する

Azure AD B2C では、**SamlMessageSigning** 暗号化キーを使用して、すべての送信認証要求が署名されます。 SAML 要求署名を無効にするには、**WantsSignedRequests** を `false` に設定します。 メタデータが `false` に設定されている場合、**SigAlg** と **Signature** パラメーター (クエリ文字列または post パラメーター) は要求から省略されます。

このメタデータによって **AuthnRequestsSigned** 属性も制御されます。これは、ID プロバイダーと共有される Azure AD B2C の技術プロファイルのメタデータに含まれます。 技術プロファイル メタデータ内の **WantsSignedRequests** 値が `false` に設定され、ID プロバイダー メタデータ **WantAuthnRequestsSigned** 値が `false` に設定されている、または指定がない場合、Azure AD B2C では要求の署名は行われません。

次の例では、SAML 要求から署名を削除します。

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>署名アルゴリズム

Azure AD B2C では、SAML 要求に署名するために `Sha1` が使用されます。 使用するアルゴリズムを構成するには、**XmlSignatureAlgorithm** メタデータを使用します。 指定できる値は `Sha256`、`Sha384`、`Sha512`、または `Sha1` (既定値) です。 このメタデータは、SAML 要求の **SigAlg** パラメーター (クエリ文字列または post パラメーター) の値を制御します。 両方の側で同じ値の署名アルゴリズムを構成するようにします。 証明書でサポートされているアルゴリズムのみを使用してください。

### <a name="include-key-info"></a>キー情報を含める

Azure AD B2C バインドが `HTTP-POST` に設定されていることが ID プロバイダーによって示される場合、Azure AD B2C では、SAML 要求の本文に署名とアルゴリズムが含められます。 バインドが `HTTP-POST` に設定されている場合に証明書の公開キーを含めるように Azure AD を構成することもできます。 **IncludeKeyInfo** メタデータを `true` または `false` に設定します。 次の例では、Azure AD によって証明書の公開キーは含まれません。

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>SAML 要求名 ID を構成する

SAML 認可要求要素は、`<NameID>` SAML 名前識別子の形式を示します。 ここでは、既定の構成と、名前 ID 要素をカスタマイズする方法について説明します。

## <a name="preferred-username"></a>推奨ユーザー名

サインインのユーザー体験中に、証明書利用者アプリケーションが特定のユーザーをターゲットとする可能性があります。 Azure AD B2C を使用すると、適切なユーザー名を SAML ID プロバイダーに送信できます。 **InputClaims** 要素は、SAML 認可要求の **Subject** 内で **NameId** を送信するために使用されます。

認可要求内にサブジェクト名 ID を含めるには、`<CryptographicKeys>` の直後に次の `<InputClaims>` 要素を追加します。 **PartnerClaimType** は `subject` に設定されている必要があります。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

この例では、Azure AD B2C により、SAML 認可要求の **Subject** 内に **NameId** を含む **signInName** 要求の値が送信されます。

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

`{OIDC:LoginHint}` などの[コンテキスト要求](claim-resolver-overview.md)を使用して、要求値を設定できます。

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>名前 ID ポリシーの形式

既定では、SAML 認可要求によって `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` ポリシーが指定されます。 これは、要求されたサブジェクトに対して ID プロバイダーがサポートするあらゆる種類の識別子を使用できることを示します。

この動作を変更するには、サポートされている名前 ID ポリシーに関するガイダンスについて、ID プロバイダーのドキュメントを参照してください。 次に、対応するポリシー形式で `NameIdPolicyFormat` メタデータを追加します。 次に例を示します。

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

次の SAML 認可要求には、名前 ID ポリシーが含まれています。

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>新しいアカウントの作成を許可する

[名前 ID ポリシーの形式](#name-id-policy-format)を指定する場合は、**NameIDPolicy** の `AllowCreate` プロパティを指定して、サインイン フロー中に新しいアカウントを作成することが ID プロバイダーに許可されているかどうかを示すこともできます。 ガイダンスについては、ID プロバイダーのドキュメントを参照してください。

Azure AD B2C では、既定で `AllowCreate` プロパティが省略されます。 この動作は、`NameIdPolicyAllowCreate` メタデータを使用して変更できます。 このメタデータの値は `true` または `false` です。

次の例は、`NameIDPolicy` の `AllowCreate` プロパティを `true` に設定する方法を示しています。

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


次の例では、認可要求内に **NameIDPolicy** 要素の **AllowCreate** を含む認可要求を示します。


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>認証コンテキスト クラスの参照を含める

SAML 認可要求には、認可要求のコンテキストを指定する **AuthnContext** 要素を含めることができます。 この要素には、認証コンテキスト クラスの参照を含めることができます。これにより、ユーザーに提示する認証メカニズムが SAML ID プロバイダーに通知されます。

認証コンテキスト クラスの参照を構成するには、**IncludeAuthnContextClassReferences** メタデータを追加します。 値に、認証コンテキスト クラスを識別する URI 参照を 1 つ以上指定します。 複数の URI をコンマ区切りのリストで指定します。 サポートされている **AuthnContextClassRef** URI に関するガイダンスについては、ID プロバイダーのドキュメントを参照してください。

次の例では、ユーザー名とパスワードの両方を使用してサインインすること、および保護されたセッション (SSL/TLS) を使用してユーザー名とパスワードでサインインすることをユーザーに許可します。

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

次の SAML 認可要求には、認証コンテキスト クラスの参照が含まれています。

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>認可要求にカスタム データを含める

必要に応じて、Azure AD BC と ID プロバイダーの両方によって合意されたプロトコル メッセージ拡張機能要素を含めることができます。 拡張機能は、XML 形式で表示されます。 CDATA 要素 `<![CDATA[Your IDP metadata]]>` 内に XML データを追加することで、拡張機能要素を含めます。 拡張機能要素がサポートされているかどうかは、ID プロバイダーのドキュメントを確認してください。

次の例では、拡張機能データの使用方法を示します。

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

SAML プロトコル メッセージ拡張機能を使用する場合、SAML 応答は次の例のようになります。

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>署名済みの SAML 応答が必須

Azure AD B2C では、すべての受信アサーションが署名されている必要があります。 この要件を削除するには、**WantsSignedAssertions** を `false` に設定します。 この場合、ID プロバイダーによってアサーションは署名されませんが、署名された場合でも、Azure AD B2C によって署名は検証されません。

**WantsSignedAssertions** メタデータによって SAML メタデータ フラグ **WantAssertionsSigned** が制御されます。これは、ID プロバイダーと共有される Azure AD B2C の技術プロファイルのメタデータに含まれます。

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

アサーションの検証を無効にした場合、応答メッセージの署名の検証も無効にすることをお勧めします。 **ResponsesSigned** メタデータを `false` に設定します。 この場合、ID プロバイダーによって SAML 応答メッセージは署名されませんが、署名された場合でも、Azure AD B2C によって署名は検証されません。

次の例では、メッセージとアサーションの署名の両方を削除します。

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>暗号化 SAML 応答が必須

すべての受信アサーションの暗号化を必須にするには、**WantsEncryptedAssertions** メタデータを設定します。 暗号化が必須の場合は、ID プロバイダーによって Azure AD B2C 技術プロファイルで暗号化証明書の公開キーが使用されます。 Azure AD B2C により、暗号化証明書のプライベート部分を使用して応答アサーションが復号化されます。

アサーションの暗号化を有効にした場合、応答の署名の検証を無効にする必要が生じることもあります (詳細については、「[署名済みの SAML 応答が必須](#require-signed-saml-responses)」を参照)。

**WantsEncryptedAssertions** メタデータが `true` に設定されている場合、Azure AD B2C 技術プロファイルのメタデータには **encryption** セクションが含まれます。 ID プロバイダーはメタデータを読み取り、Azure AD B2C 技術プロファイルのメタデータに提供されている公開キーを使用して SAML 応答アサーションを暗号化します。

次の例は、暗号化に使用される SAML メタデータのキー記述子セクションを示しています。

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

SAML 応答のアサーションを暗号化する場合:

1. 一意の識別子を使用して[ポリシー キーを作成](identity-provider-generic-saml.md#create-a-policy-key)します。 たとえば、「 `B2C_1A_SAMLEncryptionCert` 」のように入力します。
2. SAML 技術プロファイルの **CryptographicKeys** コレクションで、 **StorageReferenceId** を、最初の手順で作成したポリシー キーの名前に設定します。 `SamlAssertionDecryption` ID は、暗号化キーを使用して、SAML 応答のアサーションを暗号化および復号化することを示します。

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. 技術プロファイル メタデータ **WantsEncryptedAssertions** を `true` に設定します。
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. ID プロバイダーを新しい Azure AD B2C の技術プロファイルのメタデータで更新します。 **use** プロパティ付きの **KeyDescriptor** が証明書の公開キーを含む `encryption` に設定されます。

## <a name="enable-use-of-context-claims"></a>コンテキスト要求の使用を有効にする

入力出力および出力要求のコレクションでは、`DefaultValue` 属性を設定している限り、ID プロバイダーによって返されない要求を含めることができます。 また、[コンテキスト要求](claim-resolver-overview.md)を使用して、技術プロファイルに含めることもできます。 コンテキスト要求を使用するには、次の手順に従います。

1. [BuildingBlocks](buildingblocks.md) 内の [ClaimsSchema](claimsschema.md) 要素に要求の種類を追加します。
2. 入力または出力のコレクションに出力要求を追加します。 次の例では、最初の要求で ID プロバイダーの値を設定します。 2 番目の要求では、ユーザーの IP アドレス [コンテキスト要求](claim-resolver-overview.md)を使用します。
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>シングル ログアウトを無効にする

アプリケーションのサインアウト要求時に、Azure AD B2C によって SAML ID プロバイダーからのサインアウトが試行されます。 詳しくは、[Azure AD B2C のセッション サインアウト](session-behavior.md#sign-out)に関する記事をご覧ください。シングル ログアウト動作を無効にするには、**SingleLogoutEnabled** メタデータを `false` に設定します。

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>SAML プロトコルをデバッグする

SAML ID プロバイダーとのフェデレーションの構成とデバッグを支援するために、SAML プロトコル用のブラウザー拡張機能を使用できます。たとえば、Chrome 用の [SAML DevTools 拡張機能](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio)、FireFox 用の [SAML トレーサー](https://addons.mozilla.org/es/firefox/addon/saml-tracer/)、[Edge または IE 開発者ツール](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)などです。

これらのツールを使用して、Azure AD B2C と SAML ID プロバイダーの統合を確認できます。 次に例を示します。

* SAML 要求に署名が含まれているかどうかを確認し、認可要求へのサインインに使用するアルゴリズムを決定します。
* `AttributeStatement` セクションの下にある要求 (アサーション) を取得します。
* ID プロバイダーによってエラー メッセージが返されるかどうかを確認します。
* アサーション セクションが暗号化されているかどうかを確認します。

## <a name="next-steps"></a>次の手順

- [Application Insights](troubleshoot-with-application-insights.md) を使用してカスタム ポリシーに関する問題を診断する方法について説明します。 

::: zone-end
