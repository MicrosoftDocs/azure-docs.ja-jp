---
title: Azure AD カスタム ロールの割り当てを更新または削除する - Privileged Identity Management (PIM)
description: Privileged Identity Management (PIM) で Azure AD カスタム ロールの割り当てを更新または削除する方法
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a35442dd8af1cd4acf22de453c8d10460e1e39f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371530"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD カスタム ロールの割り当てを更新または削除する

この記事では、Privileged Identity Management (PIM) を使用し、Azure Active Directory (Azure AD) の管理エクスペリエンスから、アプリケーション管理用に作成されたカスタム ロールへの Just-In-Time の割り当てと期間限定の割り当てを更新したり削除したりする方法について説明します。 

- Azure AD でカスタム ロールを作成してアプリケーション管理を委任する方法について詳しくは、「[Azure Active Directory でのカスタム管理者ロール (プレビュー)](../roles/custom-overview.md)」を参照してください。 
- まだ Privileged Identity Management を使用したことがない方は、[Privileged Identity Management の使用を開始する](pim-getting-started.md)方法に関するページで詳しい情報をご覧いただけます。

> [!NOTE]
> プレビュー段階では、Azure AD のカスタム ロールがビルトイン ディレクトリ ロールと連携していません。 その機能の一般提供が開始されれば、ビルトイン ロールのエクスペリエンスでロールの管理が行えるようになります。 次のバナーが表示される場合は、これらのロールは[組み込みロールのエクスペリエンスで](pim-how-to-add-role-to-user.md)管理する必要があり、この記事は適用されません。
>
> [![[Azure AD] > [Privileged Identity Management] を選択します。](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>割り当てを更新または削除する

既存のカスタム ロールの割り当てを更新または削除するには、次の手順を実行します。

1. 特権ロール管理者のロールに割り当てられたユーザー アカウントで、Azure portal の [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) にサインインします。
1. **[Azure AD カスタム ロール (プレビュー)]** を選択します。

    ![[Azure AD カスタム ロール (プレビュー)] を選択して資格のあるロールの割り当てを表示する](./media/azure-ad-custom-roles-assign/view-custom.png)

1. **[ロール]** を選択して、Azure AD アプリケーションのカスタム ロールの **[割り当て]** リストを表示します。

    ![[ロール] を選択して資格のあるロールの割り当てを一覧表示する](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. 更新または削除するロールを選択します。
1. **[資格のあるロール]** タブまたは **[アクティブなロール]** タブでロールの割り当てを見つけます。
1. **[更新]** または **[削除]** を選択して、ロールの割り当てを更新または削除します。

    ![資格のあるロールの割り当てで [削除] または [更新] を選択する](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>次のステップ

- [Azure AD のカスタム ロールをアクティブ化する](azure-ad-custom-roles-assign.md)
- [Azure AD のカスタム ロールを割り当てる](azure-ad-custom-roles-assign.md)
- [Azure AD のカスタム ロールの割り当てを構成する](azure-ad-custom-roles-configure.md)
