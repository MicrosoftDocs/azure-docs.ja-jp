---
title: Azure Active Directory B2C 内のカスタム ポリシーで SAML 技術プロファイルを定義する |Microsoft Docs
description: Azure Active Directory B2C 内のカスタム ポリシーで SAML 技術プロファイルを定義します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d5120b7569acbe9735ea1a70fcb609d322d60793
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154373"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで SAML 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C では、SAML 2.0 プロトコルの ID プロバイダーにサポートを提供しています。 この記事では、この標準化されたプロトコルをサポートするクレーム プロバイダーとやりとりするための、技術プロファイルの詳細について説明します。 SAML 技術プロファイルを使用すると、[ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) や [Salesforce](active-directory-b2c-setup-sf-app-custom.md) などの SAML ベースの ID プロバイダーとフェデレーションできます。 このフェデレーションにより、ユーザーは、既存のソーシャル ID またはエンタープライズ ID でサインインできます。

## <a name="metadata-exchange"></a>メタデータ交換

メタデータとは、サービス プロバイダーや ID プロバイダーなど、SAML パーティーの構成を公開するために SAML プロトコルで使用される情報です。 メタデータは、サインインとサインアウト、証明書、サインイン方法など、サービスの場所を定義します。 ID プロバイダーは、メタデータを使用して Azure AD B2C と通信する方法を知ります。 メタデータは XML 形式で構成され、他方の当事者がメタデータの整合性を検証できるように、デジタル署名で署名されることもあります。 Azure AD B2C が SAML ID プロバイダーとフェデレーションを行う場合、SAML 要求を開始して、SAML 応答を待機するサービス プロバイダーとして機能します。 また、場合によっては、ID プロバイダーが開始した未承諾の SAML 認証を受け入れます。 

メタデータは、両当事者で「静的なメタデータ」または「動的メタデータ」として構成できます。 静的なモードでは、1 つの当事者からメタデータ全体をコピーし、他方の当事者に設定します。 動的モードでは、他方の当事者が動的に構成を読み取る間にメタデータに URL を設定します。 原則は同じであり、Azure AD B2C 技術プロファイルのメタデータを ID プロバイダーに設定し、ID プロバイダーのメタデータを Azure AD B2C に設定します。

各 SAML ID プロバイダーにはサービス プロバイダーを公開し、設定する様々な手順がありますが、この Azure AD B2C の場合は、ID プロバイダーに Azure AD B2C メタデータを設定します。 この詳しい手順については、ID プロバイダーのドキュメントを参照してください。

次の例は、Azure AD B2C テクニカル プロファイルの SAML メタデータへの URL アドレスを示しています。

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

次の値を置き換えます。

- **your-tenant-name** は、fabrikam.b2clogin.com などのテナント名に置き換えます。
- **your-policy** は、実際のポリシー名に置き換えます。 SAML プロバイダーのテクノロジ プロファイルを構成するポリシー、またはそのポリシーから継承されるポリシーを使用します。
- **your-technical-profile** は、実際の SAML ID プロバイダー技術プロファイルの名前に置き換えます。

## <a name="digital-signing-certificates-exchange"></a>デジタル署名証明書の交換

Azure AD B2C と SAML ID プロバイダー間の信頼関係をビルドするには、秘密キーで有効な X509 証明書を提供する必要があります。 秘密キー付きの証明書 (.pfx ファイル) を Azure AD B2C ポリシー鍵ストアにアップロードします。 Azure AD B2C は、提供された証明書を使用して SAML サインイン要求にデジタル署名します。 

証明書は、次の方法で使用されます。

- Azure AD B2C は証明書の Azure AD B2C 秘密キーを使用して SAML 要求を生成し、署名します。 SAML 要求が ID プロバイダーに送信され、ID プロバイダーが証明書の Azure AD B2C 秘密キーを使用して要求を検証します。 Azure AD B2C の公開証明書には、技術プロファイル メタデータを介してアクセスできます。 代わりに、.cer ファイルを SAML ID プロバイダーに手動でアップロードすることもできます。
- ID プロバイダーは Azure AD B2C に送信されたデータに、証明書の ID プロバイダーの秘密キーを使用して署名します。 Azure AD B2C では、ID プロバイダーの公開証明書を使用してデータを検証します。 ID プロバイダーはそれぞれ様々なセットアップ手順を持ちますが、詳しい手順については、ID プロバイダーのドキュメントを参照してください。 Azure AD B2C では、ポリシーで ID プロバイダーのメタデータを使用して証明書の公開キーにアクセスする必要があります。

