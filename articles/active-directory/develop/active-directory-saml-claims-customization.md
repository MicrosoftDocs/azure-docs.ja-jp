---
title: "Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ | Microsoft Docs"
description: "Azure Active Directory において事前に統合されたアプリの SAML トークンで発行された要求をカスタマイズする方法を学習します"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: e9ab491639485950b17de4be190b6797c1660530


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ
現在 Azure Active Directory は、SAML 2.0 プロトコルを使用したシングル サインオンをサポートする 150 以上のアプリケーションを含め、何千もの事前統合済みのアプリケーションを Azure AD アプリケーション ギャラリーでサポートしています。 SAML を使用し、Azure AD を介してアプリケーションを認証する場合、Azure AD は、(HTTP 302 リダイレクトを介して) アプリケーションにトークンを送信します。次に、アプリケーションはトークンを検証し、ユーザー名とパスワードを要求する代わりにトークンを使用してユーザーをログインさせます。 これらの SAML トークンには、「要求」と呼ばれる、ユーザーに関する情報が含まれています。

ID 用語で「要求」とは、そのユーザーに発行するトークンの中にあるユーザーに関する ID プロバイダーが提示した情報を指します。 [SAML トークン](http://en.wikipedia.org/wiki/SAML_2.0)では、このデータは通常 SAML 属性ステートメントに含まれています。また、ユーザーの一意の ID は通常、SAML サブジェクトで表されます。

既定では、Azure AD は、NameIdentifier 要求、Azure AD でのユーザー名の値 (この値は、ユーザーを一意に識別します) を含む、SAML トークンを発行します。 また、SAML トークンには、ユーザーの電子メール アドレス、姓名を含むその他の要求も含まれています。

SAML トークンで発行された要求を表示し、編集するには、Microsoft Azure 管理ポータルでアプリケーション レコードを開き、アプリケーションの下にある **[属性]** タブを選択します。

![][1]

SAML トークンで発行された要求の編集が必要になる場合がある理由として 2 つが考えられます。1 つは、異なる要求 URL または要求値のセットを必要とするようにアプリケーションが作成されているという理由です。もう 1 つは、Azure Active Directory に格納されているユーザー名 (ユーザー プリンシパル名ともいう) 以外の名前にするために NameIdentifier 要求を必要とする方法でアプリケーションがデプロイされているという理由です。 

SAML トークンの属性テーブル内の行のいずれかにマウスを置いた際に、右側に表示される、鉛筆の形のアイコンを選択して、既定の要求値のいずれかを編集できます。 また、**[X]** アイコンを使用することで、(NameIdentifier 以外の) 要求を削除できます。さらに、**[ユーザー属性の追加]** ボタンを使用することで、新しい要求を追加できます。

## <a name="editing-the-nameidentifier-claim"></a>NameIdentifier 要求の編集
別のユーザー名を使用して、デプロイされたアプリケーションの問題を解決するには、NameIdentifier 要求の横にある鉛筆アイコンをクリックします。 これにより、次のいくつかのオプションを示すダイアログ ボックスが表示されます。

![][2]

**[属性値]** メニューで、**[user.mail]** を選択し、ディレクトリで NameIdentifier 要求がユーザーの電子メール アドレスになるように設定するか、**[user.onpremisessamaccountname]** を選択し、オンプレミスの Azure AD から同期されたユーザーの SAM アカウント名に設定します。 

また、特殊な ExtractMailPrefix() 関数を使用して、電子メール アドレスまたはユーザー プリンシパル名のいずれかからドメイン サフィックスを削除することもできます。これにより、渡されたユーザー名の最初の部分のみになります (例: joesmith@contoso.com) の代わりに "joesmith" となります)。

![][3]

## <a name="adding-claims"></a>要求の追加
新しい要求を追加する場合は、ディレクトリに格納されているユーザーの属性に値を設定するのと同様に、属性名 (SAML 仕様のように厳密に URI パターンに従う必要はありません) を指定できます。

![][4]

たとえば、組織でユーザーが所属する部門 (例: 営業) を要求として送信する必要がある場合、アプリケーションで求められる要求値を入力し、 **[user.department]** を値として選択できます。

特定のユーザーに対し、選択された属性に格納されている値がない場合は、トークンでその要求は発行されません。

**注**: **user.onpremisesecurityidentifier** と **user.onpremisesamaccountname** は、[Azure AD Connect](../active-directory-aadconnect.md) ツールを使用してオンプレミスの Active Directory のユーザー データを同期する場合にのみサポートされます。

## <a name="related-articles"></a>関連記事
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](../active-directory-apps-index.md)
* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../active-directory-saas-custom-apps.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png



<!--HONumber=Jan17_HO3-->


