---
title: PIM で Azure AD ロールをアクティブ化する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールをアクティブにする方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb1041cb5fc4aa7503f5ae9de6ffc74ba72052b8
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421401"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールをアクティブ化する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用すると、企業における Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへの特権アクセスの管理が簡略化されます。  

管理ロールの候補者となっている場合は、特権が必要な操作を実行する必要があるときに、ロールの割り当てをアクティブにできます。 たとえば、Office 365 の機能をときどきしか管理しないユーザーは、組織の特権ロール管理者によって永続的なグローバル管理者には設定されない可能性があります。このロールは他のサービスにも影響を与えるからです。 その代わりに、Exchange Online 管理者などの Azure AD ロールが割り当てられます。 このような権限が必要な場合には、ロールをアクティブ化することを要求できます。それにより、事前に定義された期間だけ管理者権限が付与されます。

この記事は、Privileged Identity Management で自分の Azure AD ロールをアクティブ化する必要のある管理者を対象としています。

## <a name="determine-your-version-of-pim"></a>PIM のバージョンを判断する

2019 年 11 月以降、Privileged Identity Management の Azure AD ロール部分は、Azure リソース ロールのエクスペリエンスと一致する新しいバージョンに更新されます。 これによって機能が追加され、[既存の API の変更](azure-ad-roles-features.md#api-changes)が行われます。 新しいバージョンのロールアウト中、この記事で実行する手順は、現在お使いになっている Privileged Identity Management のバージョンによって異なります。 このセクションの手順に従って、お使いになっている Privileged Identity Management のバージョンを確認してください。 Privileged Identity Management のバージョンを確認したら、この記事に記載されている手順のうち、そのバージョンに一致するものを選択することができます。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールで [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。 概要ページの上部にバナーが表示されている場合は、この記事の **[新しいバージョン]** タブの指示に従ってください。 それ以外の場合は、 **[以前のバージョン]** タブの指示に従ってください。

    [![[Azure AD] > [Privileged Identity Management] を選択する](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新しいバージョン](#tab/new)

## <a name="activate-a-role"></a>ロールのアクティブ化

Azure AD ロールを想定する必要がある場合は、Privileged Identity Management で **[自分のロール]** を開いてアクティブ化を要求できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。 ダッシュボードに [Privileged Identity Management] タイルを追加する方法については、「[Privileged Identity Management の使用開始](pim-getting-started.md)」を参照してください。

1. **[自分のロール]** を選択し、 **[Azure AD ロール]** を選択して、資格がある Azure AD ロールの一覧を表示します。

    ![アクティブ化できるロールを表示した [自分のロール] ページ](./media/pim-how-to-activate-role/my-roles.png)

1. **[Azure AD ロール]** の一覧で、アクティブにするロールを見つけます。

    ![[Azure AD ロール] - 資格があるロールの一覧](./media/pim-how-to-activate-role/activate-link.png)

1. **[アクティブ化]** を選択して、[アクティブ化] ページを開きます。

    ![期間とスコープを含む Azure AD ロールのアクティブ化ページ](./media/pim-how-to-activate-role/activate-page.png)

1. お使いのロールで多要素認証が必要な場合は、 **[続行する前に ID を確認してください]** をクリックします。 認証は、セッションごとに 1 回だけ行う必要があります。

    ![ロールをアクティブ化する前に MFA で自分の ID を確認する](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **[ID を確認]** を選択し、指示に従って追加のセキュリティ確認を提供します。

    ![PIN コードなどのセキュリティ確認を提供するための画面](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. より狭いスコープを指定する場合は、 **[Scope]\(スコープ\)** を選択して [フィルター] ペインを開きます。 [フィルター] ペインでは、アクセス権が必要な Azure AD リソースを指定できます。 必要なリソースに対するアクセスのみを要求することをお勧めします。

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。 Azure AD ロールは、選択した時刻になるとアクティブになります。

1. **[理由]** ボックスに、アクティブ化要求の理由を入力します。

1. **[アクティブ化]** を選びます。

    アクティブ化に[承認が必要なロール](pim-resource-roles-approval-workflow.md)の場合は、ブラウザーの右上隅に通知が表示され、承認待ちになっていることが示されます。

    ![アクティブ化要求は承認通知待ち中](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[My requests]\(個人の要求\)** を選択して、Azure AD ロールおよび Azure リソース ロール要求の一覧を表示します。

    ![承認待ち中の要求を表示している [個人の要求 - Azure AD] ページ](./media/pim-how-to-activate-role/my-requests-page.png)

1. 右へスクロールして **[Request Status]\(要求の状態\)** 列を表示します。

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[My requests]\(個人の要求\)** を選択します。

1. 取り消すロールの **[キャンセル]** リンクを選択します。

    [キャンセル] を選択すると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

   ![[キャンセル] アクションが強調表示された個人の要求の一覧](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="permissions-are-not-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

Privileged Identity Management でロールをアクティブ化しても、アクティブ化が、特権ロールを必要とするすべてのポータルに直ちには伝播されない可能性があります。 場合によっては、変更が伝達されても、ポータルにおける Web キャッシュが原因で、変更がすぐに有効にならないことがあります。 アクティブ化が遅延している場合は、次の手順を行ってください。

1. Azure portal からサインアウトした後に、もう一度サインインします。

1. Privileged Identity Management で、自分がそのロールのメンバーとして一覧表示されていることを確認します。

# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

## <a name="activate-a-role"></a>ロールのアクティブ化

Azure AD ロールを引き受ける必要がある場合は、Privileged Identity Management の **[自分のロール]** ナビゲーション オプションを使用してアクティブ化を要求できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。 ダッシュボードに [Privileged Identity Management] タイルを追加する方法については、「[Privileged Identity Management の使用開始](pim-getting-started.md)」を参照してください。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[自分のロール]** をクリックして、対象の Azure AD ロールの一覧を表示します。

    ![資格のあるロールまたはアクティブなロールの一覧を表示した [Azure AD ロール - 自分のロール]](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. アクティブにするロールを探します。

    ![アクティブ化リンクを示す [Azure AD ロール - 自分のロール] 一覧](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. **[アクティブ化]** をクリックして、[ロールのアクティブ化の詳細] ウィンドウを開きます。

1. お使いのロールで多要素認証 (MFA) が必要な場合は、 **[続行する前に ID を確認してください]** をクリックします。 認証は、セッションごとに 1 回だけ行う必要があります。

    ![ロールをアクティブ化する前に MFA で自分の ID ウィンドウを確認する](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. **[ID を確認]** をクリックし、指示に従って追加のセキュリティ確認を提供します。

    ![連絡方法を確認する追加のセキュリティ確認ページ](./media/pim-how-to-activate-role/additional-security-verification.png)

1. **[アクティブ化]** をクリックして、[アクティブ化] ウィンドウを開きます。

    ![開始時刻、期間、チケット、理由を指定する [アクティブ化] ウィンドウ](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。

1. アクティブ化の期間を指定します。

1. **[アクティブ化の理由]** ボックスに、アクティブ化要求の理由を入力します。 一部のロールでは、トラブル チケット番号を入力するように求められます。

    ![カスタムの開始時刻、期間、チケット、理由を入力した [アクティブ化] ウィンドウ](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. **[アクティブ化]** をクリックします。

    ロールに承認が必要ない場合、 **[アクティブ化の状態]** ウィンドウが表示され、アクティブ化の状態が示されます。

    ![アクティブ化の 3 つのステージを表示した [アクティブ化の状態] ページ](./media/pim-how-to-activate-role/activation-status.png)

    すべてのステージが完了したら、 **[サインアウト]** リンクをクリックして、Azure portal からサインアウトします。 ポータルにもう一度サインインすると、ロールを使用できるようになります。

    アクティブ化するために[ロールで承認が必要](./azure-ad-pim-approval-workflow.md)な場合は、ブラウザーの右上隅に Azure の通知が表示され、要求が承認待ちになっていることが示されます。

## <a name="view-the-status-of-your-requests"></a>要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[個人の要求]** をクリックして、要求の一覧を表示します。

    ![[Azure AD ロール - 個人の要求]](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>ロールの非アクティブ化

ロールは、アクティブになった後、期限 (資格のある期間) に達すると自動的に非アクティブになります。

管理者タスクを早期に終了した場合は、Azure AD Privileged Identity Management でロールを手動で非アクティブにすることもできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[自分のロール]** をクリックします。

1. **[アクティブなロール]** をクリックして、アクティブなロールの一覧を表示します。

1. 使用が終了したロールを探し、 **[非アクティブ化]** をクリックします。

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[個人の要求]** をクリックします。

1. キャンセルするロールの **[キャンセル]** ボタンをクリックします。

    [キャンセル] をクリックすると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

   ![[キャンセル] ボタンが強調表示された個人の要求の一覧](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="permissions-are-not-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

Privileged Identity Management でロールをアクティブ化しても、アクティブ化が、特権ロールを必要とするすべてのポータルに直ちには伝播されない可能性があります。 場合によっては、変更が伝達されても、ポータルにおける Web キャッシュが原因で、変更がすぐに有効にならないことがあります。 アクティブ化が遅延している場合は、次の手順を行ってください。

1. Azure portal からサインアウトした後に、もう一度サインインします。

    Azure AD ロールをアクティブにすると、アクティブ化の各ステージが順次表示されます。 すべてのステージが完了すると、 **[サインアウト]** リンクが表示されます。 このリンクを使用してサインアウトできます。これにより、ほとんどの場合、アクティブ化の遅延は解決します。

1. Privileged Identity Management で、自分がそのロールのメンバーとして一覧表示されていることを確認します。

 ---

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で自分の Azure AD ロールをアクティブ化する](pim-how-to-activate-role.md)
