---
title: Azure シングル サインオンの SAML プロトコル
titleSuffix: Microsoft identity platform
description: この記事では、Azure Active Directory でのシングル サインオン (SSO) SAML プロトコルについて説明します。
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 40bf202e0f14f18d817e4e918f8372ba3c0a4ad8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950671"
---
# <a name="single-sign-on-saml-protocol"></a>シングル サインオンの SAML プロトコル

この記事では、Azure Active Directory (Azure AD) がシングル サインオン (SSO) に対してサポートする SAML 2.0 の認証要求と応答について説明します。

次の図は、このプロトコルでのシングル サインオンのシーケンスを示したものです。 クラウド サービス (サービス プロバイダー) は、HTTP リダイレクト バインディングを使用して、 `AuthnRequest` (認証要求) 要素を Azure AD (ID プロバイダー) に渡します。 Azure AD は、HTTP POST バインディングを使用して、 `Response` 要素をクラウド サービスに送信します。

![シングル サインオン (SSO) のワークフロー](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> この記事では、シングル サインオンでの SAML の使用について説明します。 シングル サインオンを処理するその他の方法 (OpenID Connect や統合 Windows 認証など) の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="authnrequest"></a>AuthnRequest

ユーザー認証を要求するため、クラウド サービスは `AuthnRequest` 要素を Azure AD に送信します。 サンプル SAML 2.0 `AuthnRequest` は次の例のようになります。

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| パラメーター | Type | 説明 |
| --- | --- | --- |
| id | 必須 | Azure AD はこの属性を使用して、返される応答の `InResponseTo` 属性を設定します。 ID の 1 文字目に数字を使用することはできないので、一般的な方法としては、GUID の文字列表現の前に "id" のような文字列を付加します。 たとえば、 `id6c1c178c166d486687be4aaf5e482730` は有効な ID です。 |
| Version | 必須 | このパラメーターは **2.0** に設定する必要があります。 |
| IssueInstant | 必須 | これは、UTC 値と [ラウンドトリップ書式 ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings)の DateTime 文字列です。 Azure AD はこの型の DateTime 値を期待しますが、値を評価または使用することはありません。 |
| AssertionConsumerServiceURL | 省略可能 | 指定する場合、このパラメーターは Azure AD でのクラウド サービスの `RedirectUri` と一致する必要があります。 |
| ForceAuthn | 省略可能 | これはブール値です。 true の場合は、ユーザーが Azure AD で有効なセッションを持っている場合であっても、再認証を強制されることを意味します。 |
| IsPassive | 省略可能 | これはブール値で、セッション cookie がある場合はそれを使って、ユーザーの介入なしに、サイレント モードで Azure AD がユーザーを認証する必要があるかどうかを指定します。 true の場合は、Azure AD はセッション cookie を使ってユーザーの認証を試みます。 |

Consent、Destination、AssertionConsumerServiceIndex、AttributeConsumerServiceIndex、ProviderName など、他の `AuthnRequest` 属性はすべて **無視** されます。

Azure AD は、`AuthnRequest` の `Conditions` 要素も無視します。

### <a name="issuer"></a>発行者

`AuthnRequest` の `Issuer` 要素は、Azure AD でのクラウド サービスの **ServicePrincipalNames** のいずれかと厳密に一致する必要があります。 通常、これはアプリケーション登録時に指定される **App ID URI** に設定されます。

次のサンプルは、`Issuer` 要素を含む SAML の抜粋です。

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

この要素は、特定の名前 ID 形式を応答で使用することを要求するものであり、Azure AD に送信される `AuthnRequest` 要素では省略可能です。

`NameIdPolicy` 要素は次のサンプルのようになります。

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

`NameIDPolicy` を指定する場合は、省略可能な `Format` 属性を含めることができます。 `Format` 属性で指定できる値は次のいずれかのみであり、それ以外の値はエラーになります。

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`:Azure Active Directory は、一対の識別子として NameID 要求を発行します。
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`:Azure Active Directory は、電子メール アドレス形式で NameID 要求を発行します。
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`:この値は、Azure Active Directory が要求の形式を選択することを許可します。 Azure Active Directory は、一対の識別子として NameID 要求を発行します。
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`:Azure Active Directory は、現在の SSO 操作に固有となる無作為に生成された値として NameID 要求を発行します。 つまり、値は一時的であり、認証ユーザーの識別に使うことはできません。

`SPNameQualifier` が指定されている場合は、Azure AD により、応答に同じ `SPNameQualifier` が含められます。

Azure AD は `AllowCreate` 属性を無視します。

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext` 要素は、必要な認証方法を指定します。 Azure AD に送信される `AuthnRequest` 要素では省略可能です。 Azure AD では、`urn:oasis:names:tc:SAML:2.0:ac:classes:Password` などの `AuthnContextClassRef` 値をサポートしています。

### <a name="scoping"></a>Scoping
ID プロバイダーのリストが含まれる `Scoping` 要素は、Azure AD に送信される `AuthnRequest` 要素では省略可能です。

指定する場合は、`ProxyCount` 属性、`IDPListOption` 要素、または `RequesterID` 要素を使用しないでください。これらはサポートされていません。

### <a name="signature"></a>署名
`AuthnRequest` 要素内の `Signature` 要素は省略可能です。 Azure AD では、署名がある場合は署名済みの認証要求の検証を行いません。 要求元の検証は、登録されている Assertion Consumer Service の URL に応答することによってのみ提供されます。

### <a name="subject"></a>サブジェクト
`Subject` 要素を含めないでください。 Azure AD は、要求のサブジェクトの指定をサポートしていません。指定した場合は、エラーが返されます。

## <a name="response"></a>Response
要求されたサインオンが正常に完了すると、Azure AD はクラウド サービスに応答を送信します。 サインオンに成功した場合、次のサンプルのような応答が返されます。

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Response

`Response` 要素には、承認要求の結果が含まれます。 Azure AD は、`Response` 要素の `ID`、`Version`、`IssueInstant` の値を設定します。 また、次の属性も設定します。

* `Destination`:サインオンが正常に完了すると、サービス プロバイダー (クラウド サービス) の `RedirectUri` が設定されます。
* `InResponseTo`:応答を開始した `AuthnRequest` 要素の `ID` 属性が設定されます。

### <a name="issuer"></a>発行者

Azure AD は、`Issuer` 要素を  `https://sts.windows.net/<TenantIDGUID>/` に設定します。\<TenantIDGUID> は、Azure AD テナントのテナント ID です。

たとえば、Issuer 要素を含む応答の例は次のようになります。

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

`Status` 要素には、サインオンの成功または失敗を示す値が設定されます。 `StatusCode` 要素が含まれており、この要素には要求の状態を表すコードまたは一連の入れ子になったコードが含まれます。 また、 `StatusMessage` 要素もあり、これにはサインイン プロセス中に生成されたカスタム エラー メッセージが含まれます。

<!-- TODO: Add an authentication protocol error reference -->

サインオンに失敗した場合、次のような SAML 応答が返されます。

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Assertion

`ID`、`IssueInstant`、および `Version` に加えて、Azure AD は応答の `Assertion` 要素の次の要素も設定します。

#### <a name="issuer"></a>発行者

この要素は `https://sts.windows.net/<TenantIDGUID>/` に設定されます。\<TenantIDGUID> は Azure AD テナントのテナント ID です。

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>署名

Azure AD は、サインオンが成功すると応答のアサーションに署名します。 `Signature` 要素にはデジタル署名が含まれ、クラウド サービスはそれを使用してソースを認証し、アサーションの整合性を検証できます。

このデジタル署名を生成するため、Azure AD はメタデータ ドキュメントの `IDPSSODescriptor` 要素の署名キーを使用します。

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>サブジェクト

この要素は、アサーションのステートメントのサブジェクトであるプリンシパルを指定します。 認証されたユーザーを表す `NameID` 要素が含まれます。 `NameID` の値は、トークンの対象であるサービス プロバイダーに対してのみ送信される限定的な識別子です。 永続的であり、取り消すことはできますが、再割り当てはされません。 また、非透過的であるため、ユーザーについての情報はわからず、属性クエリに対する識別子としては使用できません。

`SubjectConfirmation` 要素の `Method` 属性は、常に `urn:oasis:names:tc:SAML:2.0:cm:bearer` に設定されます。

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>条件

この要素は、SAML アサーションの許容される使用方法を定義する条件を指定します。

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` および `NotOnOrAfter` 属性は、アサーションが有効である期間を指定します。

* `NotBefore` 属性の値は、`Assertion` 要素の `IssueInstant` 属性と同じか、またはそれよりもわずかに (1 秒未満) 後です。 Azure AD では、それ自体とクラウド サービス (サービス プロバイダー) 間の時間の違いは考慮されず、この時間に対するバッファーは追加されません。
* `NotOnOrAfter` 属性の値は、`NotBefore` 属性の値より 70 分後です。

#### <a name="audience"></a>対象ユーザー

この要素には、対象を識別する URI が含まれます。 Azure AD は、この要素の値に、サインオンを開始した `AuthnRequest` の `Issuer` 要素の値を設定します。 `Audience` の値を評価するには、アプリケーション登録時に指定された `App ID URI` の値を使用します。

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

`Issuer` の値と同様に、`Audience` の値は Azure AD でクラウド サービスを表すサービス プリンシパル名のいずれかと厳密に一致する必要があります。 ただし、`Issuer` 要素の値が URI 値ではない場合は、応答の `Audience` の値は、プレフィックス `spn:` が付加された `Issuer` 値になります。

#### <a name="attributestatement"></a>AttributeStatement

この要素には、サブジェクトまたはユーザーに関する要求が含まれます。 `AttributeStatement` 要素の例を含む抜粋を次に示します。 省略記号は、要素が複数の属性および属性値を含むことができることを示します。

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Name 要求** - `Name` 属性 (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) の値は、認証されたユーザーのユーザー プリンシパル名です (例: `testuser@managedtenant.com`)。
* **ObjectIdentifier 要求** - `ObjectIdentifier` 属性 (`http://schemas.microsoft.com/identity/claims/objectidentifier`) の値は、Azure AD で認証されたユーザーを表すディレクトリ オブジェクトの `ObjectId` です。 `ObjectId` は、認証されたユーザーの変更不可能で、グローバルに一意であり、再利用が安全な識別子です。

#### <a name="authnstatement"></a>AuthnStatement

この要素は、アサーション サブジェクトが特定の時刻に特定の手段によって認証されたことをアサートします。

* `AuthnInstant` 属性は、Azure AD でユーザーが認証された時刻を指定します。
* `AuthnContext` 要素は、ユーザーの認証に使用された認証コンテキストを指定します。

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
