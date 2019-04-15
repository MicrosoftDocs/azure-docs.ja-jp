---
title: PIM で Azure リソース ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールを割り当てる方法を説明します。
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
ms.date: 08/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a912e1786e005e9a4b6ba2cc22713a172234a489
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578664"
---
# <a name="assign-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールを割り当てる

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) では、組み込みの Azure リソース ロールと、以下を含むカスタム ロール (ただしこれらに限定されません) を管理できます。

- Owner
- User Access Administrator
- Contributor
- セキュリティ管理者
- セキュリティ マネージャーなど

> [!NOTE]
> Azure AD でサブスクリプション管理を有効にする、所有者またはユーザー アクセス管理者ロールに割り当てられたグループのユーザーまたはメンバー、および全体管理者がリソース管理者です。 これらの管理者は、ロールの割り当て、ロール設定の構成、Azure リソースの PIM を使用したアクセスの確認ができます。 [Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)の一覧をご確認ください。

## <a name="assign-a-role"></a>ロールの割り当て

ユーザーを Azure リソース ロールの候補にするには、次の手順を実行します。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

    PIM を管理するためのアクセス権を別の管理者に付与する方法については、「[PIM を管理する他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)」をご覧ください。

1. **[Azure AD Privileged Identity Management]** を開きます。

    Azure portal で PIM をまだ開始していない場合は、「[PIM の使用を開始する](pim-getting-started.md)」をご覧ください。

1. **[Azure リソース]** をクリックします。

1. **[リソース フィルター]** を使用して、管理対象リソースの一覧をフィルター処理します。

    ![管理する Azure リソースの一覧](./media/pim-resource-roles-assign-roles/resources-list.png)

1. サブスクリプションや管理グループなど、管理するリソースをクリックします。

1. [管理] の **[ロール]** をクリックして、Azure リソースのロール一覧を表示します。

    ![Azure リソース ロール](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **[メンバーの追加]** をクリックして [新しい割り当て] ウィンドウを開きます。

1. **[ロールを選択する]** をクリックして、[ロールを選択する] ウィンドウを開きます。

    ![[新しい割り当て] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 割り当てるロールをクリックし、**[選択]** をクリックします。

    [メンバーまたはグループの選択] ウィンドウが開きます。

1. ロールに割り当てるメンバーまたはグループをクリックし、**[選択]** をクリックします。

    ![[メンバーまたはグループの選択] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    [メンバーシップ設定] ウィンドウが開きます。

1. **[割り当ての種類]** リストで **[対象]** または **[アクティブ]** を選択します。

    ![[メンバーシップ設定] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure リソース向けの PIM には、2 つの明確な割り当ての種類があります。

    - **[対象]** 割り当ての場合、このロールのメンバーは、ロールを使用するにはアクションを実行する必要があります。 要求されるアクションには、多要素認証 (MFA) チェックの実行、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。

    - **[アクティブ]** 割り当ての場合、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブ割り当てされたメンバーは、ロールによって提供される特権を常に所有します。

1. 割り当てを永続的 (永続的に対象または永続的に割り当て済み) にする必要がある場合は、**[Permanently]\(永続的\)** チェック ボックスをオンにします。

    ロールの設定によっては、チェック ボックスが表示されない場合や、変更できない場合があります。

1. 特定の割り当て期間を指定するには、チェック ボックスの選択を解除して、開始または終了日時フィールドを変更します。

    ![メンバー シップ設定 - 日付と時刻](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. 終了したら、**[完了]** をクリックします。

    ![新しい割り当て - 追加](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. 新しいロールの割り当てを作成するには、**[追加]** をクリックします。 状態の通知が表示されます。

    ![新しい割り当て - 通知](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>既存のロールの割り当てを更新または削除する

既存のロールの割り当てを更新または削除するには、次の手順を実行します。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** をクリックします。

1. サブスクリプションや管理グループなど、管理するリソースをクリックします。

1. [管理] の **[ロール]** をクリックして、Azure リソースのロール一覧を表示します。

    ![Azure リソース ロール - ロールの選択](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 更新または削除するロールをクリックします。

1. **[資格のあるロール]** タブまたは **[アクティブなロール]** タブでロールの割り当てを見つけます。

    ![ロールの割り当ての更新または削除](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. **[更新]** または **[削除]** をクリックして、ロールの割り当てを更新または削除します。

    ロールの割り当てを延長する方法については、「[PIM で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [PIM で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
- [PIM で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
