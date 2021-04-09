---
title: グループ情報の編集 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用してグループ情報を編集する方法を説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c346877d29c5fef03b45c895cbf8d462f90d0979
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369133"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Azure Active Directory を使用してグループ情報を編集する

Azure Active Directory (Azure AD) を使用すると、名前、説明、またはメンバーシップの種類の更新など、グループの設定を編集できます。

## <a name="to-edit-your-group-settings"></a>グループ設定を編集するには
1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、 **[グループ]** を選択します。

    **[グループ - すべてのグループ]** ページが表示され、ご自分のアクティブなグループがすべて表示されます。

3. **[グループ - すべてのグループ]** ページから、**[検索]** ボックスに必要なだけグループ名を入力します。 この記事では、**[MDM policy - West]\(MDM ポリシー - 西部\)** グループを検索します。

    検索結果が、文字を入力すると更新されて、**[検索]** ボックスの下に表示されます。

    ![[検索] ボックスに検索テキストを含む [すべてのグループ] ページ](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. グループの **[MDM policy - West]\(MDM ポリシー - 西部\)** を選択して、**[管理]** 領域から **[プロパティ]** を選択します。

    ![[メンバー] オプションと情報が強調表示されている、グループの概要ページ](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. 必要に応じて、次のような **[全般設定]** の情報を更新します。

    ![グループにおけるプロパティの設定](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **[グループ名]。** 既存のグループ名を編集します。
    
    - **[グループの説明]。** 既存のグループの説明を編集します。

    - **[グループの種類]。** グループの種類が作成されると、変更することはできません。 **[グループの種類]** を変更するには、グループを削除して、新しいグループを作成する必要があります。
    
    - **[メンバーシップの種類]。** メンバーシップの種類を変更します。 利用可能なさまざまなメンバーシップの種類に関する詳細については、[Azure Active Directory ポータルを使用して基本グループを作成してメンバーを追加する方法](active-directory-groups-create-azure-portal.md)に関する記事を参照してください。
    
    - **[オブジェクト ID]。** オブジェクト ID は変更できませんが、コピーして PowerShell コマンドでグループに対して使用できます。 PowerShell コマンドレットの使用に関する詳細については、[グループ設定の構成用の Azure Active Directory コマンドレット](../enterprise-users/groups-settings-v2-cmdlets.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory に関する追加情報を示します。

- [グループとメンバーを表示する](active-directory-groups-view-azure-portal.md)

- [基本的なグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md)

- [グループからメンバーを追加または削除する方法](active-directory-groups-members-azure-portal.md)

- [グループ内のユーザーの動的ルールの管理](../enterprise-users/groups-create-rule.md)

- [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)

- [グループを使用したリソースへのアクセス管理](active-directory-manage-groups.md)

- [Azure サブスクリプションの Azure Active Directory への関連付けまたは追加](active-directory-how-subscriptions-associated-directory.md)
