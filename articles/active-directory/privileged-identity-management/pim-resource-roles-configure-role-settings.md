---
title: PIM で Azure リソース ロールの設定を構成する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールの設定を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6da92ccd50707e676a1f5d583fe22ae8b3d8b73
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476314"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>PIM で Azure リソース ロールの設定を構成する

Azure リソース ロールの設定を構成するときに、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) で Azure リソース ロールの割り当てに適用される既定の設定を定義します。 次の手順を実行して、承認ワークフローを構成し、誰が要求を承認または拒否できるかを指定します。

## <a name="open-role-settings"></a>ロールの設定を開く

次の手順を実行して、Azure リソース ロールの設定を開きます。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** をクリックします。

1. サブスクリプションや管理グループなど、管理するリソースをクリックします。

    ![管理できるリソースを一覧表示する [Azure リソース] ページ](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. **[ロールの設定]** をクリックします。

    ![Azure リソース ロールを一覧表示する [ロールの設定] ページ](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 設定を構成するロールをクリックします。

    ![複数の割り当てとアクティブ化の設定を一覧表示する [ロールの設定の詳細] ページ](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **[編集]** をクリックして [ロールの設定] ウィンドウを開きます。

    ![割り当てとアクティブ化の設定を更新するためのオプションがある [Edit role settings]/(ロールの設定の編集/) ページ](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    各ロールの [ロールの設定] ウィンドウでは、複数の設定を構成できます。

## <a name="assignment-duration"></a>割り当て期間

ロールの設定を構成するときに、各割り当ての種類 (対象とアクティブ) に 2 つの割り当て期間オプションから選択できます。 これらのオプションは、PIM でメンバーがロールに割り当てられるときの既定の最大期間になります。

このような**資格のある**割り当て期間のオプションからいずれかを選択することができます。

| | |
| --- | --- |
| **永続的に資格のある割り当てを許可する** | リソース管理者は、永続的に資格のあるメンバーシップを割り当てることができます。 |
| **次の後に、資格のある割り当ての有効期限が切れる** | リソース管理者は、すべての資格のある割り当てに、開始日と終了日の指定を必須にすることができます。 |

また、このような**アクティブな**割り当て期間のオプションからいずれかを選択することができます。

| | |
| --- | --- |
| **永続するアクティブな割り当てを許可する** | リソース管理者は、永続的にアクティブなメンバーシップを割り当てることができます。 |
| **アクティブ割り当ては次の期間後に期限切れになる** | リソース管理者は、すべてのアクティブな割り当てに、開始日と終了日の指定を必須にすることができます。 |

> [!NOTE] 
> リソース管理者は､終了日時が指定されている割り当てのどれでも更新することができます｡ これに対し､メンバーは[ロールの割り当てを延長または更新する](pim-resource-roles-renew-extend.md)セルフサービス要求を開始することができます｡

## <a name="require-multi-factor-authentication"></a>多要素認証が必要です

PIM によって、2 つの異なるシナリオで、必要に応じて Azure Multi-factor Authentication (MFA) を強制できます。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>アクティブな割り当てに多要素認証が必要

場合によっては、メンバーをロールに短期間 (たとえば 1 日) 割り当てる必要があります。 この場合、割り当てられたメンバーにアクティブ化を要求する必要はありません。 このシナリオでは、メンバーは、ロールが割り当てられた時点からそのロールで既にアクティブであるため、自分のロールの割り当てを使用しているとき、PIM では MFA を強制できません。

割り当てを実行するリソース管理者が、確かに本人が言うような人物であることを保証するために、 **[アクティブな割り当てに多要素認証が必要]** チェックボックスをオンにして、アクティブな割り当てに MFA を強制することができます。

### <a name="require-multi-factor-authentication-on-activation"></a>アクティブ化に Multi-Factor Authentication を要求する

ロールの資格を持つメンバーに対して、アクティブ化には MFA の実行を必須にすることができます。 このプロセスにより、アクティブ化を要求しているユーザーが、確かに本人が言うような人物であることが合理的かつ確実に保証されます。 このオプションを強制すると、ユーザー アカウントが侵害された可能性がある状況で、重要なリソースが保護されます。

資格のあるメンバーに対して、アクティブ化には MFA の実行を必須にするには、 **[アクティブ化には Multi-Factor Authentication が必要です]** チェックボックスをオンにします。

詳細については、[多要素認証 (MFA) と PIN](pim-how-to-require-mfa.md) に関するページを参照してください。

## <a name="activation-maximum-duration"></a>アクティブ化の最大期間

**[アクティブ化の最大期間]** スライダーを使用して、ロールの有効期限が切れるまでの最大時間 (時間単位) を設定します。 1 から 24 時間の範囲の値を指定できます。

## <a name="require-justification"></a>理由を必須にする

アクティブな割り当てに対して、またはアクティブ化するときに、メンバーに理由の入力を必須にすることができます。 理由を必須にするには、 **[アクティブな割り当てに理由が必要]** チェックボックスまたは **[アクティブ化に理由が必要]** チェックボックスをオンにします。

## <a name="require-approval-to-activate"></a>アクティブ化の承認を必須にする

ロールをアクティブ化するには承認を必須にする場合は、次の手順を実行します。

1. **[アクティブにするには承認が必要です]** チェックボックスをオンにします。

1. **[承認者の選択]** をクリックして [メンバーまたはグループの選択] ウィンドウを開きます。

    ![承認者を選択するための [メンバーまたはグループの選択] ウィンドウ](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 少なくとも 1 つのメンバーまたはグループを選択し、 **[選択]** をクリックします。 任意の組み合わせのメンバーとグループを追加できます。 少なくとも 1 人の承認者を選択する必要があります。 既定の承認者はいません。

    選択した承認者の一覧に選択内容が表示されます。

1. すべてのロール設定を指定したら、 **[更新]** をクリックして変更を保存します。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
- [PIM で Azure リソース ロールに対するセキュリティの警告を構成する](pim-resource-roles-configure-alerts.md)
