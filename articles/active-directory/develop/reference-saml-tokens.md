---
title: SAML 2.0 トークン要求のリファレンス | Azure
titleSuffix: Microsoft identity platform
description: Microsoft の ID プラットフォームから発行された SAML 2.0 トークンに含まれる要求の詳細と、JWT のそれに相当するものなど、要求のリファレンス。
author: kenwith
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/09/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.custom: aaddev
ms.openlocfilehash: 9c3132985866a4c245984ef632107c05ca1b3350
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348384"
---
# <a name="saml-token-claims-reference"></a>SAML トークン要求のリファレンス

認証フローの処理ごとに、Microsoft ID プラットフォームから数種類のセキュリティ トークンが発行されます。 このドキュメントでは、SAML 2.0 トークンの形式、セキュリティ特性、内容について説明します。

## <a name="claims-in-saml-tokens"></a>SAML トークンの要求

> [!div class="mx-codeBreakAll"]
> | 名前 | 同等の JWT 要求 | 説明 | 例 |
> | --- | --- | --- | ------------|
> |対象ユーザー | `aud` |トークンの対象となる受信者。 トークンを受信するアプリケーションは、対象ユーザーの値が正しいことを検証し、異なる対象ユーザー向けのトークンをすべて拒否する必要があります。 | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | 認証のインスタント | |認証が行われた日時を記録します。 | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
> |認証方法 | `amr` |トークンのサブジェクトが認証された方法を示します。 | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |名 | `given_name` |Azure AD ユーザー オブジェクトに設定されたユーザーの名を示します。 | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |グループ | `groups` |サブジェクトのグループ メンバーシップを表すオブジェクト ID です。 これらの値は一意 (「オブジェクト ID」を参照) であり、アクセスの管理 (リソースへのアクセスを承認するなど) に安全に使用できます。 グループ クレームに含まれるグループは、アプリケーションごとに、アプリケーション マニフェストの ”groupMembershipClaims” プロパティを介して構成されます。 値が null の場合はすべてのグループが除外され、値が "SecurityGroup" の場合は Active Directory セキュリティ グループのメンバーシップのみが含まれ、値が ”All” の場合はセキュリティ グループと Microsoft 365 配布リストの両方が含まれます。 <br><br> **注**: <br> ユーザーが属するグループの数が制限 (SAML の場合は 150、JWT の場合は 200) を超える場合、超過分要求に、ユーザーのグループ リストを含む、Graph エンドポイントを指す要求ソースが追加されます | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | グループ超過分インジケーター | `groups:src1` | 長さは制限されていなくても、トークンには大きすぎるトークン要求の場合、ユーザーのすべてのグループ リストへのリンクが含まれます。 SAML の場合、これは `groups` 要求の代わりに新しい要求として追加されます。 | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |ID プロバイダー | `idp` |トークンのサブジェクトを認証した ID プロバイダーを記録します。 この値は、ユーザー アカウントが発行者とは異なるテナントにある場合を除いて、発行者クレームの値と同じです。 | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |IssuedAt | `iat` |トークンが発行された時刻が格納されます。 このクレームは、トークンの鮮度を測定するためによく使用されます。 | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |発行者 | `iss` |トークンを構築して返す Security Token Service (STS) を識別します。 Azure AD が返すトークンでは、発行者は sts.windows.net です。 発行者クレーム値の GUID は、Azure AD ディレクトリのテナント ID です。 テナント ID は、変更不可で信頼性の高いディレクトリの識別子です。 | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |姓 | `family_name` |Azure AD ユーザー オブジェクトで定義されたユーザーの姓や名字を示します。 | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |名前 | `unique_name` |トークンのサブジェクトを識別する、人が判読できる値を提供します。 この値は、テナント内で一意であるとは限らず、表示目的でのみ使用されます。 | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |Object ID | `oid` |Azure AD 内のオブジェクトの一意の識別子が含まれています。 この値は変更不可で、再割り当ても再利用もできません。 オブジェクト ID を使用すると、Azure AD へのクエリ内のオブジェクトを識別できます。 | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |ロール | `roles` |グループ メンバーシップを通じて直接的および間接的にサブジェクトに付与されており、ロールベースのアクセス制御を適用するために使用できるすべてのアプリケーション ロールを表します。 アプリケーション ロールは、アプリケーションごとに、アプリケーション マニフェストの `appRoles` プロパティを介して定義されます。 各アプリケーション ロールの `value` プロパティは、ロールの要求内に表示される値です。 | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |サブジェクト | `sub` |トークンが情報をアサートするプリンシパルを示します (アプリケーションのユーザーなど)。 この値は変更不可で、再割り当ても再利用もできません。したがってこの値を使用すると、安全に承認チェックができます。 サブジェクトは、Azure AD が発行するトークン内に常に存在するため、汎用性のある承認システムでこの値を使用することをお勧めします。 <br> `SubjectConfirmation` は要求ではありません。 これは、トークンのサブジェクトの検証方法を示します。 `Bearer` は、トークンを所有していることでサブジェクトが確認されることを示します。 | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |テナント ID | `tid` |トークンを発行したディレクトリ テナントを識別する、変更不可で、再利用できない識別子です。 この値を使用すると、マルチ テナント アプリケーションのテナント固有のディレクトリ リソースにアクセスできます。 たとえば、この値を使用すると、Graph API への呼び出しでテナントを識別できます。 | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |トークンの有効期間 | `nbf`, `exp` |トークンが有効である期間を定義します。 トークンを検証するサービスは、現在の日付がトークンの有効期間内にあることを確認し、有効期限内にない場合は、トークンを拒否する必要があります。 サービスでは、Azure AD とサービスの間のクロック タイムの違い (「時間のずれ」) を考慮して、トークンの有効期間の範囲を最大 5 分まで延長する場合があります。 | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>サンプル SAML トークン

