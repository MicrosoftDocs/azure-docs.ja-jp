---
title: Azure AD の UserPrincipalName の設定
description: 次のドキュメントでは、UserPrincipalName 属性がどのように設定されるかについて説明します。
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b4bbcac110398ee4ff132b76ce8c4868ee17f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317593"
---
# <a name="azure-ad-userprincipalname-population"></a>Azure AD の UserPrincipalName の設定

この記事では、Azure Active Directory (Azure AD) で UserPrincipalName 属性がどのように設定されるかについて説明します。
UserPrincipalName 属性には、該当するユーザー アカウントの Azure AD ユーザー名が値として使用されます。

## <a name="upn-terminology"></a>UPN の用語
この記事では、次の用語を使用します。

|期間|説明|
|-----|-----|
|初期ドメイン|Azure AD テナントの既定のドメイン (onmicrosoft.com)。 たとえば、contoso.onmicrosoft.com です。|
|Microsoft Online Email Routing Address (MOERA)|MOERA は、Azure AD の MailNickName 属性と Azure AD の初期ドメインから &lt;MailNickName&gt;&#64;&lt;initial domain&gt; として、Azure AD によって計算されます。|
|オンプレミスの mailNickName 属性|Active Directory 内の属性。Exchange 組織におけるユーザーの別名は、この属性の値によって表されます。|
|オンプレミスの mail 属性|Active Directory 内の属性。ユーザーのメール アドレスは、この属性の値によって表されます。|
|プライマリ SMTP アドレス|Exchange 受信者オブジェクトの通常のメール アドレス。 たとえば、SMTP:user\@contoso.com。|
|代替ログイン ID|UserPrincipalName 以外でサインインに使用されるオンプレミスの属性 (mail 属性など)。|

