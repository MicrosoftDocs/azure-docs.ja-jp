---
title: PIM で Azure AD ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールを割り当てる方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1760d0e0bd356a05d84c07eda005e0526da5d13
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476521"
---
# <a name="assign-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールを割り当てる

全体管理者は、Azure Active Directory (Azure AD) を使用して、**永続的**な Azure AD 管理者ロールの割り当てを行うことができます。 こうしたロールの割り当ては、[Azure portal](../users-groups-roles/directory-assign-admin-roles.md) または [PowerShell のコマンド](/powershell/module/azuread#directory_roles) を使用して作成できます。

永続的な管理ロールの割り当ては、特権ロール管理者が Azure AD Privileged Identity Management (PIM) サービスを使用して行うこともできます。 さらに、特権ロール管理者は、ユーザーを Azure AD 管理者ロールの**候補**にすることもできます。 管理者候補は必要なときにロールをアクティブ化できます。作業が完了すると、そのアクセス許可は期限切れになります。

## <a name="make-a-user-eligible-for-a-role"></a>ユーザーをロールの候補にする

ユーザーを Azure AD 管理者ロールの候補にするには、次の手順を実行します。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

    PIM を管理するためのアクセス権を別の管理者に付与する方法については、「[PIM を管理する他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)」をご覧ください。

1. **[Azure AD Privileged Identity Management]** を開きます。

    Azure portal で PIM をまだ開始していない場合は、「[PIM の使用を開始する](pim-getting-started.md)」をご覧ください。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[ロール]** または **[メンバー]** をクリックします。

    ![[ロール] と [メンバー] メニュー オプションが強調表示された Azure AD ロール](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **[メンバーの追加]** をクリックして [管理されるメンバーの追加] を開きます。

1. **[Select a role]\(ロールを選択する\)** をクリックして、管理するロールをクリックし、 **[選択]** をクリックします。

    ![Azure AD ロールを一覧している [ロールの選択] ウィンドウ](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **[メンバーの選択]** をクリックしてロールに割り当てるユーザーを選択し、 **[選択]** をクリックします。

    ![ユーザーを選択できる [メンバーの選択] ウィンドウ](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. [管理されるメンバーの追加] で **[OK]** をクリックし、ロールにユーザーを追加します。

1. ロールの一覧で、割り当てたロールをクリックしてメンバーの一覧を表示します。

     ロールが割り当てられると、選択したユーザーが、ロールの**候補**としてメンバー リストに表示されます。

    ![ロールのメンバーがアクティブ化の状態と共に一覧されている](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. これで、ユーザーがロールの候補者になりました。「[PIM で自分の Azure AD ロールをアクティブにする](pim-how-to-activate-role.md)」の指示に従って、ロールをアクティブ化できることをユーザーに通知します。

    管理者候補は、アクティブ化時に Azure Multi-Factor Authentication (MFA) への登録が求められます。 ユーザーが MFA に登録できない場合、または Microsoft アカウント (通常は @outlook.com) を使用している場合は、すべてのロールでこのユーザーを永続化する必要があります。

## <a name="make-a-role-assignment-permanent"></a>ロールの割り当てを永続化する

既定では、新しいユーザーは、あくまで Azure AD 管理者ロールの候補でしかありません。 ロールの割り当てを永続化する場合は、次の手順に従ってください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[メンバー]** をクリックします。

    ![Azure AD ロール - ロールとアクティブ化の状態を示すメンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 永続化する**候補**ロールをクリックします。

1. **[More]\(その他\)** をクリックして **[永続化]** をクリックします。

    ![[その他] メニュー オプションが開いたロールの対象になるユーザーを一覧するウィンドウ](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    ロールが**永続**として表示されています。

    ![現在、永続のロールとアクティブ化の状態を示すメンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>ユーザーをロールから削除する

ロールの割り当てからユーザーを削除することはできますが、必ず、永続的な全体管理者が少なくとも 1 人は常に存在するようにしてください。 どのユーザーに引き続きロールの割り当てが必要であるかわからない場合は、[ロールのアクセス レビューを開始](pim-how-to-start-security-review.md)してください。

特定のユーザーを Azure AD 管理者ロールから削除するには、次の手順に従います。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[メンバー]** をクリックします。

    ![Azure AD ロール - ロールとアクティブ化の状態を示すメンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 削除するロールの割り当てをクリックします。

1. **[More]\(その他\)** をクリックして **[削除]** をクリックします。

    ![[その他] メニュー オプションが開いた永続ロールを含むユーザーを一覧するウィンドウ](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 確認を求めるメッセージで **[はい]** をクリックします。

    ![ロールからメンバーを削除するかどうかを確認するメッセージ](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    ロールの割り当てが削除されました。

## <a name="authorization-error-when-assigning-roles"></a>ロールの割り当て時の承認エラー

サブスクリプション用に PIM を最近有効化し、ユーザーを Azure AD 管理者ロールの対象にしようとして承認エラーが発生した場合、MS PIM サービス プリンシパルにまだ適切なアクセス許可がないためである可能性があります。 MS PIM サービス プリンシパルには、他のユーザーにロールを割り当てるための[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールが必要です。 MS PIM にユーザー アクセス管理者ロールが割り当てられるまで待つ代わりに、それを手動で割り当てることができます。

以下の手順に従って、MS PIM サービス プリンシパルにサブスクリプション用のユーザー アクセス管理者ロールを割り当てます。

1. Azure Portal にグローバル管理者としてサインインします。

1. **[すべてのサービス]** 、 **[サブスクリプション]** の順に選択します。

1. サブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択します。

1. **[ロールの割り当て]** を選択すると、サブスクリプションをスコープとするロールの割り当ての現在の一覧が表示されます。

   ![サブスクリプションの [アクセス制御 (IAM)] ブレード](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. **MS PIM**サービス プリンシパルに**ユーザー アクセス管理者**ロールが割り当てられているかどうかを確認します。

1. そうなっていない場合、 **[ロールの割り当ての追加]** を選択して、 **[ロールの割り当ての追加]** ウィンドウを開きます。

1. **[ロール]** ドロップダウン リストで、 **[ユーザー アクセス管理者]** ロールを選択します。

1. **[選択]** リストで、 **[MS PIM]** サービス プリンシパルを見つけて選択します。

   ![[ロールの割り当ての追加] ウィンドウ - MS-PIM サービス プリンシパルにアクセス許可を追加する](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. **[保存]** を選択して、ロールを割り当てます。

   しばらくすると、MS PIM サービス プリンシパルに、サブスクリプション スコープでユーザー アクセス管理者ロールが割り当てられます。

   ![MS-PIM のユーザー アクセス管理者ロールの割り当てを示すアクセスの制御 (IAM) ブレード](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>次の手順

- [PIM で Azure AD 管理者ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
