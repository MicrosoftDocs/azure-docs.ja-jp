---
title: "Azure Active Directory プレビューでグループのメンバーを管理する | Microsoft Docs"
description: "Azure Active Directory でグループのユーザーとデバイスを追加または削除する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d399a97d-fd2a-4b2d-b73d-0975db83f41b
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
ms.openlocfilehash: 3373af848720c7c04e679d7fd4b075c5571fb417
ms.lasthandoff: 03/01/2017


---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Azure Active Directory テナントでユーザーのグループ メンバーシップを管理する
この記事では、Azure Active Directory (Azure AD) プレビューでグループのメンバーを管理する方法について説明します。 プレビューの機能については、[こちらの記事](active-directory-preview-explainer.md)をご覧ください。

## <a name="how-do-i-find-the-members-and-manage-them"></a>メンバーを検索して管理する方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

   ![ユーザー管理を開く](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. **[ユーザーとグループ]** ブレードで、**[すべてのグループ]** を選択します。

   ![グループ ブレードを開く](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. **[ユーザーとグループ - すべてのグループ]** ブレードで、グループを選択します。
5. **[グループ - *グループ名*]** ブレードで、**メンバー** を選択します。

   ![[メンバー] ブレードを開く](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. グループにメンバーを追加するには、**[グループ - メンバー]** ブレードで、**[メンバーの追加]** をクリックします。

   ![[メンバーの追加] コマンド](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. **[メンバー]** ブレードで、グループに追加する&1; 人以上のユーザーまたは&1; つ以上のデバイスを選択し、ブレードの下部にある **[選択]** をクリックしてグループに追加します。 **[ユーザー]** ボックスでは、入力内容とユーザー名またはデバイス名の一部との一致に基づいて表示がフィルター処理されます。 このボックスではワイルドカード文字は使用できません。
8. グループからメンバーを削除するには、 **[グループ - メンバー]** ブレードで、メンバーを選択します。
9. ***[メンバー名]*** ブレードで **[削除]** をクリックし、表示されたメッセージで削除を確定します。

   ![メンバーの [削除] コマンド](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. グループのメンバーの変更が完了したら、 **[保存]**をクリックします。

## <a name="additional-information"></a>追加情報
次の記事は、Azure Active Directory に関する追加情報を示します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [新しいグループの作成とメンバーの追加](active-directory-groups-create-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)

