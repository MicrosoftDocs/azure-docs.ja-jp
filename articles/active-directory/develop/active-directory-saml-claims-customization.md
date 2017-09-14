---
title: "Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ | Microsoft Docs"
description: "Azure Active Directory において事前に統合されたアプリの SAML トークンで発行された要求をカスタマイズする方法について説明します"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 6d232759630fcc567788a8326b566b659f89d17a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ
現在 Azure Active Directory は、SAML 2.0 プロトコルを使ったシングル サインオンをサポートする 360 以上のアプリケーションを含め、何千もの事前統合済みのアプリケーションを Azure AD アプリケーション ギャラリーでサポートしています。 ユーザーが Azure AD によって SAML を使ってアプリケーションに対して認証されると、Azure AD は、アプリケーションにトークンを送信します (HTTP POST 経由)。 その後、アプリケーションがトークンを検証し、ユーザー名とパスワードの入力を求める代わりに、検証済みのトークンを使用してユーザーをログオンします。 これらの SAML トークンには、「要求」と呼ばれる、ユーザーに関する情報が含まれています。

ID 用語で「要求」とは、そのユーザーに発行するトークンの中にあるユーザーに関する ID プロバイダーが提示した情報を指します。 [SAML トークン](http://en.wikipedia.org/wiki/SAML_2.0)では、通常、このデータは SAML 属性ステートメントに含まれています。 ユーザーの一意の ID は通常、名前識別子とも呼ばれる SAML サブジェクトで表されます。

既定では、Azure Active Directory は、アプリケーションに対して、Azure AD でのユーザーのユーザー名 (ユーザー プリンシパル名とも呼ばれます) を値に持つ NameIdentifier 要求を含む SAML トークンを発行します。 この値によって、ユーザーを一意に識別できます。 また、SAML トークンには、ユーザーの電子メール アドレス、姓名を含むその他の要求も含まれています。

アプリケーションに対して SAML トークンで発行された要求を表示または編集するには、Azure Portal でアプリケーションを開きます。 その後、アプリケーションの **[ユーザー属性]** セクションの **[その他のすべてのユーザー属性を表示および編集する]** チェック ボックスをオンにします。

![[ユーザー属性] セクション][1]

SAML トークンで発行された要求を編集する必要がある理由は、2 つ考えられます。
* アプリケーションが、別の要求 URI または要求値のセットを必要とするように記述されている。
* NameIdentifier 要求が、Azure Active Directory に格納されているユーザー名 (ユーザー プリンシパル名) 以外の何かであることを必要とする方法で、アプリケーションがデプロイされている。

既定の要求値のすべてを編集できます。 SAML トークン属性のテーブルで要求の行を選びます。 **[属性の編集]** セクションが開き、要求の名前、値、および要求に関連付けられている名前空間を編集できます。

![ユーザー属性の編集][2]

コンテキスト メニューを使って、要求 (NameIdentifier 以外) を削除することもできます。メニューを開くには、**[...]** アイコンをクリックします。  **[属性の追加]** ボタンを使って、新しい要求を追加することもできます。

![ユーザー属性の編集][3]

## <a name="editing-the-nameidentifier-claim"></a>NameIdentifier 要求の編集
異なるユーザー名を使ってデプロイされたアプリケーションの問題を解決するには、**[ユーザー属性]** セクションの **[ユーザー識別子]** ドロップダウンをクリックします。 この操作によって、複数のオプションがある次のダイアログが表示されます。

![ユーザー属性の編集][4]

ドロップダウンで **[user.mail]** を選んで、NameIdentifier 要求をディレクトリ内のユーザーのメール アドレスに設定します。 オンプレミスの Azure AD から同期されているユーザーの SAM アカウント名に設定する場合は、**[user.onpremisessamaccountname]** を選びます。

特殊な **ExtractMailPrefix()** 関数を使って、メール アドレス、SAM アカウント名、またはユーザー プリンシパル名から、ドメイン サフィックスを削除することもできます。 これにより、渡されたユーザー名の最初の部分のみが抽出されます (例: joe_smith@contoso.com ではなく "joe_smith" のみ)。

![ユーザー属性の編集][5]

ユーザー識別子の値で検証済みのドメインに参加するための **join()** 関数も追加されています。 **[ユーザー識別子]** で join() 関数を選ぶときは、最初にメール アドレスやユーザー プリンシパル名などのユーザー識別子を選び、2 番目のドロップダウン リストで検証済みのドメインを選びます。 検証済みドメインでメール アドレスを選ぶと、Azure AD は最初の値からユーザー名を抽出し (joe_smith@contoso.com から joe_smith)、それに contoso.onmicrosoft.com を追加します。 次の例を参照してください。

![ユーザー属性の編集][6]

## <a name="adding-claims"></a>要求の追加
要求を追加する場合は、属性の名前を指定できます (SAML 仕様とは異なり、URI パターンに厳密に従う必要はありません)。 ユーザー属性には、ディレクトリに格納されている値を設定します。

![ユーザー属性の追加][7]

たとえば、ユーザーが所属している組織の部署 (営業部など) を要求として送信する必要があるとします。 アプリケーションで予期されているように要求名を入力し、値として **[user.department]** を選びます。

> [!NOTE]
> 特定のユーザーに対し、選択された属性に格納されている値がない場合、その要求はトークンで発行されません。

> [!TIP]
> **user.onpremisesecurityidentifier** と **user.onpremisesamaccountname** は、[Azure AD Connect ツール](../active-directory-aadconnect.md)を使ってオンプレミスの Active Directory のユーザー データを同期する場合にのみサポートされます。

## <a name="restricted-claims"></a>制限付き要求

SAML には制限付きの要求がいくつかあります。 これらの要求を追加すると、Azure AD は要求を送信しません。 SAML の制限付き要求は次のとおりです。

    | 要求の種類 (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>次のステップ
* [Article Index for Application Management in Azure Active Directory](../active-directory-apps-index.md)
* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../active-directory-saas-custom-apps.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
