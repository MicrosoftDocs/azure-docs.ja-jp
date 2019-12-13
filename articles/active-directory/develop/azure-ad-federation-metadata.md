---
title: Azure AD フェデレーション メタデータ |Microsoft Docs
description: この記事では、Azure Active Directory が Azure Active Directory トークンを受け入れるサービスに対して発行するフェデレーション メタデータ ドキュメントについて説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0da348d87d1dedbdc1f97d3a4d0a1a001871a1d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843786"
---
# <a name="federation-metadata"></a>フェデレーション メタデータ
Azure Active Directory (Azure AD) は、Azure AD が発行するセキュリティ トークンを受け入れるように構成されているサービスのフェデレーション メタデータ ドキュメントを発行します。 フェデレーション メタデータ ドキュメントの形式は、「[Web Services Federation Language (WS-Federation) Version 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)」で説明されています。これは、[OASIS SAML (Security Assertion Markup Language) v2.0 のメタデータ](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)の拡張です。

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>テナント固有およびテナント独立のメタデータ エンドポイント
Azure AD は、テナント固有のエンドポイントとテナント独立のエンドポイントを発行します。

テナント固有のエンドポイントは、特定のテナント用に設計されています。 テナント固有のフェデレーション メタデータには、テナント固有の発行者とエンドポイントの情報など、テナントに関する情報が含まれます。 単一のテナントにアクセスを制限するアプリケーションでは、テナント固有のエンドポイントを使用します。

テナント独立のエンドポイントは、すべての Azure AD テナントに共通する情報を提供します。 この情報は、 *login.microsoftonline.com* でホストされているテナントに適用され、テナント全体で共有されます。 マルチテナント アプリケーションの場合は、特定のテナントに関連付けられていないため、テナント独立のエンドポイントをお勧めします。

## <a name="federation-metadata-endpoints"></a>フェデレーション メタデータ エンドポイント
Azure AD は、フェデレーション メタデータを `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`で発行します。

**テナント固有のエンドポイント**の場合、`TenantDomainName` に次の種類のいずれかを指定できます。

* `contoso.onmicrosoft.com`など、Azure AD のテナントの登録済みのドメイン名。
* `72f988bf-86f1-41af-91ab-2d7cd011db45`など、ドメインの変更できないテナント ID。

**テナント独立のエンドポイントの場合**、`TenantDomainName` は `common` です。 このドキュメントでは、login.microsoftonline.com でホストされているすべての Azure AD テナントに共通するフェデレーション メタデータの要素のみを示します。

たとえば、テナント固有のエンドポイントは、 `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`にすることができます。 テナント独立のエンドポイントは、[https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml) です。 ブラウザーにこの URL を入力することで、フェデレーション メタデータ ドキュメントを表示できます。

## <a name="contents-of-federation-metadata"></a>フェデレーション メタデータの内容
次のセクションでは、Azure AD によって発行されたトークンを使用するサービスに必要な情報を提供します。

### <a name="entity-id"></a>エンティティ ID
`EntityDescriptor` 要素は `EntityID` 属性を含みます。 `EntityID` 属性の値は発行者、つまり、トークンを発行した Security Token Service (STS) を表します。 トークンを受信したときに、発行者を検証することが重要です。

次のメタデータは、`EntityID` 要素を含む、サンプルのテナント固有の `EntityDescriptor` 要素を示しています。

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
テナント独立のエンドポイントのテナント ID を自分のテナント ID に置き換えて、テナント固有の `EntityID` 値を作成することができます。 結果の値は、トークンの発行者と同じになります。 この方法では、マルチテナント アプリケーションを使用して特定のテナントの発行者を検証できます。

次のメタデータは、テナント独立の `EntityID` 要素のサンプルを示します。 なお、 `{tenant}` はリテラルであり、プレースホルダーではないことに注意してください。

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>トークン署名証明書
サービスが Azure AD テナントによって発行されたトークンを受信するとき、トークンの署名は、フェデレーション メタデータ ドキュメントで発行された署名キーによって、検証される必要があります。 フェデレーション メタデータには、テナントでトークンの署名に使用される証明書の公開部分が含まれています。 証明書の未加工のバイト数は、 `KeyDescriptor` 要素にあります。 トークン署名証明書が署名で有効なのは、`use` 属性の値が `signing` の場合だけです。

Azure AD によって発行されたフェデレーション メタデータ ドキュメントには、Azure AD によって署名証明書の更新が準備されているときなどに、複数の署名キーが含まれている可能性があります。 フェデレーション メタデータ ドキュメントに複数の証明書が含まれている場合、トークンを検証するサービスは、ドキュメント内のすべての証明書をサポートする必要があります。

次のメタデータは、署名キーを含むサンプルの `KeyDescriptor` 要素を示しています。

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor` 要素は、フェデレーション メタデータ ドキュメントでは、WS-Federation 固有のセクションと SAML 固有のセクションという 2 つの場所にあります。 両方のセクションで発行された証明書は同じになります。

WS-Federation 固有のセクションで、WS-Federation メタデータ リーダーは、`SecurityTokenServiceType` 型を含む `RoleDescriptor` 要素から証明書を読み取ります。

`RoleDescriptor` 要素の例を次に示します。

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

SAML に固有のセクションで、WS-Federation メタデータ リーダーは、 `IDPSSODescriptor` 要素から証明書を読み取ります。

`IDPSSODescriptor` 要素の例を次に示します。

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
テナント固有の証明書とテナント独立の証明書の形式には、違いはありません。

### <a name="ws-federation-endpoint-url"></a>WS-Federation エンドポイントの URL
フェデレーション メタデータには、WS-Federation プロトコルで Azure AD がシングル サインインおよびシングル サインアウトに使用する URL が含まれています。 このエンドポイントは `PassiveRequestorEndpoint` 要素にあります。

次のメタデータは、テナント固有のエンドポイントに対するサンプルの `PassiveRequestorEndpoint` 要素を示しています。

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
テナント独立のエンドポイントの場合は、次の例に示すように、WS-Federation URL は WS-Federation エンドポイントにあります。

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>SAML プロトコル エンドポイントの URL
フェデレーション メタデータには、SAML 2.0 プロトコルで Azure AD がシングル サインインおよびシングル サインアウトに使用する URL が含まれています。 これらのエンドポイントは、 `IDPSSODescriptor` 要素にあります。

サインイン URL とサインアウト URL は、`SingleSignOnService` 要素と `SingleLogoutService` 要素にあります。

次のメタデータは、テナント固有のエンドポイントに対するサンプルの `PassiveResistorEndpoint` を示しています。

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

同様に、次の例に示すように、共通の SAML 2.0 プロトコル エンドポイントのエンドポイントは、テナント独立のフェデレーション メタデータに発行されます。

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
