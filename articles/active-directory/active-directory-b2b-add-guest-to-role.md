---

title: "Azure Active Directory B2B コラボレーション ユーザーをロールに追加する | Microsoft Docs"
description: "Azure Active Directory でゲスト ユーザーをロールに追加します"
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
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e816349ea971c997f655b4d51672dba666bc3e89
ms.lasthandoff: 03/17/2017


---

# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Azure Active Directory テナントでパートナー組織からユーザーにアクセス許可を付与する

Azure Active Directory (Azure AD) B2B コラボレーション ユーザーはゲスト ユーザーとしてディレクトリに追加され、ディレクトリのゲスト アクセス許可は既定で制限されています。 一部のゲスト ユーザーを組織内の上位の特権ロールに入れる必要がある場合があります。 上位の特権ロールの定義をサポートするために、ゲスト ユーザーを組織のニーズに合わせて任意のロールに追加することができます。

## <a name="default-role"></a>既定のロール

![既定のロール](./media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>全体管理者ロール

![全体管理者ロール](./media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>制限付き管理者ロール

![制限付き管理者ロール](./media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)

