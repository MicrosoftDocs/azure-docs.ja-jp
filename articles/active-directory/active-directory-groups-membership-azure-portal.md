---
title: "Azure Active Directory プレビューで自分のグループが属するグループを管理する | Microsoft Docs"
description: "Azure Active Directory でグループに他のグループを含めることができます。 ここでは、これらのメンバーシップを管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e785c2d0-7724-47d4-a56e-c58280c08a14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 58768cd59a922483bcb37797a6dcd515d159ef4c
ms.openlocfilehash: 8e0563f3490186a19dd9418ebf7beac7dd8dd046
ms.lasthandoff: 03/01/2017


---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Azure Active Directory テナントでグループが属するグループを管理する
Azure Active Directory プレビューでグループに他のグループを含めることができます。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md) ここでは、これらのメンバーシップを管理する方法について説明します。

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>自分のグループが属するグループを見つける方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

   ![ユーザー管理を開く](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
3. **[ユーザーとグループ]** ブレードで、**[すべてのグループ]** を選択します。

   ![グループ ブレードを開く](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
4. **[ユーザーとグループ - すべてのグループ]** ブレードで、グループを選択します。
5. *[*グループ - *グループ名*]** ブレードで、**[グループ メンバーシップ]** を選択します。

   ![グループ メンバーシップ ブレードを開く](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
6. グループを別のグループのメンバーとして追加するには、**[グループ - グループ メンバーシップ]** ブレードで、**[追加]** をクリックします。
7. **[グループの選択]** ブレードでグループを選択し、ブレードの下部にある **[選択]** をクリックします。 グループは、一度に&1; つのグループにのみ追加できます。 **[ユーザー]** ボックスでは、入力内容とユーザー名またはデバイス名の一部との一致に基づいて表示がフィルター処理されます。 このボックスではワイルドカード文字は使用できません。

   ![グループ メンバーシップを追加する](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. 別のグループのメンバーであるグループを削除するには、 **[グループ - グループ メンバーシップ]** ブレードでグループを選択します。
9. ***[グループ名]*** ブレードで **[削除]** をクリックし、表示されたメッセージで削除を確定します。

   ![メンバーシップの [削除] コマンド](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. グループのグループ メンバーシップの変更が完了したら、 **[保存]**をクリックします。

## <a name="additional-information"></a>追加情報
次の記事は、Azure Active Directory に関する追加情報を示します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [新しいグループの作成とメンバーの追加](active-directory-groups-create-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](active-directory-groups-members-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)