以下に示すのは、一般的な SAML トークンのサンプルです。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
    <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
    </t:Lifetime>
    <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
            <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
    </wsp:AppliesTo>
    <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
            <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
            <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
                <ds:SignedInfo>
                    <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                    <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
                    <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                        <ds:Transforms>
                            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                        </ds:Transforms>
                        <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                        <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
                    </ds:Reference>
                </ds:SignedInfo>
                <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
                <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
                    <X509Data>
                        <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
                    </X509Data>
                </KeyInfo>
            </ds:Signature>
            <Subject>
                <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
                <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
            </Subject>
            <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
                <AudienceRestriction>
                    <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
                </AudienceRestriction>
            </Conditions>
            <AttributeStatement>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
                    <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
                    <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
                    <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
                    <AttributeValue>Admin</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
                    <AttributeValue>Sample</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
                    <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
                    <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
                    <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
                    <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
                    <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
                    <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
                    <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
                    <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
                    <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
                    <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
                    <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
                    <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
                    <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
                    <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
                </Attribute>
            </AttributeStatement>
            <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
                <AuthnContext>
                    <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
                </AuthnContext>
            </AuthnStatement>
        </Assertion>
    </t:RequestedSecurityToken>
    <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
            <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
    </t:RequestedAttachedReference>
    <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
            <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
    </t:RequestedUnattachedReference>
    <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
    <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
    <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
</t:RequestSecurityTokenResponse>
```

## <a name="next-steps"></a>次の手順

* Microsoft Graph API を使用してトークンの有効期間ポリシーを管理する方法の詳細については、[Azure AD ポリシー リソースの概要](/graph/api/resources/policy)に関するページを参照してください。
* [カスタムの要求と省略可能な要求](active-directory-optional-claims.md)をアプリケーションのトークンに追加します。
* [SAML で シングル サインオン (SSO)](single-sign-on-saml-protocol.md) を使用します。
* [Azure シングル サイン アウト SAML プロトコル](single-sign-out-saml-protocol.md) を使用します
