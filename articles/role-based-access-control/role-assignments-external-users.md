---
title: RBAC を使用して外部ゲスト ユーザーの Azure リソースへのアクセスを管理する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) を使用して、組織外のユーザーの Azure リソースへのアクセスを管理する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 09/12/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 12f4b0276074b6732cf57443f51ef5d867f205a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967329"
---
# <a name="manage-access-to-azure-resources-for-external-guest-users-using-rbac"></a>RBAC を使用して外部ゲスト ユーザーの Azure リソースへのアクセスを管理する

ロールベースのアクセス制御 (RBAC) では、環境内の特定のリソースへのアクセスが必要なものの、インフラストラクチャ全体や課金に関連するスコープへのアクセスが必ずしも必要ではない外部のコラボレーター、ベンダー、フリーランサーと連携している大企業や中小企業向けの、優れたセキュリティ管理を実現できます。 [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) の機能を使用して外部ゲスト ユーザーと共同作業を行うことができます。また、RBAC を使用して、自分の環境でゲスト ユーザーに必要なアクセス許可のみを付与することができます。

## <a name="when-would-you-invite-guest-users"></a>ゲスト ユーザーを招待するタイミング

ここでは、ゲスト ユーザーを組織に招待し、アクセス許可を付与するシナリオ例をいくつか示します。

- プロジェクトの Azure リソースにアクセスするために、電子メール アカウントしか持っていない外部の自営仕入先を許可します。
- 外部パートナーが特定のリソースまたはサブスクリプション全体を管理することを許可します。
- 組織外のサポート エンジニア (Microsoft サポートなど) に、問題をトラブルシューティングしてもらうために Azure リソースに一時的にアクセスすることを許可します。

## <a name="permission-differences-between-member-users-and-guest-users"></a>メンバー ユーザーとゲスト ユーザーのアクセス許可の違い

ディレクトリのネイティブ メンバー (メンバー ユーザー) は、B2B コラボレーション ゲスト (ゲスト ユーザー) として別のディレクトリから招待されたユーザーとは異なるアクセス許可を持ちます。 たとえば、メンバー ユーザーはほとんどすべてのディレクトリ情報を読み取ることができますが、ゲスト ユーザーにはディレクトリのアクセス許可が制限されています。 メンバー ユーザーとゲスト ユーザーの詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../active-directory/fundamentals/users-default-permissions.md)」を参照してください。

## <a name="add-a-guest-user-to-your-directory"></a>ゲスト ユーザーをディレクトリに追加する

Azure Active Directory ページを使用してディレクトリにゲスト ユーザーを追加するには、次の手順を実行します。

1. 組織の外部コラボレーション設定が、ゲストを招待できるように構成されていることを確認します。 詳細については、「[B2B 外部コラボレーションを有効にしてゲストを招待できるユーザーを管理する](../active-directory/b2b/delegate-invitations.md)」を参照してください。

1. Azure portal で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[新しいゲスト ユーザー]** の順にクリックします。

    ![Azure portal の [新しいゲスト ユーザー] 機能](./media/role-assignments-external-users/invite-guest-user.png)

1. 手順に従って新しいゲスト ユーザーを追加します。 詳細については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)」を参照してください。

ゲスト ユーザーをディレクトリに追加すると、共有アプリへの直接リンクをゲスト ユーザーに送信するか、ゲスト ユーザーが招待メール内の引き換えの URL をクリックできます。

![ゲスト ユーザーの招待メール](./media/role-assignments-external-users/invite-email.png)

ゲスト ユーザーがディレクトリにアクセスできるようになるには、招待プロセスを完了する必要があります。

![ゲスト ユーザーの招待レビューのアクセス許可](./media/role-assignments-external-users/invite-review-permissions.png)

招待プロセスの詳細については、「[Azure Active Directory B2B コラボレーションの招待の利用](../active-directory/b2b/redemption-experience.md)」を参照してください。

## <a name="grant-access-to-a-guest-user"></a>ゲスト ユーザーにアクセス権を付与する

