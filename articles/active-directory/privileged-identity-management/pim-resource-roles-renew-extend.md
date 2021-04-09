---
title: PIM で Azure リソース ロール割り当てを更新する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロール割り当てを延長または更新する方法を説明します。
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f0d22fc540aae448a3da731b709a3b4ea13a69d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742235"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールの割り当てを延長または更新する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) には、Azure リソースへのアクセスと割り当てのライフサイクルを管理するための新しいコントロールが提供されています。 管理者は、[開始日時] と [終了日時] プロパティを使用してメンバーシップを割り当てることができます。 割り当ての終了日時が近づくと、Privileged Identity Management は、影響を受けるユーザーまたはグループに電子メール通知を送信します。 適切なアクセスが維持されることを保証するためにリソースの管理者にも電子メール通知を送信します。 アクセスが延長されなかった場合でも、割り当てを更新して、最大 30 日間、期限切れの状態のままで表示できます。

## <a name="who-can-extend-and-renew"></a>延長と更新を実行できるのは誰か

ロールの割り当ての期限を延長するか更新できるのは、リソースの管理者だけです。 影響を受けるユーザーまたはグループは、まもなく期限切れになるロールの延長と、既に期限切れになっているロールの更新を要求できます。

## <a name="when-are-notifications-sent"></a>通知の送信時機

Privileged Identity Management は、14 日以内に期限切れになるロールの管理者と影響を受けるユーザーまたはグループに電子メール通知を送信します。電子メール通知は、有効期限の前日にも送信されます。 電子メールは、割り当てが正式に期限切れになったときにも送信されます。

管理者は、ユーザーまたはグループが期限が迫っているか期限切れになっているロールが期限の延長または更新を要求したときに通知を受信します。 特定の管理者が要求を解決すると、他のすべての管理者に解決の決定 (承認または拒否) が通知されます。 その後、要求したユーザーまたはグループに決定が通知されます。

## <a name="extend-role-assignments"></a>ロールの割り当てを延長する

ロールの割り当ての延長または更新の要求、解決、または管理に関連する手順とユーザー インターフェイスの要点を次に示します。

### <a name="self-extend-expiring-assignments"></a>有効期限が切れた割り当ての自動延長

ロールに割り当てられたユーザーまたはグループは、リソースの **[自分のロール]** ページ、および Privileged Identity Management ポータルの **[自分のロール]** ページの最上位レベルの **[有資格]** または **[アクティブ]** から、有効期限が切れるロールの割り当てを直接延長できます。ユーザーまたはグループは、向こう 14 日以内に有効期限が切れる、資格のあるアクティブな (割り当て済みの) ロールの延長を要求できます。

