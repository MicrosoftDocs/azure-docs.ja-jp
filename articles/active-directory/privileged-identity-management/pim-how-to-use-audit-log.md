---
title: Azure AD PIM で Azure AD ロールの監査ログ レポートを表示する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの監査ログ履歴を表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8481c562ecbab1f26e877e55a5a9454695ddf4c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370833"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールの監査履歴を表示する

Privileged Identity Management (PIM) の監査履歴を使用すると、すべての特権ロールでの過去 30 日間におけるすべてのロール割り当てとアクティブ化を確認できます。 管理者、エンド ユーザー、同期アクティビティを含む Azure Active Directory (Azure AD) 組織内のアクティビティの完全な監査履歴を確認するには、[Azure Active Directory のセキュリティおよびアクティビティ レポート](../reports-monitoring/overview-reports.md)を使用できます。

## <a name="determine-your-version-of-pim"></a>PIM のバージョンを判断する

2019 年 11 月以降、Privileged Identity Management の Azure AD ロール部分は、Azure リソース ロールのエクスペリエンスと一致する新しいバージョンに更新されます。 これによって機能が追加され、[既存の API の変更](azure-ad-roles-features.md#api-changes)が行われます。 新しいバージョンのロールアウト中、この記事で実行する手順は、現在お使いになっている Privileged Identity Management のバージョンによって異なります。 このセクションの手順に従って、お使いになっている Privileged Identity Management のバージョンを確認してください。 Privileged Identity Management のバージョンを確認したら、この記事に記載されている手順のうち、そのバージョンに一致するものを選択することができます。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)のロールであるユーザーで [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。 概要ページの上部にバナーが表示されている場合は、この記事の **[新しいバージョン]** タブの指示に従ってください。 それ以外の場合は、 **[以前のバージョン]** タブの指示に従ってください。

    [![[Azure AD ロール - ディレクトリ ロールの監査履歴] ページを示すスクリーンショット。](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "自分のバージョン用のタブを選択する")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[新しいバージョン](#tab/new)

Azure AD ロールの監査履歴を表示するには、次の手順に従います。

## <a name="view-resource-audit-history"></a>リソースの監査履歴を表示する

リソースの監査では、自分の Azure AD ロールに関連付けられているすべてのアクティビティを確認できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[リソースの監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![フィルターを使用したリソースの監査の一覧](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>自分の監査を表示する

自分の監査を使用すると、個人のロール アクティビティを表示できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. 監査履歴を表示するリソースを選択します。

1. **[自分の監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![現在のユーザーに対する監査の一覧](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

## <a name="view-audit-history"></a>監査履歴を表示する

Azure AD ロールの監査履歴を表示するには、次の手順に従います。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[ディレクトリ ロールの監査履歴]** を選択します。

    監査履歴に応じて、棒グラフ、アクティブ化の合計数、1 日当たりの最大アクティブ化数、および 1 日当たりの平均アクティブ化数が表示されます。

    [![Azure AD ロールの新しいバージョン](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "ディレクトリ ロールの監査履歴を表示する")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    ページの下部に、使用可能な監査履歴内の各アクションに関する情報を含む表が表示されます。 列には次の意味があります。

    | 列 | 説明 |
    | --- | --- |
    | Time | アクションが発生した時刻。 |
    | 要求元 | ロールのアクティブ化または変更を要求したユーザー。 値が **[Azure システム]** の場合は、Azure 監査履歴で詳細を確認できます。 |
    | アクション | 要求元によって実行されたアクション。 アクションには、Assign、Unassign、Activate、Deactivate、または AddedOutsidePIM が含まれます。 |
    | メンバー | ロールをアクティブ化しているユーザー、またはロールに割り当てられているユーザー。 |
    | Role | ユーザーによって割り当てられたかアクティブ化されたロール。 |
    | 理由 | アクティブ化中に、[理由] フィールドに入力されたテキスト。 |
    | 有効期限 | アクティブ化されたロールが期限切れになる時刻。 資格のあるロールの割り当てにのみ適用されます。 |

1. 履歴を並べ替えるには、 **[時間]** 、 **[アクション]** 、および **[ロール]** の各ボタンを使用できます。

## <a name="filter-audit-history"></a>監査履歴をフィルター処理する

1. 監査履歴ページの上部にある **[フィルター]** ボタンをクリックします。

    **[グラフ パラメーターの更新]** ウィンドウが表示されます。

1. **[時間の範囲]** で、時間の範囲を選択します。

1. **[Roles]\(ロール\)** で、表示するロールを示すチェック ボックスをオンにします。

    ![グラフ パラメーター ウィンドウを更新する](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. **[完了]** を選択して、フィルター処理された監査履歴を表示します。

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>承認イベントの理由、承認者、およびチケット番号を取得する

1. 特権ロール管理者ロールのアクセス許可で [Azure portal](https://aad.portal.azure.com) にサインインし、Azure AD を開きます。
1. **[監査ログ]** を選択します。
1. **[サービス]** フィルターを使用して、Privileged Identity Management サービスの監査イベントのみを表示します。 **[監査ログ]** ページでは、次のことを実行できます。

    - **[状態の理由]** 列で監査イベントの理由を確認します。
    - "ロールへのメンバーの追加要求が承認されました" イベントの **[開始者 (アクター)]** 列で承認者を確認します。

    [![[開始者 (アクター)] メニューが開き、[PIM] が選択された [監査ログ] ページを示すスクリーンショット。](media/pim-how-to-use-audit-log/filter-audit-logs.png "PIM サービスについて監査ログをフィルターに掛ける")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. 監査ログ イベントを選択すると、 **[詳細]** ペインの **[アクティビティ]** タブにチケット番号が表示されます。
  
    [![[詳細] ペインにチケット番号が強調表示された [監査ログ] ページを示すスクリーンショット。](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "監査イベントのチケット番号を確認する")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. 監査イベントの **[詳細]** ペインの **[ターゲット]** タブに、要求者 (ロールをアクティブにしているユーザー) を表示できます。 Azure AD のロールには、次の 2 つのターゲットの種類があります。

    - ロール (**Type** = Role)
    - 要求者 (**Type** = User)

通常、承認イベントのすぐ上にある監査ログ イベントは、**開始者 (アクター)** が要求者である "ロールへのメンバーの追加が完了しました" のイベントです。 ほとんどの場合、監査の観点から承認要求で要求者を検索する必要はありません。

---

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールのアクティビティおよび監査履歴を表示する](azure-pim-resource-rbac.md)