## <a name="what-is-userprincipalname"></a>UserPrincipalName とは
UserPrincipalName は、インターネット標準 [RFC 822](https://www.ietf.org/rfc/rfc0822.txt) に基づく属性で、ユーザーのインターネット形式のログイン名となります。 

### <a name="upn-format"></a>UPN の形式
UPN は、UPN プレフィックス (ユーザー アカウント名) と UPN サフィックス (DNS ドメイン名) とから成ります。 プレフィックスとサフィックスは、"\@" 記号を使用して結合されます。 たとえば、"someone\@example.com" です。 UPN は、ディレクトリ フォレスト内のすべてのセキュリティ プリンシパル オブジェクトの中で一意であることが必要です。 

## <a name="upn-in-azure-ad"></a>Azure AD における UPN 
Azure AD では、ユーザーにサインインを許可するために UPN が使用されます。  ユーザーが使用できる UPN は、ドメインが検証済みであるかどうかによって異なります。  ドメインが検証済みである場合、そのサフィックスを含んだユーザーは、Azure AD にサインインすることができます。  

この属性は、Azure AD Connect によって同期されます。  検証済みのドメインとそうでないドメインは、インストール中に確認することができます。

   ![未検証のドメイン](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>代替ログイン ID
環境によっては、エンド ユーザーは電子メール アドレスのみを認識し、UPN を認識していない場合があります。  電子メール アドレスの使用は、企業のポリシーまたはオンプレミス基幹業務アプリの依存関係によって求められることがあります。

代替ログイン ID を使用すると、ユーザーがメールなどの UPN 以外の属性でサインイン可能になるサインイン エクスペリエンスを構成できます。

Azure AD Connect を使用している場合、Azure AD との間で代替ログイン ID を有効にするための特別な構成手順は不要です。 代替 ID は、ウィザードから直接構成することができます。 ユーザーの Azure AD サインイン構成は、[同期] セクションに表示されます。 **[ユーザー プリンシパル名]** ボックスの一覧で、代替ログイン ID の属性を選択してください。

![ユーザープリンシパル名の一覧が強調表示されているスクリーンショット。ここでは、代替ログイン ID 属性を選択できます。](./media/plan-connect-userprincipalname/altloginid.png)  

詳細については、[代替ログイン ID の構成](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)に関するページおよび「[Azure AD サインインの構成](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)」を参照してください。

## <a name="non-verified-upn-suffix"></a>未検証の UPN サフィックス
Azure AD テナントに関して、オンプレミスの UserPrincipalName 属性/代替ログイン ID のサフィックスが検証されていない場合、Azure AD の UserPrincipalName 属性の値が MOERA に設定されます。 MOERA は、Azure AD の MailNickName 属性と Azure AD の初期ドメインから &lt;MailNickName&gt;&#64;&lt;initial domain&gt; として、Azure AD によって計算されます。

## <a name="verified-upn-suffix"></a>検証済みの UPN サフィックス
Azure AD テナントに関して、オンプレミスの UserPrincipalName 属性/代替ログイン ID のサフィックスが検証されている場合、Azure AD の UserPrincipalName 属性の値は、オンプレミスの UserPrincipalName 属性/代替ログイン ID と同じ値になります。

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD の MailNickName 属性値の計算
Azure AD の UserPrincipalName 属性の値が MOERA に設定される可能性があるので、Azure AD の MailNickName 属性の値 (MOERA プレフィックス) がどのように計算されるかを理解しておくことが大切です。

ユーザー オブジェクトが Azure AD テナントに対して初めて同期されるときに、Azure AD は、以下の項目を上から順に調べ、最初に見つかった項目を MailNickName 属性の値に設定します。

- オンプレミスの mailNickName 属性
- プライマリ SMTP アドレスのプレフィックス
- オンプレミスの mail 属性のプレフィックス
- オンプレミスの userPrincipalName 属性/代替ログイン ID のプレフィックス
- セカンダリ SMTP アドレスのプレフィックス

ユーザー オブジェクトに対する更新が Azure AD テナントとの間で同期される際、MailNickName 属性の値が Azure AD によって更新されるのは、オンプレミスの mailNickName 属性の値が更新されていた場合だけです。

>[!IMPORTANT]
>オンプレミスの UserPrincipalName 属性/代替ログイン ID の値に対する更新が Azure AD テナントとの間で同期された場合にのみ、UserPrincipalName 属性の値が Azure AD によって再計算されます。 
>
>Azure AD が UserPrincipalName 属性を再計算するときは常に MOERA も再計算されます。 

## <a name="upn-scenarios"></a>UPN のシナリオ
以降、UPN がどのように計算されるかの例を、設定したシナリオごとに紹介します。

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>シナリオ 1:未検証の UPN サフィックス - 初期同期

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

オンプレミス ユーザー オブジェクト:
- mailNickName      : &lt;未設定&gt;
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us3@contoso.com

Azure AD テナントとの間でユーザー オブジェクトを初回同期
- Azure AD の MailNickName 属性をプライマリ SMTP アドレスのプレフィックスに設定します。
- MOERA を  &lt;MailNickName&gt;&#64;&lt;initial domain&gt; に設定します。
- Azure AD の UserPrincipalName 属性を MOERA に設定します。

Azure AD テナントのユーザー オブジェクト:
- MailNickName      : us1           
- UserPrincipalName : us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>シナリオ 2: 未検証の UPN サフィックス - オンプレミスの mailNickName 属性を設定

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

オンプレミス ユーザー オブジェクト:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us3@contoso.com

オンプレミスの mailNickName 属性に対する更新を Azure AD テナントとの間で同期
- Azure AD の MailNickName 属性をオンプレミスの mailNickName 属性で更新します。
- オンプレミスの userPrincipalName 属性が更新されていないので、Azure AD UserPrincipalName 属性への変更はありません。

Azure AD テナントのユーザー オブジェクト:
- MailNickName      : us4
- UserPrincipalName : us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>シナリオ 3: 未検証の UPN サフィックス - オンプレミスの userPrincipalName 属性を更新

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

オンプレミス ユーザー オブジェクト:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us5@contoso.com

オンプレミスの userPrincipalName 属性に対する更新を Azure AD テナントとの間で同期
- オンプレミスの userPrincipalName 属性を更新すると、MOERA と Azure AD の UserPrincipalName 属性の再計算がトリガーされます。
- MOERA を &lt;MailNickName&gt;&#64;&lt;initial domain&gt; に設定します。
- Azure AD の UserPrincipalName 属性を MOERA に設定します。

Azure AD テナントのユーザー オブジェクト:
- MailNickName      : us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>シナリオ 4: 未検証の UPN サフィックス - プライマリ SMTP アドレスとオンプレミスの mail 属性を更新

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

オンプレミス ユーザー オブジェクト:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- mail          : us7@contoso.com
- userPrincipalName : us5@contoso.com

オンプレミスの mail 属性およびプライマリ SMTP アドレスに対する更新を Azure AD テナントとの間で同期
- ユーザー オブジェクトの初期同期後、オンプレミスの mail 属性とプライマリ SMTP アドレスを更新しても、Azure AD の MailNickName 属性と UserPrincipalName 属性はどちらも変更されません。

Azure AD テナントのユーザー オブジェクト:
- MailNickName      : us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>シナリオ 5: 検証済みの UPN サフィックス - オンプレミスの userPrincipalName 属性のサフィックスを更新

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

オンプレミス ユーザー オブジェクト:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- mail          : us7@contoso.com
- userPrincipalName : us5@verified.contoso.com

オンプレミスの userPrincipalName 属性に対する更新を Azure AD テナントとの間で同期
- オンプレミスの userPrincipalName 属性を更新すると、Azure AD の UserPrincipalName 属性の再計算がトリガーされます。
- Azure AD テナントに関して UPN サフィックスが検証されているため、Azure AD の UserPrincipalName 属性をオンプレミスの userPrincipalName 属性に設定します。

Azure AD テナントのユーザー オブジェクト:
- MailNickName      : us4     
- UserPrincipalName : us5@verified.contoso.com

## <a name="next-steps"></a>次の手順
- [オンプレミスのディレクトリと Azure Active Directory の統合](whatis-hybrid-identity.md)
- [Azure AD Connect のカスタム インストール](how-to-connect-install-custom.md)