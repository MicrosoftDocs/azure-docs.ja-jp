---
title: PIM で Azure リソース ロール割り当てを延長または更新する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロール割り当てを延長または更新する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8aef7d4ac37109233af4ffb364b6f3e4c9b291ac
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578392"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>PIM で Azure リソース ロール割り当てを延長または更新する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) には、Azure リソースへのアクセスと割り当てのライフサイクルを管理するための新しいコントロールが導入されています。 管理者は、[開始日時] と [終了日時] プロパティを使用してメンバーシップを割り当てることができます。 割り当ての終了日時が近づくと、PIM は、影響を受けるユーザーまたはグループに電子メール通知を送信します。 適切なアクセスが維持されることを保証するためにリソースの管理者にも電子メール通知を送信します。 アクセスが延長されなかった場合でも、割り当てを更新して、最大 30 日間、期限切れの状態のままで表示できます。

## <a name="who-can-extend-and-renew"></a>延長と更新を実行できるのは誰か

ロールの割り当ての期限を延長するか更新できるのは、リソースの管理者だけです。 影響を受けるメンバーは、まもなく期限切れになるロールの延長と、既に期限切れになっているロールの更新を要求できます。

## <a name="when-are-notifications-sent"></a>通知の送信時機

PIM は、14 日以内に期限切れになるロールの管理者と影響を受けるメンバーに電子メール通知を送信します。電子メール通知は、有効期限の前日にも送信されます。 電子メールは、割り当てが正式に期限切れになったときにも送信されます。 

管理者は、期限が迫っているか期限切れになっているロールのメンバーが期限の延長または更新を要求したときに通知を受信します。 特定の管理者が要求を解決すると、他のすべての管理者に解決の決定 (承認または拒否) が通知されます。 その後、要求したメンバーに決定が通知されます。 

## <a name="extend-role-assignments"></a>ロールの割り当てを延長する

ロールの割り当ての延長または更新の要求、解決、または管理に関連する手順とユーザー インターフェイスの要点を次に示します。 

### <a name="member-extend"></a>メンバーの延長

ロールの割り当てのメンバーは、期限が迫っているロールの割り当ての延長を、リソースの **自分のロール**ページの**有資格**または**アクティブ**タブと、PIM ポータルの**自分のロール**から直接要求できます。 メンバーは、14 日後に期限切れになる有資格ロールと (割り当て済みの) アクティブ ロールの延長を要求できます。

![ロールの拡張](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

割り当ての終了日時が 14 日以内にある場合は、ユーザー インターフェイスの**延長**ボタンがアクティブ リンクになります。 次の例では、現在の日付が 3 月 27 日であると想定しています。

![[Extend]\(延長\) ボタン](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

このロールの割り当ての延長を要求するには、**延長**をクリックして要求フォームを開きます。

![要求フォームを開く](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

**[割り当ての詳細]** を展開して、最初の割り当てに関する情報を確認します。 延長要求の理由を入力し、**延長**をクリックします。

>[!Note]
>延長が必要な詳しい理由と希望の延長期間 (わかっている場合) を入力することをお勧めします。

![ロールの割り当てを延長する](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

リソース管理者は、延長要求の確認を求める電子メール通知をすぐに受信します。 延長要求が既に送信されている場合は、Azure Portal の上部に、エラーの理由を説明するトースト通知が表示されます。

![通知を説明するエラー](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

左側のペインから**保留中の要求**タブに移動して、要求の状態を確認します。要求をキャンセルすることもできます。

![保留中の要求](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>管理者の承認

メンバーがロールの割り当てを延長する要求を送信すると、リソース管理者は、最初の割り当ての詳細と要求者が入力した理由を含む電子メール通知を受信します。 通知には、管理者が要求を承認または拒否するための直接リンクが含まれています。 

電子メール内のリンクに加え、管理者は、PIM の管理ポータルに移動し、左側のペインから**申請の承認**を選択することで、要求を承認または拒否できます。

![エラーのスクリーンショット](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

管理者が**承認**または**拒否**を選択すると、要求の詳細が、監査ログ用の理由を入力するフィールドと共に表示されます。

![ロールの割り当て要求を承認する](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

要求を承認してロールの割り当てを延長するとき、リソース管理者は、新しい開始日時、終了日時、および割り当ての種類を選択できます。 管理者が特定のタスク を完了するためのアクセス制限 を指定する (たとえば 1 日だけ延長する) 場合は、割り当ての種類の変更が必要になることがあります。 この例では、管理者は、割り当ての種類を**有資格**から**アクティブ**に変更できます。 つまり、アクティブ化の必要がないアクセスを要求者に提供できます。

### <a name="admin-extend"></a>管理の延長

ロールのメンバーがロールのメンバーシップの延長要求を忘れたか実行できなかった場合、管理者がメンバーに代わって割り当てを延長できます。 ロールのメンバーシップの管理者による延長には承認は必要ありませんが、ロールの延長の完了時に他のすべての管理者に通知が送信されます。

ロールのメンバーシップを延長するには、PIM でリソースのロールまたはメンバー ビューに移動します。 延長する必要があるメンバーを探します。 そして、[アクション] 列の**延長**をクリックします。

![ロールのメンバーシップの延長](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>ロールの割り当ての更新

概念上は似ていますが、有効期限が切れたロールの割り当てを更新するプロセスは、メンバーにとっても管理者にとっても、延長の要求とは異なっています。 メンバーと管理者は、次の手順を使用して、必要な場合に有効期限が切れたロールへのアクセスを更新できます。

### <a name="member-renew"></a>メンバーによる更新

リソースにアクセスできなくなったメンバーは、最大 30 日間分の有効期限が切れた割り当て履歴にアクセスできます。 PIM の左側のナビゲーションで**自分のロール**に移動し、[Azure リソースのロール] セクションの**期限切れのロール**タブを選択します。

![[期限切れのロール] タブ](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

ロールの一覧には、**有資格の割り当て**が既定で表示されます。 ドロップダウンを使用して、割り当て済みの有資格ロールとアクティブ ロールを切り替えます。

一覧のいずれかのロールの割り当ての更新を要求するには、**更新**アクションを選択します。 そして、要求の理由を入力します。 詳しい理由に加えて延長期間を入力すると、リソース管理者が承認するか拒否するかを決定する際に役立ちます。

![ロールの割り当ての更新](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

要求が送信されると、リソース管理者は、ロールの割り当ての更新を求めている保留中の要求の通知を受信します。

### <a name="admin-approves"></a>管理者による承認

リソース管理者は、電子メール通知内のリンクから更新要求にアクセスできます。Auzre Portal から PIM にアクセスし、左側のナビゲーション メニューから**申請の承認**を選択することでもアクセスできます。

![要求の承認](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

管理者が**承認**または**拒否**を選択すると、要求の詳細が、監査ログ用の理由を入力するフィールドと共に表示されます。

![ロールの割り当ての承認](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

要求を承認してロールの割り当てを更新するとき、リソース管理者は、新しい開始日時、終了日時、および割り当ての種類を入力する必要があります。 

### <a name="admin-renew"></a>管理者による更新

リソース管理者は、リソースの左側のナビゲーション メニューの**メンバー**で、有効期限が切れているロールの割り当てを更新できます。 またはリソース ロールの**期限切れのロール**タブで、有効期限が切れているロールの割り当てを更新できます。

有効期限が切れたすべてのロールの割り当ての一覧を表示するには、**メンバー**画面で**期限切れのロール**を選択します。

![期限切れのロール](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
- [PIM で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
