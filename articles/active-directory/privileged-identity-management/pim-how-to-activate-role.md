---
title: PIM で Azure AD ロールをアクティブ化する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールをアクティブにする方法について説明します。
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
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa820d6c140251fce6b09110e65b45005b53afcc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59488441"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールをアクティブ化する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用すると、企業における Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへの特権アクセスの管理が簡略化されます。  

管理ロールの候補者となっている場合は、特権の必要な操作を実行する必要のあるときにロールをアクティブにできます。 たとえば、Office 365 の機能をときどきしか管理しないユーザーは、組織の特権ロール管理者によって永続的なグローバル管理者には設定されない可能性があります。このロールは他のサービスにも影響を与えるからです。 その代わりに、Exchange Online 管理者などの Azure AD ロールが割り当てられます。 このような権限が必要な場合には、ロールをアクティブ化することを要求できます。それにより、事前に定義された期間だけ管理者権限が付与されます。

この記事は、PIM で Azure AD ロールをアクティブにする必要がある管理者向けです。

## <a name="activate-a-role"></a>ロールのアクティブ化

Azure AD ロールを有効にする必要がある場合は、PIM の **[自分のロール]** ナビゲーション オプションを使用してアクティブ化を要求できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。 ダッシュボードに PIM タイルを追加する方法については、「[PIM の使用を開始する](pim-getting-started.md)」をご覧ください。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[自分のロール]** をクリックして、対象の Azure AD ロールの一覧を表示します。

    ![Azure AD ロール - [自分のロール]](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. アクティブにするロールを探します。

    ![Azure AD ロール - [自分のロール] 一覧](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. **[アクティブ化]** をクリックして、[ロールのアクティブ化の詳細] ウィンドウを開きます。

1. お使いのロールで多要素認証 (MFA) が必要な場合は、**[続行する前に ID を確認してください]** をクリックします。 認証は、セッションごとに 1 回だけ行う必要があります。

    ![ロール アクティブ化前の MFA の確認](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. **[ID を確認]** をクリックし、指示に従って追加のセキュリティ確認を提供します。

    ![追加のセキュリティ確認](./media/pim-how-to-activate-role/additional-security-verification.png)

1. **[アクティブ化]** をクリックして、[アクティブ化] ウィンドウを開きます。

    ![[アクティブ化] ウィンドウ](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。

1. アクティブ化の期間を指定します。

1. **[アクティブ化の理由]** ボックスに、アクティブ化要求の理由を入力します。 一部のロールでは、トラブル チケット番号を入力するように求められます。

    ![完了した [アクティブ化] ウィンドウ](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. **[アクティブ化]** をクリックします。

    ロールに承認が必要ない場合、**[アクティブ化の状態]** ウィンドウが表示され、アクティブ化の状態が示されます。

    ![アクティブ化の状態](./media/pim-how-to-activate-role/activation-status.png)

    すべてのステージが完了したら、**[サインアウト]** リンクをクリックして、Azure portal からサインアウトします。 ポータルにもう一度サインインすると、ロールを使用できるようになります。

    アクティブ化に[承認が必要なロール](./azure-ad-pim-approval-workflow.md)の場合は、ブラウザーの右上隅に通知が表示され、承認待ちになっていることが示されます。

    ![要求保留の通知](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[個人の要求]** をクリックして、要求の一覧を表示します。

    ![Azure AD ロール - [個人の要求]](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>ロールの非アクティブ化

ロールは、アクティブになった後、期限 (資格のある期間) に達すると自動的に非アクティブになります。

管理者タスクを早期に終了した場合は、Azure AD Privileged Identity Management でロールを手動で非アクティブにすることもできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[自分のロール]** をクリックします。

1. **[アクティブなロール]** をクリックして、アクティブなロールの一覧を表示します。

1. 使用が終了したロールを探し、**[非アクティブ化]** をクリックします。

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[個人の要求]** をクリックします。

1. キャンセルするロールの **[キャンセル]** ボタンをクリックします。

    [キャンセル] をクリックすると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

   ![保留中の要求をキャンセルする](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="permissions-not-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

PIM でロールをアクティブにすると、必要な管理ポータルにアクセスしたり、特定の管理ワークロード内で機能を実行したりできるようになるには、少なくとも 10 分かかります。 アクティブ化が完了したら、Azure portal からサインアウトし、もう一度サインインして、新しくアクティブになったロールの使用を開始します。

追加のトラブルシューティング手順については、[昇格されたアクセス許可のトラブルシューティング](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [PIM で自分の Azure リソース ロールをアクティブにする](pim-resource-roles-activate-your-roles.md)
