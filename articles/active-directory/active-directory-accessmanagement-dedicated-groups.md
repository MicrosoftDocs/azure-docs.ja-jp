---
title: "Azure Active Directory の専用グループ | Microsoft Docs"
description: "Azure Active Directory の専用グループの機能と作成方法の概要について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 86158909-083a-41fe-8090-955e96ad1865
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: a4a78b92c8bb9e8aff25fd724ed78283de8f2fd8
ms.openlocfilehash: 92b9c88ec49424c96c3bd21bc5c4ce390352c17b


---
# <a name="dedicated-groups-in-azure-active-directory"></a>Azure Active Directory の専用グループ
Azure Active Directory (Azure AD) では、専用グループ機能によって Azure AD 事前定義済みグループのメンバーシップが自動的に作成および設定されます。 専用グループのメンバーは、Azure クラシック ポータル、Windows PowerShell コマンドレット、プログラムのいずれの方法でも追加したり削除したりすることはできません。

> [!NOTE]
> 専用グループを使用するには、次のユーザーに Azure AD Premium ライセンスが割り当てられている必要があります。
>
> * グループに対するルールを管理する管理者
> * ルールによってグループのメンバーとして選択されるすべてのユーザー
>
>

**専用グループを有効にするには**

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で **[Active Directory]**を選択し、該当する組織のディレクトリを開きます。
2. **[グループ]** タブを選択し、編集するグループを開きます。
3. **[構成]** タブを選択し、**[専用グループの有効化]** を **[はい]** に設定します。

[専用グループの有効化] を **[はい]** に設定した後、**["すべてのユーザー" グループの有効化]** を **[はい]** に設定することで、"すべてのユーザー" 専用グループをディレクトリで自動的に作成できるようになります。 その後、 **["すべてのユーザー" グループの表示名]** で、この専用グループの名前を編集できます。

All Users グループは、ディレクトリ内のすべてのユーザーに同じアクセス許可を割り当てる場合に使用できます。 たとえば、ディレクトリ内のすべてのユーザーに SaaS アプリケーションへのアクセス権を与えるには、All Users 専用グループに対してこのアプリケーションへのアクセス権を割り当てます。

All Users 専用グループには、ゲストや外部ユーザーを含む、ディレクトリ内のすべてのユーザーが含まれます。 外部ユーザーを除外するグループが必要な場合、次のような属性ベースの動的ルールでグループを作成することによって、これを実現できます。

                (user.userPrincipalName -notContains "#EXT#@")

すべてのゲストを除外するグループの場合は、次のようなルールを使用します。

                (user.userType -ne "Guest")

動的グループのメンバーシップ管理を目的とした *高度* なルール (複数の比較を伴うルール) を作成する方法については、「 [属性を使用した高度なルールの作成](active-directory-accessmanagement-groups-with-advanced-rules.md)」を参照してください。

### <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory とは](active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)



<!--HONumber=Feb17_HO2-->


