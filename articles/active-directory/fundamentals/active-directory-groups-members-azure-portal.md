---
title: グループ メンバーを追加または削除する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用してグループに対してメンバーを追加または削除する方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dafdf4c67e8d3d74109b3879fb0deacd79b1774
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182240"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Azure Active Directory を使用してグループのメンバーを追加または削除する
Azure Active Directory を使用して、グループのメンバーの追加と削除を続けることができます。

## <a name="to-add-group-members"></a>グループのメンバーを追加するには

1. ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、**[グループ]** を選択します。

3. **[グループ - すべてのグループ]** ページで、メンバーを追加するグループを検索して選択します。 ここでは、以前に作成したグループ **MDM policy - West** を使用します。

    ![[グループ - すべてのグループ] ページ、グループ名が強調表示](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. **[MDM policy - West の概要]** ページで、**[管理]** 領域から **[メンバー]** を選択します。

    ![[MDM policy - West の概要] ページ、[メンバー] オプションが強調表示](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. **[メンバーの追加]** を選択し、グループに追加する各メンバーを検索して選択したら、**[選択]** を選択します。

    メンバーが正常に追加されたことを示すメッセージが表示されます。

    ![検索したメンバーが表示されたメンバーの追加ページ](media/active-directory-groups-members-azure-portal/update-members.png)

6. グループに追加したすべてのメンバー名を表示するには画面を更新します。

## <a name="to-remove-group-members"></a>グループのメンバーを削除するには

1. **[グループ - すべてのグループ]** ページで、メンバーを削除するグループを検索して選択します。 ここでも **MDM policy - West** を使用します。

2. **[管理]** 領域の **[メンバー]** を選択し、削除するメンバーの名前を検索して選択したら、**[削除]** を選択します。

    ![メンバー情報ページの [削除] オプション](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>次の手順

- [グループとメンバーを表示する](active-directory-groups-view-azure-portal.md)

- [グループの設定を編集する](active-directory-groups-settings-azure-portal.md)

- [グループを使用したリソースへのアクセス管理](active-directory-manage-groups.md)

- [グループ内のユーザーの動的ルールの管理](../users-groups-roles/groups-create-rule.md)

- [Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する](active-directory-how-subscriptions-associated-directory.md)
