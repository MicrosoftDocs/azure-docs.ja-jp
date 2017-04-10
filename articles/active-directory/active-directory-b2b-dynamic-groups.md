---
title: "動的グループと Azure Active Directory B2B コラボレーション | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションは、Azure AD の動的グループと共に使用することができます"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: a483e219c1e730e174baa0ffa8ba471b0724f0da


---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>動的グループと Azure Active Directory B2B コラボレーション

## <a name="what-are-dynamic-groups"></a>動的グループとは
Azure Active Directory (Azure AD) のセキュリティ グループ メンバーシップの動的な構成は、[Azure Portal](https://portal.azure.com) で、パブリック プレビューで利用できます。 管理者は、ユーザー属性 (userType、部門、国など) に基づいて、Azure Active Directory で作成されたグループのメンバーを設定するためのルールを指定できます。 このルールにより、メンバーは属性の変化に基づいて自動的にセキュリティ グループに追加されたり、セキュリティ グループから削除されたりします。 これらのグループを使用すると、アプリケーションやクラウド リソース (SharePoint サイトやドキュメントなど) へのアクセスを付与したり、メンバーにライセンスを割り当てたりすることができます。 動的なグループの詳細については、「[Azure Active Directory の専用グループ](active-directory-accessmanagement-dedicated-groups.md)」を参照してください。

AAD Premium P1 または P2 サブスクリプションでは、Azure Portal で Azure Active Directory プレビュー グループに対してより複雑な属性ベースの動的メンバーシップを利用できる高度なルールを作成できるようになりました。 高度なルールの作成の詳細については、「[属性を使用して Azure Active Directory プレビューのグループ メンバーシップの高度なルールを作成する](active-directory-groups-dynamic-membership-azure-portal.md)」を参照してください。

## <a name="what-are-the-built-in-dynamic-groups"></a>組み込みの動的グループとは
**[すべてのユーザー]** 動的グループを使用すると、テナント管理者は、テナントのすべてのユーザーを含むグループをワンクリックで作成できます。 既定では、**[すべてのユーザー]** グループには、メンバーとゲストを含む、ディレクトリ内のすべてのユーザーが含まれます。
新しい Azure Active Directory 管理ポータルの [グループ設定] ビューで、**[すべてのユーザー]** グループを有効にすることができます。

![組み込みグループ](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

[すべてのユーザー] 動的グループを強化できます。既定では、**[すべてのユーザー]** グループには B2B コラボレーション (ゲスト) ユーザーも含まれます。 ゲスト ユーザーを削除して、**[すべてのユーザー]** グループのセキュリティを強化する場合、操作は簡単です。**[すべてのユーザー]** グループの属性ベースのルールを変更するだけです。 次の図は、ゲストを除外するように変更された **[すべてのユーザー]** グループを示しています。

![すべてのユーザー グループを有効にする](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

ゲスト ユーザーのみが含まれている新しい動的グループを作成すると便利な場合もあります。 このようなグループは、ポリシー (条件付きアクセス ポリシーなど) でゲスト ユーザーを対象にする場合に非常に便利です。
次の図は、そのようなグループがどのようなものかを示しています。

![ゲスト ユーザーを除外する](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


