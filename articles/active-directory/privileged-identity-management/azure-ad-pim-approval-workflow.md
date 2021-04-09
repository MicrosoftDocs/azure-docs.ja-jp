---
title: PIM で Azure AD ロールに対する要求を承認または拒否する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールに対する要求を承認または拒否する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d74a243ab10d0fffca960dc5149999560a7b54cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94980588"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールに対する要求を承認または拒否する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、アクティブ化の承認を必要とするようにロールを構成できます。また、代理承認者として 1 名以上のユーザーまたはグループを選択できます。 代理承認者は、要求を承認するまでに 24 時間あります。 要求が 24 時間以内に承認されない場合、有資格ユーザーは新しい要求を再送信する必要があります。 24 時間の承認時間枠は構成できません。

## <a name="determine-your-version-of-pim"></a>PIM のバージョンを判断する

2019 年 11 月以降、Privileged Identity Management の Azure AD ロール部分は、Azure ロールのエクスペリエンスと一致する新しいバージョンに更新されます。 これによって機能が追加され、[既存の API の変更](azure-ad-roles-features.md#api-changes)があります。 新しいバージョンのロールアウト中、この記事で実行する手順は、現在お使いになっている Privileged Identity Management のバージョンによって異なります。 このセクションの手順に従って、お使いになっている Privileged Identity Management のバージョンを確認してください。 Privileged Identity Management のバージョンを確認したら、この記事に記載されている手順のうち、そのバージョンに一致するものを選択することができます。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)のロールであるユーザーで [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。 概要ページの上部にバナーが表示されている場合は、この記事の **[新しいバージョン]** タブの指示に従ってください。 それ以外の場合は、 **[以前のバージョン]** タブの指示に従ってください。

    [![[Azure AD] > [Privileged Identity Management] を選択します。](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Azure AD ロールに対する要求を承認または拒否するには、この記事の手順に従ってください。

## <a name="new-version"></a>[新しいバージョン](#tab/new)

### <a name="view-pending-requests"></a>保留中の要求を表示する

代理承認者は、Azure AD ロール要求が代理承認者による承認を待っている状態になると、メール通知を受け取ります。 これらの保留中の要求は、Privileged Identity Management で表示できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[要求の承認]** を選択します。

    ![申請の承認 - Azure AD ロールのレビューの要求を示すページ](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    **[ロールのアクティブ化に関する要求]** セクションに、承認が保留されている要求の一覧が表示されます。

### <a name="approve-requests"></a>要求の承認

1. 承認する要求を見つけて選択します。 承認または拒否のページが表示されます。

    ![[申請の承認 - Azure AD ロール] ページを示すスクリーンショット。](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. **[理由]** ボックスに、業務上の正当な理由を入力します。

1. **[承認]** を選択します。 承認に関する Azure 通知を受信します。

    ![要求が承認されたことを示す承認の通知](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

### <a name="deny-requests"></a>要求を拒否する

1. 拒否する要求を見つけて選択します。 承認または拒否のページが表示されます。

    ![申請の承認 - 詳細と [根拠] ボックスを含む承認または拒否のウィンドウ](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **[理由]** ボックスに、業務上の正当な理由を入力します。

1. **[拒否]** を選択します。 拒否すると同時に通知が表示されます。

### <a name="workflow-notifications"></a>ワークフロー通知

ワークフロー通知に関するいくつかの情報を次に示します。

- ロールの要求のレビューが保留中の場合、承認者にメールで通知されます。 電子メール通知には、承認者が承認または拒否できる、要求への直接リンクが含まれています。
- 要求は、承認または拒否した最初の承認者によって解決されます。
- 承認者が要求に応答すると、すべての承認者にその動作が通知されます。
- 全体管理者と特権ロール管理者には、承認されたユーザーがそのロール内でアクティブになると通知されます。

>[!NOTE]
>全体管理者または特権ロール管理者は、承認されたユーザーをアクティブにするべきではないと判断した場合、Privileged Identity Management でアクティブなロールの割り当てを削除できます。 管理者は、承認者でない限り、保留中の要求の通知を受け取りませんが、Privileged Identity Management で保留中の要求を表示することで、すべてのユーザーの保留中の要求を確認およびキャンセルできます。

## <a name="previous-version"></a>[以前のバージョン](#tab/previous)

### <a name="view-pending-requests"></a>保留中の要求を表示する

代理承認者は、Azure AD ロール要求が代理承認者による承認を待っている状態になると、メール通知を受け取ります。 これらの保留中の要求は、Privileged Identity Management で表示できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[申請の承認]** をクリックします。

    ![Azure AD ロール - 申請の承認](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    承認が保留されている要求の一覧を確認できます。

### <a name="approve-requests"></a>要求の承認

1. 承認する要求を選択し、 **[承認]** をクリックして、[選択した要求の承認] ウィンドウを開きます。

    ![[承認] オプションが強調表示された [申請の承認] の一覧](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. **[承認の理由]** ボックスに理由を入力します。

    ![[承認の理由] を含む [選択した要求の承認] ウィンドウ](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. **[Approve]\(承認\)** をクリックします。

    承認すると、状態シンボルが更新されます。

    ![[承認] をクリックした後の [選択した要求の承認] ウィンドウ](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

### <a name="deny-requests"></a>要求を拒否する

1. 拒否する要求を選択し、 **[拒否]** をクリックして、[選択した要求の拒否] ウィンドウを開きます。

    ![[拒否] オプションが強調表示された [申請の承認] の一覧](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. **[拒否の理由]** ボックスに理由を入力します。

    ![[拒否の理由] を含む [選択した要求の拒否] ウィンドウ](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. **[拒否]** を選択します。

    拒否すると、状態シンボルが更新されます。

---

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management での電子メール通知](pim-email-notifications.md)
- [Privileged Identity Management で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
