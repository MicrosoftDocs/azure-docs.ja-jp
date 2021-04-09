---
title: Privileged Identity Management でメンバー割り当ての有効期限が切れたグループ所有者を更新する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) でロールの割り当て可能なグループの割り当てを延長または更新する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87505619"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Privileged Identity Management で特権アクセス グループの割り当て (プレビュー) を延長または更新する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) には、特権アクセス グループに対するアクセスと割り当てのライフサイクルを管理するための制御が備わっています。 管理者は、[開始日時] と [終了日時] プロパティを使用してメンバーシップを割り当てることができます。 割り当ての終了日時が近づくと、Privileged Identity Management は、影響を受けるユーザーまたはグループに電子メール通知を送信します。 適切なアクセスが維持されることを保証するためにリソースの管理者にも電子メール通知を送信します。 アクセスが延長されなかった場合でも、割り当てを更新して、最大 30 日間、期限切れの状態のままで表示できます。

## <a name="who-can-extend-and-renew"></a>延長と更新を実行できるユーザー

特権アクセス グループの割り当てを延長または更新できるのは、リソースの管理者だけです。 影響を受けるユーザーまたはグループは、まもなく期限切れになる割り当ての延長を要求したり、既に期限切れになっている割り当ての更新を要求したりできます。

## <a name="when-notifications-are-sent"></a>通知の送信タイミング

Privileged Identity Management から、期限切れになる特権アクセス グループの割り当ての管理者および影響を受けるユーザーに電子メール通知が送信されます。

- 有効期限切れ前の 14 日以内
- 有効期限の切れる前日
- 割り当ての有効期限が切れたとき

管理者は、ユーザーまたはグループが期限が迫っているか期限切れになっている割り当ての延長または更新を要求したときに通知を受信します。 1 人の管理者が要求を解決すると、すべての管理者と要求元のユーザーにその承認または拒否が通知されます。

## <a name="extend-group-assignments"></a>グループの割り当てを延長する

次の手順で、グループの割り当ての延長または更新の要求、解決、または管理のプロセスの概要を示します。

### <a name="self-extend-expiring-assignments"></a>有効期限が切れた割り当ての自動延長

特権アクセス グループに割り当てられているユーザーは、グループの **[割り当て]** ページにある **[対象]** または **[アクティブ]** タブから直接、有効期限が迫っているグループの割り当てを延長できます。 ユーザーまたはグループは、14 日以内に期限切れになる対象およびアクティブの割り当ての延長を要求できます。

![[アクション] 列を含む、対象の割り当てが一覧表示されている [自分のロール] ページ](media/groups-renew-extend/self-extend-group-assignment.png)

割り当て終了日時が 14 日以内になると、 **[延長]** コマンドが使用できるようになります。 グループ割り当ての延長を要求するには、 **[延長]** を選択して、要求フォームを開きます。

![[理由] ボックスと詳細を含むグループの割り当ての延長ペイン](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>延長が必要な詳しい理由と希望の延長期間 (わかっている場合) を入力することをお勧めします。

管理者は、延長要求の確認を求める電子メール通知をすぐに受信します。 延長の要求が既に送信されている場合は、Azure の通知がポータルに表示されます。

要求の状態の表示またはキャンセルを行うには、グループの割り当ての **[保留中の要求]** ページを開きます。

![特権アクセス グループの割り当て - [保留中の要求] ページ (キャンセルするためのリンクを表示)](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>管理者に承認された延長

ユーザーまたはグループがグループの割り当ての延長を求める要求を送信すると、管理者は、元の割り当ての詳細と要求の理由を含む電子メール通知を受信します。 通知には、管理者が要求を承認または拒否するための直接リンクが含まれています。

電子メール内のリンクに加え、管理者は、Privileged Identity Management の管理ポータルに移動し、左側のペインから **[要求の承認]** を選択することで、要求を承認または拒否できます。

![特権アクセス グループの割り当て - [要求の承認] ページ (要求と、承認または拒否を行うためのリンクを一覧表示)](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

管理者が **[承認]** または **[拒否]** を選択すると、要求の詳細が、監査ログ用の業務上の正当な理由を入力するフィールドと共に表示されます。

![[要求元の理由]、[割り当ての種類]、[開始時刻]、[終了時刻]、および [理由] を含むグループの割り当て要求の承認](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

リソース管理者は、グループの割り当ての延長を求める要求を承認するとき、新しい開始日、終了日、および割り当ての種類を選択できます。 管理者が特定のタスク を完了するためのアクセス制限 を指定する (たとえば 1 日だけ延長する) 場合は、割り当ての種類の変更が必要になることがあります。 この例では、管理者は、割り当ての種類を **有資格** から **アクティブ** に変更できます。 つまり、アクティブ化の必要がないアクセスを要求者に提供できます。

### <a name="admin-initiated-extension"></a>管理者による延長

グループに割り当てられているユーザーがグループの割り当ての延長を要求しない場合、管理者がユーザーの代わりに割り当てを延長できます。 管理者によるグループの割り当ての延長には承認は必要ありませんが、割り当てが延長された後に他のすべての管理者に通知が送信されます。

グループの割り当てを延長するには、Privileged Identity Management の割り当てビューを参照します。 延長する必要がある割り当てを探します。 そして、[アクション] 列の **延長** をクリックします。

![延長するためのリンクが付いた対象グループ割り当てが一覧表示されている [割り当て] ページ](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>グループの割り当てを更新する

有効期限が切れたグループの割り当てを更新するプロセスは、延長の要求のプロセスと概念上は似ていますが、異なります。 割り当てと管理者は、次の手順を使用して、必要な場合に有効期限が切れた割り当てへのアクセスを更新できます。

### <a name="self-renew"></a>自己更新

リソースにアクセスできなくなったユーザーは、最大 30 日間分の有効期限が切れた割り当て履歴にアクセスできます。 これを行うには、左側のペインで **[自分のロール]** を参照し、 **[Expired assignments]\(期限切れの割り当て\)** タブを選択します。

![[自分のロール] ページ - [Expired assignments]\(期限切れの割り当て\) タブ](media/groups-renew-extend/groups-renew-from-my-roles.png)

割り当ての一覧には、**対象の割り当て** が既定で表示されます。 [対象] と [アクティブ] の割り当てを切り替えるには、ドロップダウン メニューを使用します。

一覧のいずれかのグループの割り当ての更新を要求するには、 **[更新]** アクションを選択します。 そして、要求の理由を入力します。 詳しい理由または業務上の正当な理由を加えて延長期間を入力すると、リソース管理者が承認するか拒否するかを決定する際に役立ちます。

![[理由] ボックスが表示されているグループの割り当ての更新ペイン](media/groups-renew-extend/groups-renew-request-form.png)

要求が送信されると、リソース管理者は、グループの割り当ての更新を求める保留中の要求の通知を受信します。

### <a name="admin-approves"></a>管理者による承認

リソース管理者は、電子メール通知内のリンクから更新要求にアクセスできます。Auzre Portal から Privileged Identity Management にアクセスし、左側のナビゲーション メニューから **[要求の承認]** を選択することでもアクセスできます。

管理者が **[承認]** または **[拒否]** を選択すると、要求の詳細が、監査ログ用の業務上の正当な理由を入力するフィールドと共に表示されます。

リソース管理者は、グループの割り当てを更新を求める要求を承認するとき、新しい開始日、終了日、および割り当ての種類を入力する必要があります。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で特権アクセス グループの割り当てに対する要求を承認または拒否する](groups-approval-workflow.md)
- [Privileged Identity Management で特権アクセス グループの設定を構成する](groups-role-settings.md)
