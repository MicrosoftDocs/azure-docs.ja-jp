---
title: PIM で Azure リソース ロールに対する要求を承認する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールに対する要求を承認または拒否する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 096da0547806680f62b569f7c7809a7bbb04cb86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742081"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールに対する要求を承認または拒否する

Azure Active Directory (Azure AD) の Privileged Identity Management (PIM) を使用すると、アクティブ化の承認を必要とするようにロールを構成できます。また、Azure AD 組織からユーザーまたはグループを代理承認者として選択できます。 特権ロール管理者の作業負荷を減らすには、ロールごとに 2 人以上の承認者を選択することをお勧めします。 代理承認者は、要求を承認するまでに 24 時間あります。 要求が 24 時間以内に承認されない場合、有資格ユーザーは新しい要求を再送信する必要があります。 24 時間の承認時間枠は構成できません。

Azure リソース ロールの要求を承認または拒否するには、この記事の手順に従ってください。

## <a name="view-pending-requests"></a>保留中の要求を表示する

代理承認者は、Azure リソース ロール要求が代理承認者による承認を待っている状態になると、電子メール通知を受け取ります。 これらの保留中の要求は、Privileged Identity Management で表示できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[要求の承認]** を選択します。

    ![申請の承認 - レビューの要求を示す Azure リソース ページ](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    **[ロールのアクティブ化に関する要求]** セクションに、承認が保留されている要求の一覧が表示されます。

## <a name="approve-requests"></a>要求の承認

1. 承認する要求を見つけて選択します。 承認または拒否のページが表示されます。

    ![申請の承認 - 詳細と [根拠] ボックスを含む承認または拒否のウィンドウ](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **[理由]** ボックスに、業務上の正当な理由を入力します。

1. **[承認]** を選択します。 承認に関する Azure 通知を受信します。

    ![要求が承認されたことを示す承認の通知](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>要求を拒否する

1. 拒否する要求を見つけて選択します。 承認または拒否のページが表示されます。

    ![申請の承認 - 詳細と [根拠] ボックスを含む承認または拒否のウィンドウ](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **[理由]** ボックスに、業務上の正当な理由を入力します。

1. **[拒否]** を選択します。 拒否すると同時に通知が表示されます。

## <a name="workflow-notifications"></a>ワークフロー通知

ワークフロー通知に関するいくつかの情報を次に示します。

- ロールの要求のレビューが保留中の場合、承認者にメールで通知されます。 電子メール通知には、承認者が承認または拒否できる、要求への直接リンクが含まれています。
- 要求は、承認または拒否した最初の承認者によって解決されます。
- 承認者が要求に応答すると、すべての承認者にその動作が通知されます。
- リソース管理者には、承認されたユーザーがそのロール内でアクティブになると通知されます。

>[!Note]
>承認されたユーザーをアクティブにしないほうがよいと見なしたリソース管理者は、Privileged Identity Management でアクティブなロールの割り当てを削除できます。 リソース管理者は、承認者でない限り、保留中の要求の通知を受け取りませんが、Privileged Identity Management で保留中の要求を表示することで、すべてのユーザーの保留中の要求を確認およびキャンセルできます。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management での電子メール通知](pim-email-notifications.md)
- [Privileged Identity Management で Azure AD ロールに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
