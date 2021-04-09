---
title: PIM で Azure リソース ロールの設定を構成する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールの設定を構成する方法について説明します。
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
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ded81389fae5954c0e488f634945b4f1c1fe4c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009043"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールの設定を構成する

Azure リソース ロールの設定を構成するときに、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) で Azure リソース ロールの割り当てに適用される既定の設定を定義します。 次の手順を実行して、承認ワークフローを構成し、誰が要求を承認または拒否できるかを指定します。

## <a name="open-role-settings"></a>ロールの設定を開く

次の手順を実行して、Azure リソース ロールの設定を開きます。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)ロールのユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. サブスクリプションや管理グループなど、管理するリソースを選択します。

    ![管理できるリソースを一覧表示する [Azure リソース] ページ](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. **ロール設定** を選択します。

    ![Azure リソース ロールを一覧表示する [ロールの設定] ページ](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 設定を構成するロールを選択します。

    ![複数の割り当てとアクティブ化の設定を一覧表示する [ロールの設定の詳細] ページ](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **[編集]** を選択して **[ロールの設定]** ペインを開きます。 最初のタブでは、Privileged Identity Management でロールのアクティブ化の構成を更新できます。

    ![[アクティブ化] タブが開かれている [Edit role settings]\(ロールの設定の編集\) ページ](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. **[割り当て]** タブまたはページ下部の **[Next: Assignment]\(次へ: 割り当て)** ボタンを選択し、割り当ての設定タブを開きます。これらの設定は、Privileged Identity Management インターフェイス内で行われたロールの割り当てを制御します。

    ![[ロールの設定] ページの [ロールの割り当て] タブ](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. **通知** タブまたはページの下部にある **Next:Activation\(次へ: アクティブ化** ボタンから、このロールの通知設定タブを表示します。 これらの設定は、このロールに関連するすべてのメール通知を制御します。

    ![[ロールの設定] ページの [ロールの通知] タブ](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    [ロールの設定] ページの **[通知]** タブで、Privileged Identity Management によって、通知を受信するユーザーとユーザーが受信する通知をきめ細かく制御できます。

    - **メールをオフにする**<br>特定のメールをオフにするには、既定の受信者のチェック ボックスをオフにし、追加の受信者をすべて削除します。  

    - **指定したメール アドレスへのメールを制限する**<br>既定の受信者に送信されるメールをオフにするには、既定の受信者のチェック ボックスをオフにします。 その後に、追加の受信者として追加のメール アドレスを追加できます。 複数のメール アドレスを追加する場合は、セミコロン (;) で区切ります。

    - **既定の受信者と追加の受信者の両方にメールを送信する**<br>既定の受信者と追加の受信者の両方にメールを送信するには、既定の受信者のチェック ボックスをオンにし、追加の受信者のメール アドレスを追加します。

    - **重要なメールのみ**<br>メールの種類ごとに、重要なメールのみを受信するためのチェック ボックスをオンにすることができます。 つまり、Privileged Identity Management では、メールに即時の操作が必要な場合にのみ、構成された受信者に引き続きメールが送信されます。 たとえば、ユーザーにロールの割り当てを拡張するように求めるメールはトリガーされませんが、管理者に拡張の要求を承認するように求めるメールはトリガーされます。

1. いつでも **[更新]** ボタンを選択して、ロールの設定を更新します。

## <a name="assignment-duration"></a>割り当て期間

ロールの設定を構成するときに、各割り当ての種類 (対象とアクティブ) に 2 つの割り当て期間オプションから選択できます。 これらのオプションは、Privileged Identity Management でユーザーがロールに割り当てられるときの既定の最大期間になります。

このような **資格のある** 割り当て期間のオプションからいずれかを選択することができます。

| | |
| --- | --- |
| **永続的に資格のある割り当てを許可する** | リソース管理者は、永続的に資格のある割り当てを行うことができます。 |
| **次の後に、資格のある割り当ての有効期限が切れる** | リソース管理者は、すべての資格のある割り当てに、開始日と終了日の指定を必須にすることができます。 |

また、このような **アクティブな** 割り当て期間のオプションからいずれかを選択することができます。

| | |
| --- | --- |
| **永続するアクティブな割り当てを許可する** | リソース管理者は、永続的にアクティブな割り当てを行うことができます。 |
| **アクティブ割り当ては次の期間後に期限切れになる** | リソース管理者は、すべてのアクティブな割り当てに、開始日と終了日の指定を必須にすることができます。 |

> [!NOTE]
> リソース管理者は､終了日時が指定されている割り当てのどれでも更新することができます｡ これに対し､ユーザーは[ロールの割り当てを延長または更新する](pim-resource-roles-renew-extend.md)セルフサービス要求を開始することができます｡

## <a name="require-multi-factor-authentication"></a>多要素認証が必要です

Privileged Identity Management では、2 つの異なるシナリオで、Azure AD Multi-factor Authentication の強制を選択できます。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>アクティブな割り当てに多要素認証が必要

場合によっては、ユーザーまたはグループをロールに短期間 (たとえば 1 日) 割り当てる必要があります。 この場合、割り当てられたユーザーがアクティブ化を要求する必要はありません。 このシナリオでは、ユーザーは割り当てられた時点からそのロールでアクティブになるため、各自がそのロールの割り当てを使用するときに、Privileged Identity Management で Multi-Factor Authentication を強制することができません。

割り当てを実行するリソース管理者が、確かに本人が言うような人物であることを保証するために、 **[アクティブな割り当てに多要素認証が必要]** チェックボックスをオンにして、アクティブな割り当てに Multi-Factor Authentication を強制することができます。

### <a name="require-multi-factor-authentication-on-activation"></a>アクティブ化に Multi-Factor Authentication を要求する

ロールの資格を持つユーザーに対して、アクティブ化する前に Azure AD Multi-Factor Authentication を使用していることを証明するように要求することができます。 Multi-Factor Authentication により、ユーザーが、確かに本人が言うような人物であることが合理的かつ確実に保証されます。 このオプションを強制すると、ユーザー アカウントが侵害された可能性がある状況で、重要なリソースが保護されます。

アクティブ化には多要素認証の実行を必須にするには、 **[アクティブ化には Multi-Factor Authentication が必要です]** チェックボックスをオンにします。

詳細については、[Multi-Factor Authentication と Privileged Identity Management](pim-how-to-require-mfa.md) に関するページを参照してください。

## <a name="activation-maximum-duration"></a>アクティブ化の最大期間

**[アクティブ化の最大期間]** スライダーを使用して、ロールの有効期限が切れるまでの最大時間 (時間単位) を設定します。 1 から 24 時間の範囲の値を指定できます。

## <a name="require-justification"></a>理由を必須にする

ユーザーがアクティブ化するときに、業務上の正当な理由を入力するように要求できます。 理由を必須にするには、 **[アクティブな割り当てに理由が必要]** チェックボックスまたは **[アクティブ化に理由が必要]** チェックボックスをオンにします。

## <a name="require-approval-to-activate"></a>アクティブ化の承認を必須にする

ロールをアクティブ化するには承認を必須にする場合は、次の手順を実行します。

1. **[アクティブにするには承認が必要です]** チェックボックスをオンにします。

1. **[承認者の選択]** を選択して **[メンバーまたはグループの選択]** ページを開きます。

    ![承認者を選択するための [ユーザーまたはグループの選択] ペイン](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 少なくとも 1 つのユーザーまたはグループを選択し、 **[選択]** をクリックします。 任意の組み合わせのユーザーとグループを追加できます。 少なくとも 1 人の承認者を選択する必要があります。 既定の承認者はいません。

    選択した承認者の一覧に選択内容が表示されます。

1. すべてのロール設定を指定したら、 **[更新]** を選択して変更を保存します。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
- [Privileged Identity Management で Azure リソース ロールに対するセキュリティ アラートを構成する](pim-resource-roles-configure-alerts.md)
