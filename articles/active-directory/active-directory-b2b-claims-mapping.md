---
title: "Azure Active Directory での B2B コラボレーション ユーザーの要求マッピング | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションの要求マッピング リファレンス"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.lasthandoff: 03/17/2017


---

# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Azure Active Directory での B2B コラボレーション ユーザーの要求マッピング

Azure Active Directory (Azure AD) では、B2B コラボレーション ユーザーの SAML トークンで発行される要求のカスタマイズがサポートされています。 アプリケーションに対するユーザーの認証時に、Azure AD は、ユーザーを一意に識別する情報 (要求) を含む SAML トークンをアプリに発行します。 既定では、ユーザーのユーザー名、電子メール アドレス、名、および姓が含まれます。 [属性] タブで、アプリケーションに SAML トークンで送信された要求を表示または編集できます。

SAML トークンで発行された要求を編集する必要がある理由は、2 つ考えられます。

1. アプリケーションが、別の要求 URI または要求値のセットを必要とするように記述されている。

2. アプリケーションで NameIdentifier 要求が Azure Active Directory に格納されているユーザー プリンシパル名以外のものである必要がある。

  ![SAML トークンの要求の表示](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

要求を追加および編集する方法については、要求のカスタマイズについての記事である「[Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ](develop/active-directory-saml-claims-customization.md)」を参照してください。 B2B コラボレーション ユーザーの場合は、セキュリティ上の理由で、NameID と UPN クロステナントとのマッピングは許可されていません。


## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)

