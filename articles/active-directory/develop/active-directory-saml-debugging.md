---
title: "Azure Active Directory のアプリケーションに対す SAML に基づいたシングル サインオンをデバッグする方法 | Microsoft Docs"
description: "Azure Active Directory のアプリケーションに対す SAML に基づいたシングル サインオンをデバッグする方法について説明します  "
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: 245c2de65ca771cdc0e6371010246725c407ae11


---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法
SAML ベースのアプリケーション統合をデバッグするときは、多くの場合、 [Fiddler](http://www.telerik.com/fiddler) などのツールを使用して、SAML 要求、SAML 応答、およびアプリケーションに対して発行された実際の SAML トークンを確認すると便利です。 SAML トークンを確認することで、必要な属性、SAML サブジェクト内のユーザー名、および発行者 URI のすべてが予想どおりに送信されたことを確認できます。

![][1]

SAML トークンを含む Azure AD からの応答は、通常は、https://login.windows.net からの HTTP 302 リダイレクトの後に発生する応答であり、アプリケーションの構成済みの**応答 URL** に送信されます。 

SAML トークンは、行を選択し、右側のパネルで **[インスペクター]、[WebForms]** の順に選択することで表示できます。 そこから、**SAMLResponse** 値を右クリックし、**[TextWizard に送信]** を選択します。 次に、**[変換]** メニューの **[Base64 から]** を選択してトークンをデコードし、その内容を確認します。

**注**: この HTTP 要求の内容を Fiddler で表示するには、HTTPS トラフィックの暗号化解除を構成することを求めるプロンプトが表示される可能性があります。この場合は、構成を実行する必要があります。

## <a name="related-articles"></a>関連記事
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](../active-directory-apps-index.md)
* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../active-directory-saas-custom-apps.md)
* [Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png



<!--HONumber=Jan17_HO3-->