![資格のあるロールと [アクション] 列が一覧表示されている [Azure リソース] - [自分のロール] ページ](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

割り当ての終了日時が 14 日以内にある場合は、ユーザー インターフェイスの **延長** ボタンがアクティブ リンクになります。 次の例では、現在の日付が 3 月 27 日であると想定しています。

![アクティブ化または延長するためのリンクがある [アクション] 列](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

このロールの割り当ての延長を要求するには、**延長** をクリックして要求フォームを開きます。

![[理由] ボックスがある [Extend role assignment]/(ロールの割り当ての延長/) ウィンドウ](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

**[割り当ての詳細]** を展開して、最初の割り当てに関する情報を確認します。 延長要求の理由を入力し、**延長** をクリックします。

>[!NOTE]
>延長が必要な詳しい理由と希望の延長期間 (わかっている場合) を入力することをお勧めします。

![[割り当ての詳細] が展開されている [Extend role assignment]/(ロールの割り当ての延長/) ウィンドウ](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

リソース管理者は、延長要求の確認を求める電子メール通知をすぐに受信します。 延長の要求が既に送信されている場合は、Azure の通知がポータルに表示されます。

![保留になっている既存のロール割り当ての延長が既にあることを示す通知](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

**[保留中の要求]** ページに移動して、要求の状態を確認します。要求をキャンセルすることもできます。

![保留中の要求とキャンセルのためのリンクが一覧表示されている [Azure リソース] - [保留中の要求] ページ](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>管理者に承認された延長

ユーザーまたはグループがロールの割り当てを延長する要求を送信すると、リソース管理者は、最初の割り当ての詳細と要求者が入力した理由を含む電子メール通知を受信します。 通知には、管理者が要求を承認または拒否するための直接リンクが含まれています。

電子メール内のリンクに加え、管理者は、Privileged Identity Management の管理ポータルに移動し、左側のペインから **[要求の承認]** を選択することで、要求を承認または拒否できます。

![要求と承認または拒否するためのリンクが一覧表示されている [Azure リソース] - [要求の承認] ページ](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

管理者が **[承認]** または **[拒否]** を選択すると、要求の詳細が、監査ログ用の業務上の正当な理由を入力するフィールドと共に表示されます。

![[要求元の理由]、[割り当ての種類]、[開始時刻]、[終了時刻]、および [理由] がある [Approve role assignment request]/(ロールの割り当て要求の承認/)](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

要求を承認してロールの割り当てを延長するとき、リソース管理者は、新しい開始日時、終了日時、および割り当ての種類を選択できます。 管理者が特定のタスク を完了するためのアクセス制限 を指定する (たとえば 1 日だけ延長する) 場合は、割り当ての種類の変更が必要になることがあります。 この例では、管理者は、割り当ての種類を **有資格** から **アクティブ** に変更できます。 つまり、アクティブ化の必要がないアクセスを要求者に提供できます。

### <a name="admin-initiated-extension"></a>管理者による延長

ロールに割り当てられたユーザーがロールの割り当ての延長を要求しない場合、管理者がユーザーの代わりに割り当てを延長できます。 管理者によるロールの割り当ての延長には承認は必要ありませんが、ロールの延長の完了時に他のすべての管理者に通知が送信されます。

ロールの割り当てを延長するには、Privileged Identity Management のリソース ロールまたは割り当てビューを参照します。 延長する必要がある割り当てを探します。 そして、[アクション] 列の **延長** をクリックします。

![資格のあるロールと延長のためのリンクが一覧表示されている [Azure リソース] - [割り当て] ページ](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>ロールの割り当ての更新

概念上は似ていますが、有効期限が切れたロールの割り当てを更新するプロセスは、メンバーにとっても管理者にとっても、延長の要求とは異なっています。 割り当てと管理者は、次の手順を使用して、必要な場合に有効期限が切れたロールへのアクセスを更新できます。

### <a name="self-renew"></a>自己更新

リソースにアクセスできなくなったユーザーは、最大 30 日間分の有効期限が切れた割り当て履歴にアクセスできます。 PIM の左側のナビゲーションで **自分のロール** に移動し、[Azure リソースのロール] セクションの **期限切れのロール** タブを選択します。

![[自分のロール] ページ - [期限切れのロール] タブ](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

ロールの一覧には、**有資格の割り当て** が既定で表示されます。 ドロップダウンを使用して、割り当て済みの有資格ロールとアクティブ ロールを切り替えます。

一覧のいずれかのロールの割り当ての更新を要求するには、**更新** アクションを選択します。 そして、要求の理由を入力します。 詳しい理由または業務上の正当な理由を加えて延長期間を入力すると、リソース管理者が承認するか拒否するかを決定する際に役立ちます。

![[理由] ボックスが表示されている [ロールの割り当ての更新] ウィンドウ](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

要求が送信されると、リソース管理者は、ロールの割り当ての更新を求めている保留中の要求の通知を受信します。

### <a name="admin-approves"></a>管理者による承認

リソース管理者は、電子メール通知内のリンクから更新要求にアクセスできます。Auzre Portal から Privileged Identity Management にアクセスし、左側のナビゲーション メニューから **[要求の承認]** を選択することでもアクセスできます。

![要求と承認または拒否するためのリンクが一覧表示されている [Azure リソース] - [要求の承認] ページ](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

管理者が **[承認]** または **[拒否]** を選択すると、要求の詳細が、監査ログ用の業務上の正当な理由を入力するフィールドと共に表示されます。

![[要求元の理由]、[割り当ての種類]、[開始時刻]、[終了時刻]、および [理由] がある [Approve role assignment request]/(ロールの割り当て要求の承認/)](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

要求を承認してロールの割り当てを更新するとき、リソース管理者は、新しい開始日時、終了日時、および割り当ての種類を入力する必要があります。

### <a name="admin-renew"></a>管理者による更新

リソース管理者は、リソースの左側のナビゲーション メニューの **メンバー** で、有効期限が切れているロールの割り当てを更新できます。 またはリソース ロールの **期限切れのロール** タブで、有効期限が切れているロールの割り当てを更新できます。

有効期限が切れたすべてのロールの割り当ての一覧を表示するには、**メンバー** 画面で **期限切れのロール** を選択します。

![期限切れのロールと更新のためのリンクが一覧表示されている [Azure リソース] - [メンバー] ページ](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
- [Privileged Identity Management で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
