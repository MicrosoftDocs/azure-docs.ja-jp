---
title: "Azure Active Directory プレビューでユーザー グループを作成する | Microsoft Docs"
description: "Azure Active Directory でグループを作成し、メンバーをそのグループに追加する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
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
ms.openlocfilehash: 4b85a754dfa9632f27308a3d9234213533dea4c4
ms.lasthandoff: 03/01/2017


---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Azure Active Directory でグループを作成し、メンバーを追加する
> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-groups-create-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

この記事では、Azure Active Directory (Azure AD) プレビューで新しいグループを作成して設定する方法について説明します。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md) グループを使用して管理タスクを実行します。たとえば、複数のユーザーやデバイスにライセンスまたはアクセス許可を一度に割り当てることができます。

## <a name="how-do-i-create-a-group"></a>どのようにしてグループを作成しますか?
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[More services (その他のサービス)]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

   ![ユーザー管理を開く](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. **[ユーザーとグループ]** ブレードで、**[すべてのグループ]** を選択します。

   ![グループ ブレードを開く](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. **[Users and groups - All groups (ユーザーとグループ - すべてのグループ)]** ブレードで、**[追加]** をクリックします。

   ![[追加] をクリックする](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. **[グループ]** ブレードで、グループの名前と説明を追加します。
6. グループに追加するメンバーを選択するには、**[メンバーシップの種類]** ボックスで **[割り当て済み]** を選択し、**[メンバー]** を選択します。 グループのメンバーシップを動的に管理する方法の詳細については、 [属性を使用したグループ メンバーシップの高度なルールの作成](active-directory-groups-dynamic-membership-azure-portal.md)を参照してください。

   ![追加するメンバーを選択する](./media/active-directory-groups-create-azure-portal/select-members.png)
7. **[メンバー]** ブレードで、グループに追加する&1; 人以上のユーザーまたは&1; つ以上のデバイスを選択し、ブレードの下部にある **[選択]** をクリックしてグループに追加します。 **[ユーザー]** ボックスでは、入力内容とユーザー名またはデバイス名の一部との一致に基づいて表示がフィルター処理されます。 このボックスではワイルドカード文字は使用できません。
8. グループへのメンバーの追加が完了したら、**[グループ]** ブレードの **[作成]** をクリックします。    

   ![グループの作成の確認](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)

## <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory に関する追加情報を示します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](active-directory-groups-members-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)