自己署名証明書は、ほとんどのシナリオで受け入れ可能です。 運用環境では、証明機関が発行した X509 証明書を使用することをお勧めします。 また、このドキュメントの後半で説明する、非運用環境に無効では、両側で SAML の署名を無効にできます。

次の図は、メタデータと証明書の交換を示しています。

![メタデータおよび証明書の交換](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)


## <a name="digital-encryption"></a>デジタル暗号化

SAML 応答のアサーションを暗号化するには、ID プロバイダーは常に Azure AD B2C の技術プロファイルの暗号化証明書の公開キーを使用します。 Azure AD B2C はデータの暗号化を解除する必要がある場合には、暗号化証明書の秘密部分を使用します。

SAML 応答のアサーションを暗号化する場合:

1. 有効な秘密キー付きの X509 証明書 (.pfx ファイル) を Azure AD B2C ポリシー鍵ストアにアップロードします。
2. 識別子 `SamlAssertionDecryption` の **CryptographicKey** 要素を技術プロファイル **CryptographicKeys** コレクションに追加します。 **StorageReferenceId** を、ステップ 1 で作成したポリシー キーの名前に設定します。
3. 技術プロファイル メタデータ **WantsEncryptedAssertions** を `true` に設定します。
4. ID プロバイダーを新しい Azure AD B2C の技術プロファイル メタデータで更新します。 **use** プロパティ付きの **KeyDescriptor** が証明書の公開キーを含む `encryption` に設定されます。

次の例では、メタデータの Azure AD B2C 技術プロファイル暗号化セクションを示しています。

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="identity-provider-initiated-flow"></a>ID プロバイダー開始フロー

IDP によって開始されたシングル サインオン セッションで (要請されていない要求) で、要請されていない SAML 応答がサービス プロバイダー (この場合は Azure AD B2C の技術プロファイル) に送信されます。 このフローでは、ユーザーは Web アプリケーションを最初に使用せず ID プロバイダーに送られます。 要求が送信されると、認証ページが ID プロバイダーによってユーザーに提供されます。 ユーザーがサインインを完了すると、アサーションを含む SAML 応答のある Azure AD B2C に要求がリダイレクトされます。 Azure AD B2C はアサーションを読み取って新しい SAML トークンを発行し、証明書利用者アプリケーションにユーザーをリダイレクトします。 リダイレクトは **AssertionConsumerService**要素の **Location** プロパティによって実行されます。


![開始された SAML IDP](media/saml-technical-profile/technical-profile-idp-saml-idp-initiated.png) 

ID プロバイダー開始フローを作成する際には、次のポリシーの要件を考慮してください。

- 最初のオーケストレーション手順は、SAML の技術プロファイルを指す 1 つの要求交換とする必要があります。
- 技術プロファイルでは、**IdpInitiatedProfileEnabled** というメタデータ項目を `true` に設定する必要があります。
- 証明書利用者ポリシーは、SAML 証明書利用者とする必要があります。
- 証明書利用者ポリシーでは、**IdpInitiatedProfileEnabled** というメタデータ項目を `true` に設定する必要があります。
- 要請されていない応答は `/your-tenant/your-policy/samlp/sso/assertionconsumer` エンドポイントに送信される必要があります。 応答に含まれているすべてのリレー状態は、証明書利用者に転送されます。 値 **your-tenant** をテナント名に置き換えます。 **your-policy**を証明書利用者のポリシー名に置き換えます。
    
## <a name="protocol"></a>Protocol

Protocol 要素の **Name** 属性は `SAML2` に設定する必要があります。 

## <a name="output-claims"></a>出力要求
 
**OutputClaims** 要素には、`AttributeStatement` セクション下に SAML ID プロバイダーにより返される要求の一覧が存在します。 お使いのポリシーに定義されている要求の名前を、ID プロバイダーで定義されている名前にマップする必要があるかもしれません。 `DefaultValue` 属性を設定している限り、ID プロバイダーにより返されない要求を追加することもできます。
 
