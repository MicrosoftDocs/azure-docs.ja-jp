---
title: PIM で Azure リソース ロールをアクティブ化する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールをアクティブにする方法を説明します。
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
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501666"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>PIM で自分の Azure リソース ロールをアクティブにする

Azure リソースの適格なロール メンバーは、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、将来の日時のアクティブ化をスケジュールできます。 最大範囲 (管理者が設定) 内で特定のアクティブ化期間を選択することもできます｡

この記事は、PIM で Azure リソース ロールをアクティブにする必要があるメンバー向けです。

## <a name="activate-a-role"></a>ロールのアクティブ化

Azure リソース ロールが必要な場合は、PIM の **[自分のロール]** ナビゲーション オプションを使用してアクティブ化を要求できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。 ダッシュボードに PIM タイルを追加する方法については、「[PIM の使用を開始する](pim-getting-started.md)」をご覧ください。

1. **[自分のロール]** をクリックします。

    ![アクティブ化できるロールを表示した [自分のロール] ページ](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. **[Azure リソース ロール]** をクリックして、適格な Azure リソース ロールの一覧を表示します。

   ![[自分のロール - Azure リソース ロール] ページ](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. **[Azure リソース ロール]** の一覧で、アクティブにするロールを見つけます。

    ![[Azure リソース ロール] - 自分の [資格のあるロール] の一覧](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. **[アクティブ化]** をクリックして、[アクティブ化] ウィンドウを開きます。

1. お使いのロールで多要素認証 (MFA) が必要な場合は、 **[続行する前に ID を確認してください]** をクリックします。 認証は、セッションごとに 1 回だけ行う必要があります。

    ![ロールをアクティブ化する前に MFA で自分の ID を確認する](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **[ID を確認]** をクリックし、指示に従って追加のセキュリティ確認を提供します。

    ![PIN コードなどのセキュリティ確認を提供するための画面](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. より狭いスコープを指定する場合は、 **[Scope]\(スコープ\)** をクリックして [リソース フィルター] ウィンドウを開きます。

    ベスト プラクティスは、必要なリソースへのアクセスのみを要求することです。 [リソース フィルター] ウィンドウでは、アクセスする必要があるリソース グループまたはリソースを指定できます。

    ![[アクティブ化] - スコープを指定する [リソース フィルター] ウィンドウ](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。 メンバーは、選択した時刻になるとアクティブになります。

1. **[理由]** ボックスに、アクティブ化要求の理由を入力します。

    ![スコープ、開始時刻、期間、理由が入力された [アクティブ化] ウィンドウ](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **[アクティブ化]** をクリックします。

    承認が必要ないロールは、アクティブ化され、アクティブなロールの一覧に追加されます。 ロールを使用する場合は、次のセクションの手順に従います。

    アクティブ化に[承認が必要なロール](pim-resource-roles-approval-workflow.md)の場合は、ブラウザーの右上隅に通知が表示され、承認待ちになっていることが示されます。

    ![アクティブ化要求は承認通知待ち中](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>アクティブ化後すぐにロールを使用する

アクティブ化の後の任意の遅延が発生した場合、Azure リソース ロールをすぐに使用するようにアクティブ化した後に、次の手順に従います。

1. Azure AD Privileged Identity Management を開きます。

1. **[自分のロール]** をクリックして、適格な Azure AD ロールおよび Azure リソース ロールの一覧を表示します。

1. **[Azure リソース ロール]** をクリックします。

1. **[アクティブなロール]** タブをクリックします。

1. ロールがアクティブになった場合、ポータルからサインアウトしてもう一度サインインします。

    これでロールは使用可能になります。

## <a name="view-the-status-of-your-requests"></a>要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[個人の要求]** をクリックして、Azure AD ロールおよび Azure リソース ロール要求の一覧を表示します。

    ![承認待ち中の要求を表示した [個人の要求 - Azure リソース] ページ](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 右へスクロールして **[Request Status]\(要求の状態\)** 列を表示します。

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[個人の要求]** をクリックします。

1. 取り消すロールの **[キャンセル]** リンクをクリックします。

    [キャンセル] をクリックすると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

   ![[キャンセル] アクションが強調表示された個人の要求の一覧](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="permissions-are-not-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

PIM でロールをアクティブにしても、アクティブ化は特権ロールを必要とするすべてのポータルにすぐに反映されない場合があります。 場合によっては、変更が反映されても、ポータルにおける Web キャッシュが原因で、変更がすぐに有効にならないことがあります。 アクティブ化が遅延している場合は、次の手順を行ってください。

1. Azure portal からサインアウトした後に、もう一度サインインします。

    Azure リソース ロールをアクティブにすると、アクティブ化の各ステージが順次表示されます。 すべてのステージが完了すると、 **[サインアウト]** リンクが表示されます。 このリンクを使用してサインアウトできます。これにより、ほとんどの場合、アクティブ化の遅延は解決します。

1. PIM で自分がロールのメンバーとして表示されていることを確認します。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [PIM で Azure AD ロールをアクティブ化する](pim-how-to-activate-role.md)
