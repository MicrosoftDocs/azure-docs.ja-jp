---
title: PIM で Azure AD ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールを割り当てる方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/16/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd4374067fe0070c379a76ef5f59bb6aef5b29fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123107"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールを割り当てる

全体管理者は、Azure Active Directory (Azure AD) を使用して、**永続的** な Azure AD 管理者ロールの割り当てを行うことができます。 こうしたロールの割り当ては、[Azure portal](../roles/permissions-reference.md) または [PowerShell のコマンド](/powershell/module/azuread#directory_roles) を使用して作成できます。

永続的な管理ロールの割り当ては、特権ロール管理者が Azure AD Privileged Identity Management (PIM) サービスを使用して行うこともできます。 さらに、特権ロール管理者は、ユーザーを Azure AD 管理者ロールの **候補** にすることもできます。 管理者候補は必要なときにロールをアクティブ化できます。作業が完了すると、そのアクセス許可は期限切れになります。

## <a name="determine-your-version-of-pim"></a>PIM のバージョンを判断する

2019 年 11 月以降、Privileged Identity Management の Azure AD ロール部分は、Azure リソース ロールのエクスペリエンスと一致する新しいバージョンに更新されます。 これによって機能が追加され、[既存の API の変更](azure-ad-roles-features.md#api-changes)が行われます。 新しいバージョンのロールアウト中、この記事で実行する手順は、現在お使いになっている Privileged Identity Management のバージョンによって異なります。 このセクションの手順に従って、お使いになっている Privileged Identity Management のバージョンを確認してください。 Privileged Identity Management のバージョンを確認したら、この記事に記載されている手順のうち、そのバージョンに一致するものを選択することができます。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)のロールであるユーザーで [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。 概要ページの上部にバナーが表示されている場合は、この記事の **[新しいバージョン]** タブの指示に従ってください。 それ以外の場合は、 **[以前のバージョン]** タブの指示に従ってください。

  [![[Azure AD] > [Privileged Identity Management] を選択する](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新しいバージョン](#tab/new)

## <a name="assign-a-role"></a>ロールの割り当て

ユーザーを Azure AD 管理者ロールの候補にするには、次の手順を実行します。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

    Privileged Identity Management を管理するためのアクセス権を別の管理者に付与する方法については、「[Privileged Identity Management を管理する他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)」を参照してください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[ロール]** を選択して、Azure AD のアクセス許可のロール一覧を表示します。

    ![[割り当ての追加] 操作が選択されている [ロール] ページのスクリーンショット。](./media/pim-how-to-add-role-to-user/roles-list.png)

1. **[割り当ての追加]** を選択して、 **[割り当ての追加]** ページを開きます。

1. **[ロールを選択]** を選択して **[ロールを選択]** ページを開きます。

    ![[新しい割り当て] ウィンドウ](./media/pim-how-to-add-role-to-user/select-role.png)

1. 割り当てるロールを選択し、ロールを割り当てるメンバーを選択してから、 **[次へ]** を選択します。

1. **[メンバーシップ設定]** ウィンドウの **[割り当ての種類]** リストで、 **[対象]** または **[アクティブ]** を選択します。

    - **[対象]** 割り当ての場合、このロールのメンバーは、ロールを使用するにはアクションを実行する必要があります。 要求されるアクションには、多要素認証 (MFA) チェックの実行、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。

    - **[アクティブ]** 割り当ての場合、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブ割り当てされたメンバーは、ロールによって提供される特権を常に所有します。

1. 特定の割り当て期間を指定するには、開始日時と終了日時のボックスを追加します。 完了したら、 **[割り当て]** を選択して、新しいロールの割り当てを作成します。

    ![メンバー シップ設定 - 日付と時刻](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. ロールが割り当てられると、割り当て状態の通知が表示されます。

    ![新しい割り当て - 通知](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>制限スコープがあるロールを割り当てる

特定のロールでは、付与されるアクセス許可の範囲を 1 つの管理単位、サービス プリンシパル、またはアプリケーションに制限することができます。 この手順は、管理単位のスコープを持つロールを割り当てる場合の例です。 管理単位を介してスコープをサポートするロールの一覧については、「[スコープ付きロールを管理単位に割り当てる](../roles/admin-units-assign-roles.md)」を参照してください。 この機能は、現在 Azure AD 組織に展開されています。

1. 特権ロール管理者アクセス許可を使用して、[Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択します。

1. **[ユーザー管理者]** を選択します。

    ![ポータルでロールを開くと、[割り当ての追加] コマンドを使用できます](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. **[割り当ての追加]** を選択します。

    ![ロールがスコープをサポートしている場合は、スコープを選択できます](./media/pim-how-to-add-role-to-user/add-scope.png)

1. **[割り当ての追加]** ページでは、次のことができます。

   - ロールに割り当てるユーザーまたはグループを選択する
   - ロールのスコープ (この場合は管理単位) を選択する
   - スコープの管理単位を選択する

管理単位の作成の詳細については、[管理単位の追加と削除](../roles/admin-units-manage.md)に関するページを参照してください。

## <a name="update-or-remove-an-existing-role-assignment"></a>既存のロールの割り当てを更新または削除する

既存のロールの割り当てを更新または削除するには、次の手順を実行します。 **Azure AD P2 ライセンス供与されたお客様のみ**:Azure AD と Privileged Identity Management (PIM) の両方を使用して、グループをアクティブとしてロールに割り当てないでください。 詳細については、「[既知の問題](../roles/groups-concept.md#known-issues)」を参照してください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[ロール]** を選択して、Azure AD のロール一覧を表示します。

1. 更新または削除するロールを選択します。

1. **[資格のあるロール]** タブまたは **[アクティブなロール]** タブでロールの割り当てを見つけます。

    ![ロールの割り当ての更新または削除](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. **[更新]** または **[削除]** を選択して、ロールの割り当てを更新または削除します。

# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>ユーザーをロールの候補にする

ユーザーを Azure AD 管理者ロールの候補にするには、次の手順を実行します。

1. **[ロール]** または **[メンバー]** をクリックします。

    ![Azure AD ロールを開く](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **[メンバーの追加]** を選択して **[管理されるメンバーの追加]** を開きます。

1. **[Select a role]\(ロールを選択する\)** をクリックして、管理するロールをクリックし、 **[選択]** をクリックします。

    ![ロールを選択する](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **[メンバーの選択]** を選択してロールに割り当てるユーザーを選択し、 **[選択]** を選択します。

    ![割り当てるユーザーまたはグループを選択します](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. **[管理されるメンバーの追加]** で **[OK]** を選択し、ロールにユーザーを追加します。

1. ロールの一覧で、割り当てたロールを選択してメンバーの一覧を表示します。

     ロールが割り当てられると、選択したユーザーが、ロールの **候補** としてメンバー リストに表示されます。

    ![ロールの候補となるユーザー](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. これで、そのユーザーがロールの対象となりました。ユーザーに、[Privileged Identity Management での自分の Azure AD ロールのアクティブ化](pim-how-to-activate-role.md)に関するページの手順に従ってロールをアクティブ化できることを知らせてください。

    臨時管理者には、アクティブ化時に Azure AD Multi-Factor Authentication への登録が求められます。 ユーザーが MFA に登録できない場合、または Microsoft アカウント (@outlook.com など) を使用している場合は、すべてのロールでこのユーザーを永続化する必要があります。

## <a name="make-a-role-assignment-permanent"></a>ロールの割り当てを永続化する

既定では、新しいユーザーは、あくまで Azure AD 管理者ロールの *候補* でしかありません。 ロールの割り当てを永続化する場合は、次の手順に従ってください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[メンバー]** を選択します。

    ![メンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 永続化する **候補** ロールを選択します。

1. **[その他]** を選択し、 **[永続化]** を選択します。

    ![ロールの割り当てを永続化する](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    ロールが **永続** として表示されています。

    ![永続化されたメンバーの一覧が変化](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>ユーザーをロールから削除する

ロールの割り当てからユーザーを削除することはできますが、必ず、永続的な全体管理者が少なくとも 1 人は常に存在するようにしてください。 どのユーザーに引き続きロールの割り当てが必要であるかわからない場合は、[ロールのアクセス レビューを開始](pim-how-to-start-security-review.md)してください。

特定のユーザーを Azure AD 管理者ロールから削除するには、次の手順に従います。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[メンバー]** を選択します。

    ![メンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 削除するロールの割り当てを選択します。

1. **[その他]** を選択し、 **[削除]** を選択します。

    ![ロールの削除](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 確認を求めるメッセージで **[はい]** を選択します。

    ![削除を確認します](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    ロールの割り当てが削除されました。

## <a name="authorization-error-when-assigning-roles"></a>ロールの割り当て時の承認エラー

サブスクリプション用に Privileged Identity Management を最近有効にしており、ユーザーを Azure AD 管理者ロールの対象にしようとすると承認エラーが表示される場合は、MS-PIM サービス プリンシパルがまだ適切なアクセス許可を持っていないためである可能性があります。 MS PIM サービス プリンシパルには、他のユーザーにロールを割り当てるための[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールが必要です。 MS PIM にユーザー アクセス管理者ロールが割り当てられるまで待つ代わりに、それを手動で割り当てることができます。

以下の手順に従って、MS PIM サービス プリンシパルにサブスクリプション用のユーザー アクセス管理者ロールを割り当てます。

1. Azure Portal にグローバル管理者としてサインインします。

1. **[すべてのサービス]** 、 **[サブスクリプション]** の順に選択します。

1. サブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択します。

1. **[ロールの割り当て]** を選択すると、サブスクリプションをスコープとするロールの割り当ての現在の一覧が表示されます。

   ![サブスクリプションの [アクセス制御 (IAM)] ブレード](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. **MS PIM** サービス プリンシパルに **ユーザー アクセス管理者** ロールが割り当てられているかどうかを確認します。

1. そうなっていない場合、 **[ロールの割り当ての追加]** を選択して、 **[ロールの割り当ての追加]** ウィンドウを開きます。

1. **[ロール]** ドロップダウン リストで、 **[ユーザー アクセス管理者]** ロールを選択します。

1. **[選択]** リストで、 **[MS PIM]** サービス プリンシパルを見つけて選択します。

   ![[ロールの割り当ての追加] ウィンドウ - MS-PIM サービス プリンシパルにアクセス許可を追加する](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. **[保存]** を選択して、ロールを割り当てます。

   しばらくすると、MS PIM サービス プリンシパルに、サブスクリプション スコープでユーザー アクセス管理者ロールが割り当てられます。

   ![MS-PIM サービス プリンシパルに対するユーザー アクセス管理者ロールの割り当てを示すアクセス制御ページ](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD 管理者ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [Privileged Identity Management で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)