**[件名]** の SAML アサーション **NamedId** を正規化された要求として 読み取るには、要求 **PartnerClaimType** を `assertionSubjectName` に設定します。 **NameId** がアサーション XML 内の最初の値であることを確認します。 複数のアサーションを定義した場合、Azure AD B2C は、最後のアサーションからサブジェクト値を取得します。
 
**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。
 
次の例は、Facebook ID プロバイダーにより返される要求を示しています。

- **SocialIdpUserId** 要求は、**assertionSubjectName** 要求にマップされます。
- **givenName** 要求にマップされている **first_name** 要求。
- **surname** 要求にマップされている **last_name** 要求。
- どの名前にもマップされていない **displayName** 要求。
- どの名前にもマップされていない **email** 要求。
 
また、技術プロファイルは、ID プロバイダーにより返されない要求も返します。 
 
- ID プロバイダーの名前を保持する **identityProvider** 要求。
- 既定値の **socialIdpAuthentication** である **authenticationSource** 要求。
 
```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| PartnerEntity | はい | SAML ID プロバイダーのメタデータの URL です。 ID プロバイダーのメタデータをコピーし、CDATA 要素 `<![CDATA[Your IDP metadata]]>` 内に追加します |
| WantsSignedRequests | いいえ  | 技術プロファイルでは、送信認証要求すべてを署名する必要があるかどうかを示します。 指定できる値: `true` または `false`。 既定値は `true` です。 値を `true` に設定すると、 **SamlMessageSigning** 暗号化キーを指定する必要があり、送信認証要求すべてが署名されます。 値が `false` に設定されている場合、**SigAlg** と **Signature** パラメーター (クエリ文字列または post パラメーター) は要求から省略されます。 このメタデータは、メタデータ **AuthnRequestsSigned** 属性も制御し、これは ID プロバイダーと供給される Azure AD B2C の技術プロファイルのメタデータに出力されます。 技術プロファイル メタデータ内の **WantsSignedRequests** が `false` に設定され、ID プロバイダー メタデータ **WantAuthnRequestsSigned** が `false` に設定されているか指定されていない場合、Azure AD B2C は、要求に署名しません。 |
| XmlSignatureAlgorithm | いいえ  | SAML 要求に署名するために Azure AD B2C が使用するメソッド。 このメタデータは、SAML 要求の **SigAlg** パラメーター (クエリ文字列または post パラメーター) の値を制御します。 可能な値: `Sha256`、`Sha384`、`Sha512`、または`Sha1`。 両方の側で同じ値の署名アルゴリズムを構成するようにします。 証明書でサポートされているアルゴリズムのみを使用してください。 | 
| WantsSignedAssertions | いいえ  | 技術プロファイルで、着信アサーションすべてに署名が必要かどうかを示します。 指定できる値: `true` または `false`。 既定値は `true` です。 値が `true` に設定されている場合、ID プロバイダーによって Azure AD B2C に送信されるすべてのアサーション セクション `saml:Assertion` に署名する必要があります。 値が `false` に設定されている場合、ID プロバイダーは、アサーションを署名しませんが、その場合でも Azure AD B2C は署名を検証しません。 このメタデータは、メタデータ フラグ **WantsAssertionsSigned** も制御し、これは ID プロバイダーと供給される Azure AD B2C の技術プロファイルのメタデータに出力されます。 アサーションの検証を無効にした場合、応答の署名の検証も無効にできます (詳細については、**ResponsesSigned** を参照)。 |
| ResponsesSigned | いいえ  | 指定できる値: `true` または `false`。 既定値は `true` です。 値が `false` に設定されている場合、ID プロバイダーは、SAML 応答を署名しませんが、その場合でも Azure AD B2C は署名を検証しません。 値が `true` に設定されている場合、ID プロバイダーによって Azure AD B2C に送信される SAML 応答が署名され、必ず検証されます。 SAML 応答の検証を無効にした場合、アサーション署名の検証も無効にできます (詳細については、**WantsSignedAssertions** を参照)。 |
| WantsEncryptedAssertions | いいえ  | 技術プロファイルで、着信アサーションすべてに暗号化が必要かどうかを示します。 指定できる値: `true` または `false`。 既定値は `false` です。 値が `true` に設定されている場合、ID プロバイダーによって Azure AD B2C に送信されるアサーションが必ず署名され、**SamlAssertionDecryption** 暗号化キーを指定する必要があります。 値が `true` に設定されている場合、Azure AD B2C 技術プロファイルのメタデータには **encryption** セクションが含まれます。 ID プロバイダーはメタデータを読み取り、Azure AD B2C 技術プロファイルのメタデータに提供されている公開キーを使用して SAML 応答アサーションを暗号化します。 アサーションの暗号化を有効にした場合、応答の署名の検証を無効にする必要があります (詳細については、**ResponsesSigned** を参照)。 | 
| IdpInitiatedProfileEnabled | いいえ  | SAML ID プロバイダーのプロファイルによって開始されたシングル サインオン セッションのプロファイルが有効かどうかを示します。 指定できる値: `true` または `false`。 既定では、 `false`です。 ID プロバイダーによって開始されたフローでは、ユーザーが外部から認証され、要請されていない応答は Azure AD B2C に送信されます。次にこれがトークンを使用してオーケストレーションの手順を実行し、応答を証明書利用者アプリケーションに送信します。 |
| NameIdPolicyFormat | いいえ  | 要求されたサブジェクトを表すために使用する名前識別子に対する制約を指定します。 省略した場合、要求されたサブジェクトに対して ID プロバイダーがサポートするあらゆる種類の識別子を使用できます。 たとえば、`urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` を使用します。 **NameIdPolicyFormat** は **NameIdPolicyAllowCreate** とともに使用できます。 サポートされている名前 ID ポリシーに関するガイダンスについては、ID プロバイダーのドキュメントを参照してください。 |
| NameIdPolicyAllowCreate | いいえ  | **NameIdPolicyFormat** を使用するときに、**NameIDPolicy** の `AllowCreate` プロパティも指定できます。 このメタデータの値は `true` または `false` で、ID プロバイダーがサインイン フロー中に新しいアカウントを作成できるかどうかを示します。 この詳しい手順については、ID プロバイダーのドキュメントを参照してください。 |
| AuthenticationRequestExtensions | いいえ  | Azure AD BC と ID プロバイダー間で同意される省略可能なプロトコル メッセージ拡張機能要素。 拡張機能は、XML 形式で表示されます。 CDATA 要素 `<![CDATA[Your IDP metadata]]>` 内部に XML データを追加します。 拡張機能要素がサポートされているかどうかは、ID プロバイダーのドキュメントを確認してください。 |
| IncludeAuthnContextClassReferences | いいえ  | 認証コンテキスト クラスを識別する URI 参照を 1 つ以上指定します。 たとえば、ユーザーがユーザー名とパスワードのみでサインインできるようにするには、値を `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` に設定します。 保護されたセッション (SSL/TLS) でユーザー名とパスワードを使用してサインインできるようにするには、`PasswordProtectedTransport` を指定します。 サポートされている **AuthnContextClassRef** URI に関するガイダンスについては、ID プロバイダーのドキュメントを参照してください。 |
| IncludeKeyInfo | いいえ  | バインディングが `HTTP-POST` に設定されているときに、証明書の公開キーが SAML 認証要求に含まれるかどうかを示します。 指定できる値: `true` または `false`。 |

## <a name="cryptographic-keys"></a>暗号化キー

**CryptographicKeys** 要素には次の属性が存在します。

| Attribute |必須 | 説明 |
| --------- | ----------- | ----------- |
| SamlMessageSigning |はい | SAML メッセージを署名するために使用する X509 証明書 (RSA キー セット)。 Azure AD B2C では、このキーを使用して、要求に署名し、ID プロバイダーに送信します。 |
| SamlAssertionDecryption |はい | SAML メッセージを復号化するために使用する X509 証明書 (RSA キー セット)。 この証明書は、ID プロバイダーによって提供される必要があります。 Azure AD B2C では、この証明書を使用して、ID プロバイダーによって送信されるデータを復号化します。 |
| MetadataSigning |いいえ  | SAML データを署名するために使用する X509 証明書 (RSA キー セット)。 Azure AD B2C では、このキーを使用して、メタデータに署名します。  |

## <a name="examples"></a>例

- [カスタム ポリシーを使って ADFS を SAML ID プロバイダーとして追加する](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [SAML を利用した、Salesforce アカウントでのサインイン](active-directory-b2c-setup-sf-app-custom.md)













