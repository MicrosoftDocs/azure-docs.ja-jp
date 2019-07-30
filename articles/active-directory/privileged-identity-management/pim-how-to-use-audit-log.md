---
title: PIM で Azure AD ロールの監査履歴を表示する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの監査履歴を表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053943"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールの監査履歴を表示する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) の監査履歴を使用すると、すべての特権ロールでの過去 30 日間のすべてのロールの割り当てとアクティブ化を確認できます。 管理者、エンド ユーザー、同期アクティビティを含むディレクトリのアクティビティの完全な監査履歴を確認するには、[Azure Active Directory のセキュリティおよびアクティビティ レポート](../reports-monitoring/overview-reports.md)を使用できます。

## <a name="view-audit-history"></a>監査履歴を表示する

Azure AD ロールの監査履歴を表示するには、次の手順に従います。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[ディレクトリ ロールの監査履歴]** をクリックします。

    監査履歴に応じて、棒グラフ、アクティブ化の合計数、1 日当たりの最大アクティブ化数、および 1 日当たりの平均アクティブ化数が表示されます。

    ![ディレクトリ ロールの監査履歴](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    ページの下部に、使用可能な監査履歴内の各アクションに関する情報を含む表が表示されます。 列には次の意味があります。

    | 列 | 説明 |
    | --- | --- |
    | Time | アクションが発生した時刻。 |
    | 要求元 | ロールのアクティブ化または変更を要求したユーザー。 値が **[Azure システム]** の場合は、Azure 監査履歴で詳細を確認できます。 |
    | Action | 要求元によって実行されたアクション。 アクションには、Assign、Unassign、Activate、Deactivate、または AddedOutsidePIM が含まれます。 |
    | メンバー | ロールをアクティブ化しているユーザー、またはロールに割り当てられているユーザー。 |
    | Role | ユーザーによって割り当てられたかアクティブ化されたロール。 |
    | 理由 | アクティブ化中に、[理由] フィールドに入力されたテキスト。 |
    | 有効期限 | アクティブ化されたロールが期限切れになる時刻。 資格のあるロールの割り当てにのみ適用されます。 |

1. 履歴を並べ替えるには、 **[時間]** 、 **[アクション]** 、および **[ロール]** の各ボタンを使用できます。

## <a name="filter-audit-history"></a>監査履歴をフィルター処理する

1. 監査履歴ページの上部にある **[フィルター]** ボタンをクリックします。

    **[グラフ パラメーターの更新]** ウィンドウが表示されます。

1. **[時間の範囲]** で、時間の範囲を選択します。

1. **[ロール]** で、表示するロールをオンにして追加します。

    ![グラフ パラメーター ウィンドウを更新する](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. **[完了]** をクリックして、フィルター処理された監査履歴を表示します。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールのアクティビティおよび監査履歴を表示する](azure-pim-resource-rbac.md)
