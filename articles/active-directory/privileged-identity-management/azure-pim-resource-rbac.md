---
title: Privileged Identity Management (PIM) で Azure リソース ロールの監査レポートを表示する - Azure AD |Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールのアクティビティおよび監査履歴を表示します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002495"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールのアクティビティおよび監査履歴を表示する

組織内の Azure リソース ロールのアクティビティ、アクティブ化、および監査履歴を Azure Active Directory (Azure AD) Privileged Identity Management (PIM) で表示できます。 対象には、サブスクリプション、リソース グループ、さらに仮想マシンも含まれます。 Azure のロールベースのアクセス制御機能を利用する Azure portal 内のすべてのリソースで、Privileged Identity Management のセキュリティおよびライフサイクル管理機能を利用できます。

> [!NOTE]
> 組織で、[Azure の委任されたリソース管理](../../lighthouse/concepts/azure-delegated-resource-management.md)を使用するサービス プロバイダーに管理機能を外部委託している場合、そのサービス プロバイダーによって承認されているロールの割り当てはここに表示されません。

## <a name="view-activity-and-activations"></a>アクティビティとアクティブ化を表示する

各リソースに対して特定のユーザーが行った操作を確認する場合、指定したアクティブ化期間に関連付けられている Azure リソース アクティビティを表示することができます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. アクティビティとアクティブ化を表示するリソースを選択します。

1. **[ロール]** または **[メンバー]** をクリックします。

1. ユーザーを選択します。

    日付ごとの Azure リソースに対するユーザー操作の概要が表示されます。 その同じ期間での最近のロールのアクティブ化も表示されます。

    ![リソース アクティビティの概要とロールのアクティブ化を含むユーザーの詳細](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 特定のロールのアクティブ化を選択して、詳細およびそのユーザーがアクティブな間に発生した、対応する Azure リソース アクティビティを表示します。

    [![選択されたロールのアクティブ化とアクティビティの詳細](media/azure-pim-resource-rbac/export-membership.png "選択されたロールのアクティブ化とアクティビティの詳細")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>子を含むロールの割り当てをエクスポートする

コンプライアンス要件により、ロールの割り当ての完全な一覧を監査担当者に提供しなければならない場合があります。 Privileged Identity Management を使用すると、特定のリソースでロールの割り当て (すべての子リソースのロールの割り当てを含む) のクエリを実行できます。 以前は、管理者がサブスクリプションに対するロールの割り当ての完全な一覧を取得するのは困難で、特定のリソースごとにロールの割り当てをエクスポートする必要がありました。 Privileged Identity Management を使用すると、サブスクリプション内のアクティブかつ資格のあるすべてのロールの割り当て (すべてのリソース グループとリソースに対するロールの割り当てを含む) についてクエリを実行できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. サブスクリプションなど、ロールの割り当てをエクスポートするリソースを選択します。

1. **[メンバー]** を選択します。

1. **[エクスポート]** を選択して [メンバーシップのエクスポート] ウィンドウを開きます。

    [![すべてのメンバーをエクスポートする [メンバーシップのエクスポート] ウィンドウ](media/azure-pim-resource-rbac/export-membership.png "すべてのメンバーをエクスポートする [メンバーシップのエクスポート] ページ")](media/azure-pim-resource-rbac/export-membership.png)

1. **[すべてのメンバーのエクスポート]** を選択して、すべてのロールの割り当てを CSV ファイルでエクスポートします。

    ![Excel に表示された CSV ファイルでエクスポートされたロールの割り当て](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>リソースの監査履歴を表示する

リソースの監査では、リソースのすべてのロール アクティビティのビューが提供されます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. 監査履歴を表示するリソースを選択します。

1. **[リソースの監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    [![フィルターを使用したリソースの監査の一覧](media/azure-pim-resource-rbac/rbac-resource-audit.png "フィルターを使用したリソースの監査の一覧")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. **[監査の種類]** で **[アクティブ化 (割り当て済み + アクティブ化済み)]** を選択します。

    [![アクティブ化の監査の種類でフィルター処理されたリソースの監査の一覧](media/azure-pim-resource-rbac/rbac-audit-activity.png "アクティブ化でフィルター処理されたリソース監査の一覧")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![アクティブ化の監査の種類でフィルター処理されたリソースの監査の一覧](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. **[操作]** の下で、ユーザーの **[(アクティビティ)]** をクリックして、そのユーザーの Azure リソースに対するアクティビティの詳細を表示します。

    ![特定のアクションに対するユーザーのアクティビティの詳細](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>自分の監査を表示する

自分の監査を使用すると、個人のロール アクティビティを表示できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. 監査履歴を表示するリソースを選択します。

1. **[自分の監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    [![現在のユーザーに対する監査の一覧](media/azure-pim-resource-rbac/my-audit-time.png "現在のユーザーに対する監査の一覧")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> 監査履歴にアクセスするには、全体管理者ロールまたは特権ロールを持った管理者ロールが必要です。

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>承認イベントの理由、承認者、およびチケット番号を取得する

1. 特権ロール管理者ロールのアクセス許可で [Azure portal](https://aad.portal.azure.com) にサインインし、Azure AD を開きます。
1. **[監査ログ]** を選択します。
1. **[サービス]** フィルターを使用して、Privileged Identity Management サービスの監査イベントのみを表示します。 **[監査ログ]** ページでは、次のことを実行できます。

    - **[状態の理由]** 列で監査イベントの理由を確認します。
    - 「ロールへのメンバーの追加要求が承認されました」イベントの **[開始者 (アクター)]** 列で承認者を確認します。

    [![PIM サービスについて監査ログをフィルターに掛ける](media/azure-pim-resource-rbac/filter-audit-logs.png "PIM サービスについて監査ログをフィルターに掛ける")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. 監査ログ イベントを選択すると、 **[詳細]** ウィンドウの **[アクティビティ]** タブにチケット番号が表示されます。
  
    [![監査イベントのチケット番号の確認する](media/azure-pim-resource-rbac/audit-event-ticket-number.png "監査イベントのチケット番号を確認する")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)

1. 監査イベントの **[詳細]** ウィンドウの **[ターゲット]** タブに、要求者 (ロールをアクティブにしているユーザー) を表示できます。 Azure リソース ロールには、次の 3 種類のターゲットがあります。

    - ロール (**Type** = Role)
    - 要求者 (**Type** = Other)
    - 承認者 (**Type** = User)

    [![ターゲットの種類を確認する](media/azure-pim-resource-rbac/audit-event-target-type.png "ターゲットの種類を確認する")](media/azure-pim-resource-rbac/audit-event-target-type.png)

通常、承認イベントのすぐ上にあるログ イベントは、**開始者 (アクター)** が要求者である「ロールへのメンバーの追加が完了しました」のイベントです。 ほとんどの場合、監査の観点から承認要求で要求者を検索する必要はありません。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
- [Privileged Identity Management で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
- [Privileged Identity Management で Azure AD ロールの監査履歴を表示する](pim-how-to-use-audit-log.md)
