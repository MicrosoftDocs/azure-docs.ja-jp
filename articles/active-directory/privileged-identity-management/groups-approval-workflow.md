---
title: Privileged Identity Management でグループ メンバーおよび所有者のアクティブ化要求を承認する - Azure AD
description: Azure AD Privileged Identity Management (PIM) でロールを割り当て可能なグループに対する要求を承認または拒否する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91536989"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>特権アクセス グループのメンバーと所有者のアクティブ化要求を承認する (プレビュー)

Azure Active Directory (Azure AD) の Privileged Identity Management (PIM) を使用すると、アクティブ化の承認を必要とするように特権アクセス グループのメンバーと所有者を構成できます。また、Azure AD 組織からユーザーまたはグループを代理承認者として選択できます。 特権ロール管理者の作業負荷を減らすには、グループごとに 2 人以上の承認者を選択することをお勧めします。 代理承認者は、要求を承認するまでに 24 時間あります。 要求が 24 時間以内に承認されない場合、有資格ユーザーは新しい要求を再送信する必要があります。 24 時間の承認時間枠は構成できません。

Azure リソース ロールの要求を承認または拒否するには、この記事の手順に従ってください。

## <a name="view-pending-requests"></a>保留中の要求を表示する

代理承認者は、Azure リソース ロール要求が代理承認者による承認を待っている状態になると、電子メール通知を受け取ります。 保留中の要求は、Privileged Identity Management で表示できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[要求の承認]** を選択します。

    ![申請の承認 - レビューの要求を示す Azure リソース ページ](./media/groups-approval-workflow/groups-select-request.png)

    **[ロールのアクティブ化に関する要求]** セクションに、承認が保留されている要求の一覧が表示されます。

## <a name="approve-requests"></a>要求の承認

1. 承認する要求を見つけて選択し、 **[承認]** を選択します。

    ![[要求の承認] ページを示すスクリーンショット。[承認] ボタンと [確認] ボタンが強調表示されています。](./media/groups-approval-workflow/groups-confirm-approval.png)

1. **[理由]** ボックスに、業務上の正当な理由を入力します。

1. **[確認]** を選択します。 承認によって Azure の通知が生成されます。

## <a name="deny-requests"></a>要求を拒否する

1. 拒否する要求を見つけて選択し、 **[拒否]** を選択します。

    ![申請の承認 - 詳細と [根拠] ボックスを含む承認または拒否のウィンドウ](./media/groups-approval-workflow/groups-confirm-denial.png)

1. **[理由]** ボックスに、業務上の正当な理由を入力します。

1. **[確認]** を選択します。 拒否によって Azure の通知が生成されます。

## <a name="workflow-notifications"></a>ワークフロー通知

ワークフロー通知に関するいくつかの情報を次に示します。

- グループ割り当ての要求のレビューが保留中の場合、承認者にメールで通知されます。 電子メール通知には、承認者が承認または拒否できる、要求への直接リンクが含まれています。
- 要求は、承認または拒否した最初の承認者によって解決されます。
- 承認者が要求に応答すると、すべての承認者にその動作が通知されます。

>[!Note]
>承認されたユーザーをアクティブにしないほうがよいと見なした管理者は、Privileged Identity Management でアクティブなグループ割り当てを削除できます。 リソース管理者は、承認者でない限り、保留中の要求の通知を受け取りませんが、Privileged Identity Management で保留中の要求を表示することで、すべてのユーザーの保留中の要求を確認およびキャンセルできます。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management でグループ割り当てを延長または更新する](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management での電子メール通知](pim-email-notifications.md)
- [Privileged Identity Management でグループ割り当てに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
