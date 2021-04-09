---
title: 特権アクセス グループに有資格の所有者およびメンバーを割り当てる - Azure Active Directory
description: Azure AD Privileged Identity Management (PIM) でロールを割り当て可能なグループの有資格の所有者またはメンバーを割り当てる方法について説明します。
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
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534422"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Privileged Identity Management で特権アクセス グループ (プレビュー) の資格を割り当てる

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用すると、Azure AD の特権アクセス グループへの割り当ての資格とアクティブ化が管理しやすくなります。 グループのメンバーまたは所有者に資格を割り当てることができます。

>[!NOTE]
>特権アクセス グループのメンバーシップまたは所有権の適用対象であるユーザーは、Azure AD Premium P2 ライセンスを持っている必要があります。 詳細については、「[Privileged Identity Management を使用するためのライセンスの要件](subscription-requirements.md)」を参照してください。

## <a name="assign-an-owner-or-member-of-a-group"></a>グループの所有者またはメンバーを割り当てる

これらの手順に従って、ユーザーを特権アクセス グループのメンバーまたは所有者の適用対象にすることができます。

1. 全体管理者またはグループ所有者のアクセス許可を持つ [Azure AD にサインイン](https://aad.portal.azure.com)します。
1. **[グループ]** を選択し、管理対象とする、ロールを割り当て可能なグループを選択します。 リストを検索またはフィルター処理することができます。

    ![PIM で管理する、ロールを割り当て可能なグループを見つける](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. グループを開き、 **[特権アクセス (プレビュー)]** を選択します。

    ![Privileged Identity Management エクスペリエンスを開く](./media/groups-assign-member-owner/groups-discover-groups.png)

1. **[割り当ての追加]** を選択します。

    ![[新しい割り当て] ウィンドウ](./media/groups-assign-member-owner/groups-add-assignment.png)

1. 特権アクセス グループの適用対象にするメンバーまたは所有者を選択します。

    ![[メンバーまたはグループの選択] ウィンドウが開かれ、[選択] ボタンが強調表示されている [割り当ての追加] ページを示すスクリーンショット。](./media/groups-assign-member-owner/add-assignments.png)

1. **[次へ]** を選択して、メンバーシップまたは所有権の期間を設定します。

    ![[メンバーまたはグループの選択] ウィンドウ](./media/groups-assign-member-owner/assignment-duration.png)

1. **[割り当ての種類]** リストで **[対象]** または **[アクティブ]** を選択します。 特権アクセス グループには、2 つの異なる割り当ての種類があります。

    - **[対象]** 割り当ての場合、このロールのメンバーは、ロールを使用するにはアクションを実行する必要があります。 要求されるアクションには、多要素認証 (MFA) チェックの実行、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。

    - **[アクティブ]** 割り当ての場合、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブ割り当てされたメンバーは、ロールによって提供される特権を常に所有します。

1. 割り当てを永続的 (永続的に対象または永続的に割り当て済み) にする必要がある場合は、 **[Permanently]\(永続的\)** チェック ボックスをオンにします。 組織の設定によっては、チェック ボックスが表示されない場合や編集できない場合があります。

1. 完了したら、 **[割り当て]** を選択します。

1. 新しいロールの割り当てを作成するには、 **[追加]** を選択します。 状態の通知が表示されます。

    ![新しい割り当て - 通知](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>既存のロールの割り当てを更新または削除する

既存のロールの割り当てを更新または削除するには、次の手順を実行します。

1. 全体管理者またはグループ所有者のアクセス許可を持つ [Azure AD にサインイン](https://aad.portal.azure.com)します。
1. **[グループ]** を選択し、管理対象とする、ロールを割り当て可能なグループを選択します。 リストを検索またはフィルター処理することができます。

    ![PIM で管理する、ロールを割り当て可能なグループを見つける](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. グループを開き、 **[特権アクセス (プレビュー)]** を選択します。

    ![Privileged Identity Management エクスペリエンスを開く](./media/groups-assign-member-owner/groups-discover-groups.png)

1. 更新または削除するロールを選択します。

1. **[資格のあるロール]** タブまたは **[アクティブなロール]** タブでロールの割り当てを見つけます。

    ![ロールの割り当ての更新または削除](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. **[更新]** または **[削除]** を選択して、ロールの割り当てを更新または削除します。

    ロールの割り当てを延長する方法については、[Privileged Identity Management で Azure リソース ロールを延長または更新する方法](pim-resource-roles-renew-extend.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