RBAC でアクセス権を付与するには、ロールを割り当てます。 ゲスト ユーザーにアクセス権を付与するには、メンバー ユーザー、グループ、サービス プリンシパル、またはマネージド ID の場合と[同じ手順](role-assignments-portal.md#add-a-role-assignment)を実行します。 次の手順に従って、異なるスコープのゲスト ユーザーにアクセス権を付与します。

1. Azure Portal で、 **[すべてのサービス]** をクリックします。

1.  アクセスが適用されるリソースのセット (スコープとも呼ばれます) を選択します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

    次のスクリーンショットは、リソース グループのアクセス制御 (IAM) ブレードの例を示しています。 ここでアクセス制御を変更すると、リソース グループにのみ適用されます。

    ![リソース グループの [アクセス制御 (IAM)] ブレード](./media/role-assignments-external-users/access-control-resource-group.png)

1. **[ロールの割り当て]** タブをクリックして、このスコープのすべてのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックして、[ロールの割り当ての追加] ウィンドウを開きます。

    ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

    ![[追加] メニュー](./media/role-assignments-external-users/add-menu.png)

1. **[ロール]** ボックスの一覧で、 **[仮想マシン共同作成者]** などのロールを選択します。

1. **[選択]** 一覧で、ゲスト ユーザーを選択します。 一覧にユーザーが表示されない場合には、 **[選択]** ボックスに表示名、メール アドレス、オブジェクト識別子を入力してディレクトリを検索します。

   ![[ロールの割り当ての追加] ウィンドウ](./media/role-assignments-external-users/add-role-assignment.png)

1. **[保存]** をクリックして、選択したスコープでロールを割り当てます。

    ![仮想マシン共同作成者のロールの割り当て](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="grant-access-to-a-guest-user-not-yet-in-your-directory"></a>ディレクトリにまだ存在しないゲスト ユーザーにアクセス権を付与する

RBAC でアクセス権を付与するには、ロールを割り当てます。 ゲスト ユーザーにアクセス権を付与するには、メンバー ユーザー、グループ、サービス プリンシパル、またはマネージド ID の場合と[同じ手順](role-assignments-portal.md#add-a-role-assignment)を実行します。

ゲスト ユーザーがディレクトリにまだ存在しない場合は、[ロールの割り当ての追加] ウィンドウからユーザーを直接招待できます。

1. Azure Portal で、 **[すべてのサービス]** をクリックします。

1.  アクセスが適用されるリソースのセット (スコープとも呼ばれます) を選択します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このスコープのすべてのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックして、[ロールの割り当ての追加] ウィンドウを開きます。

    ![[追加] メニュー](./media/role-assignments-external-users/add-menu.png)

1. **[ロール]** ボックスの一覧で、 **[仮想マシン共同作成者]** などのロールを選択します。

1. **[選択]** 一覧に、招待する相手のメール アドレスを入力し、その人物を選択します。

   ![[ロールの割り当ての追加] ウィンドウでゲスト ユーザーを招待する](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. **[保存]** をクリックしてゲスト ユーザーをディレクトリに追加し、ロールを割り当てて、招待を送信します。

    しばらくすると、ロールの割り当ての通知と、招待に関する情報が表示されます。

    ![ロールの割り当てと招待されたユーザーへの通知](./media/role-assignments-external-users/invited-user-notification.png)

1. ゲスト ユーザーを手動で招待するには、右クリックして、通知の招待リンクをコピーします。 招待プロセスを開始するため、招待リンクをクリックしないでください。

    招待リンクの形式は次のとおりです。

    `https://invitations.microsoft.com/redeem/...`

1. ゲスト ユーザーに招待リンクを送信して招待プロセスを完了します。

    招待プロセスの詳細については、「[Azure Active Directory B2B コラボレーションの招待の利用](../active-directory/b2b/redemption-experience.md)」を参照してください。

## <a name="remove-a-guest-user-from-your-directory"></a>ディレクトリからゲスト ユーザーを削除する

ディレクトリからゲスト ユーザーを削除する前に、まずそのゲスト ユーザーのすべてのロールの割り当てを削除する必要があります。 ディレクトリからゲスト ユーザーを削除するには、次の手順を実行します。

1. ゲスト ユーザーにロールが割り当てられている管理グループ、サブスクリプション、リソース グループ、リソースなどのスコープで**アクセス制御 (IAM)** を開きます。

1. **[ロールの割り当て]** タブをクリックして、すべてのロールの割り当てを表示します。

1. ロールの割り当ての一覧で、ロールの割り当てを削除するゲスト ユーザーの隣にチェックマークを追加します。

   ![ロールの割り当てを削除する](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. **[削除]** をクリックします。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-external-users/remove-role-assignment.png)

1. 表示されるロールの割り当ての削除メッセージで、 **[はい]** をクリックします。

1. 左側のナビゲーション バーで、 **[Azure Active Directory]**  >  **[ユーザー]** をクリックします。

1. 削除するゲスト ユーザーをクリックします。

1. **[削除]** をクリックします。

   ![ゲスト ユーザーを削除する](./media/role-assignments-external-users/delete-guest-user.png)

1. 表示される削除メッセージで、 **[はい]** をクリックします。

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="guest-user-cannot-browse-the-directory"></a>ゲスト ユーザーがディレクトリを参照できません

ゲスト ユーザーは、ディレクトリ アクセス許可を制限されています。 たとえば、ゲスト ユーザーはディレクトリを参照することができず、グループやアプリケーションを検索することができません。 詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../active-directory/fundamentals/users-default-permissions.md)」を参照してください。

![ゲスト ユーザーがディレクトリ内のユーザーを参照できません](./media/role-assignments-external-users/directory-no-users.png)

ゲスト ユーザーがディレクトリで追加の特権を必要とする場合は、ゲスト ユーザーにディレクトリ ロールを割り当てることができます。 ゲスト ユーザーにディレクトリへのフル読み取りアクセス権を付与する場合は、Azure AD の[ディレクトリ閲覧者](../active-directory/users-groups-roles/directory-assign-admin-roles.md)ロールにゲスト ユーザーを追加できます。 詳細については、「[Azure Active Directory テナントでパートナー組織からユーザーにアクセス許可を付与する](../active-directory/b2b/add-guest-to-role.md)」を参照してください。

![ディレクトリ閲覧者ロールの割り当て](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>ゲスト ユーザーは、ユーザー、グループ、またはサービス プリンシパルを参照してロールを割り当てることはできません

ゲスト ユーザーは、ディレクトリ アクセス許可を制限されています。 ゲスト ユーザーがスコープの[所有者](built-in-roles.md#owner)であっても、ロールの割り当てを作成して他のユーザーにアクセス権を付与しようとした場合に、ユーザー、グループ、またはサービス プリンシパルの一覧を参照できません。

![ゲスト ユーザーがセキュリティ プリンシパルを参照してロールを割り当てられません](./media/role-assignments-external-users/directory-no-browse.png)

ゲスト ユーザーは、ディレクトリ内のユーザーの正確なサインイン名を知っていれば、アクセス権を付与することができます。 ゲスト ユーザーにディレクトリへのフル読み取りアクセス権を付与する場合は、Azure AD の[ディレクトリ閲覧者](../active-directory/users-groups-roles/directory-assign-admin-roles.md)ロールにゲスト ユーザーを追加できます。 詳細については、「[Azure Active Directory テナントでパートナー組織からユーザーにアクセス許可を付与する](../active-directory/b2b/add-guest-to-role.md)」を参照してください。

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>ゲスト ユーザーがアプリケーションを登録できません、またはサービス プリンシパルを作成できません

ゲスト ユーザーは、ディレクトリ アクセス許可を制限されています。 ゲスト ユーザーがアプリケーションを登録できる、またはサービス プリンシパルを作成できる必要がある場合は、Azure AD の[アプリケーション開発者](../active-directory/users-groups-roles/directory-assign-admin-roles.md)ロールにゲスト ユーザーを追加します。 詳細については、「[Azure Active Directory テナントでパートナー組織からユーザーにアクセス許可を付与する](../active-directory/b2b/add-guest-to-role.md)」を参照してください。

![ゲスト ユーザーがアプリケーションを登録できません](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>ゲスト ユーザーに新しいディレクトリが表示されません

ゲスト ユーザーにディレクトリへのアクセス権が付与されていても、 **[ディレクトリ + サブスクリプション]** ウィンドウで切り替えようとしたときに Azure portal の一覧に新しいディレクトリが表示されない場合は、ゲスト ユーザーが招待プロセスを完了していることを確認してください。 招待プロセスの詳細については、「[Azure Active Directory B2B コラボレーションの招待の利用](../active-directory/b2b/redemption-experience.md)」を参照してください。

### <a name="guest-user-does-not-see-resources"></a>ゲスト ユーザーにリソースが表示されません

ゲスト ユーザーにディレクトリへのアクセスが許可されていても、Azure portal でアクセス権を付与されているリソースが表示されない場合は、ゲスト ユーザーが正しいディレクトリを選択していることを確認してください。 ゲスト ユーザーは、複数のディレクトリにアクセス権を持つ場合があります。 ディレクトリを切り替えるには、左上にある **[ディレクトリ + サブスクリプション]** をクリックし、適切なディレクトリをクリックします。

![Azure portal の [ディレクトリ + サブスクリプション] ウィンドウ](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>次の手順

- [Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/b2b/add-users-administrator.md)
- [Azure Active Directory B2B コラボレーション ユーザーのプロパティ](../active-directory/b2b/user-properties.md)
- [B2B コラボレーションの招待メールの要素 - Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)