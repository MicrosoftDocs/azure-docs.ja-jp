---
title: Azure portal を使用して Azure ロールの割り当てを追加または削除する - Azure RBAC
description: Azure portal と Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 76f4f39e7def192b8cb97c37aefc9f67d82ad4be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362248"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Azure portal を使用して Azure ロールの割り当てを追加または削除する

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] この記事では、Azure portal を使用してロールを割り当てる方法について説明します。

Azure Active Directory で管理者ロールを割り当てる必要がある場合は、「[Azure Active Directory で管理者ロールを表示して割り当てる](../active-directory/users-groups-roles/directory-manage-roles-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

ロールの割り当てを追加または削除するには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)

## <a name="access-control-iam"></a>アクセス制御 (IAM)

**アクセス制御 (IAM)** は、一般的には、ロールを割り当てて Azure リソースへのアクセスを付与するために使用するページです。 ID とアクセス管理とも呼ばれ、Azure portal のいくつかの場所に示されます。 サブスクリプションの [アクセス制御 (IAM)] ページの例を次に示します。

![サブスクリプションの [アクセス制御 (IAM)] ページ](./media/role-assignments-portal/access-control-subscription.png)

[アクセス制御 (IAM)] ページを最も効果的に使用するには、ロールを割り当てる際に、次の 3 つの質問に回答できると役立ちます。

1. **誰がアクセスを必要としていますか?**

    誰が、にあてはまるのはユーザー、グループ、サービス プリンシパル、またはマネージド ID になります。 これは *セキュリティ プリンシパル* とも呼ばれます。

1. **それらには、どのようなロールが必要ですか?**

    アクセス許可はロールでまとめてグループ化されます。 複数の[組み込みロール](built-in-roles.md)の一覧から選択することも、独自のカスタム ロールを使用することもできます。

1. **どこでアクセスが必要ですか?**

    どこで、にあてはまるのはアクセスが適用されるリソースのセットになります。 管理グループ、サブスクリプション、リソース グループ、またはストレージ アカウントなどの単一のリソースである場合があります。 これは*スコープ* と呼ばれます。

## <a name="add-a-role-assignment"></a>ロールの割り当てを追加する

Azure RBAC では、Azure リソースへのアクセス権を付与するために、ロールの割り当てを追加します。 ロールを割り当てるには、次の手順に従います。

1. Azure portal で、 **[すべてのサービス]** をクリックし、アクセス権を付与するスコープを選択します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. そのスコープの特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

    ![[アクセス制御 (IAM)] および [ロールの割り当て] タブ](./media/role-assignments-portal/role-assignments.png)

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。

   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[ロールの割り当ての追加] メニュー](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ウィンドウが開きます。

   ![[ロールの割り当ての追加] ウィンドウ](./media/role-assignments-portal/add-role-assignment.png)

1. **[ロール]**  ボックスの一覧で、 **[仮想マシン共同作成者]**  などのロールを選択します。

1. **[選択]** 一覧で、ユーザー、グループ、サービス プリンシパル、またはマネージド ID を選択します。 [選択] 一覧で、ユーザー、グループ、サービス プリンシパル、またはマネージド ID を選択します。 一覧にセキュリティ プリンシパルが表示されない場合には、 **[選択]**  ボックスに表示名、メール アドレス、オブジェクト識別子を入力してディレクトリを検索します。

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、セキュリティ プリンシパルに選択されたスコープのロールが割り当てられます。

    ![保存されたロールの割り当ての追加](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>サブスクリプションの管理者としてユーザーを割り当てる

ユーザーを Azure サブスクリプションの管理者にするには、サブスクリプションのスコープで、そのユーザーに[所有者](built-in-roles.md#owner)ロールを割り当てます。 所有者ロールにより、他のユーザーにアクセス権を付与する権限を含め、サブスクリプションにあるすべてのリソースへのフル アクセス権がユーザーに付与されます。 次の手順は、他のロールの割り当てと同じです。

1. Azure portal で、 **[すべてのサービス]** 、 **[サブスクリプション]**  の順にクリックします。

1. アクセス権を付与するサブスクリプションをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このサブスクリプションのロールの割り当てを表示します。

    ![[アクセス制御 (IAM)] および [ロールの割り当て] タブ](./media/role-assignments-portal/role-assignments.png)

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。

   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[ロールの割り当ての追加] メニュー](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ウィンドウが開きます。

   ![[ロールの割り当ての追加] ウィンドウ](./media/role-assignments-portal/add-role-assignment.png)

1. **[ロール]**  ドロップダウン リストで、 **[所有者]**  ロールを選択します。

1. **[選択]** 一覧で、ユーザーを選択します。 一覧にユーザーが表示されない場合には、 **[選択]**  ボックスに表示名とメール アドレスを入力して、ディレクトリを検索します。

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、サブスクリプション スコープで、ユーザーに所有者のロールが割り当てられます。

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>マネージド ID のロールの割り当てを追加する (プレビュー)

この記事で前に説明したように、 **[アクセス制御 (IAM)]** ページを使用して、マネージド ID のロールの割り当てを追加できます。 [アクセス制御 (IAM)] ページを使用するときには、スコープから開始し、次にマネージド ID とロールを選択します。 このセクションでは、マネージド ID のためにロールの割り当てを追加する別の方法について説明します。 これらの手順を使用して、マネージド ID から開始し、スコープとロールを選択します。

> [!IMPORTANT]
> 代わりになるこれらの手順を使用してマネージド ID のロールの割り当てを追加することは、現在プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

### <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

次の手順に従ってマネージド ID から開始することで、システム割り当てマネージド ID にロールを割り当てます。

1. Azure portal で、システム割り当てマネージド ID を開きます。

1. 左側のメニューで、 **[ID]** をクリックします。

    ![システム割り当てマネージド ID](./media/shared/identity-system-assigned.png)

1. **[アクセス許可]** で、 **[Azure でのロールの割り当て]** をクリックします。

    選択したシステム割り当てマネージド ID に、既にロールが割り当てられている場合は、ロールの割り当ての一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![システム割り当てマネージド ID のロールの割り当て](./media/shared/role-assignments-system-assigned.png)

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

1. **[ロールの割り当ての追加 (プレビュー)]** をクリックします。

1. ドロップダウン リストを使用して、**サブスクリプション**、**リソース グループ**、リソースなど、ロールの割り当てを適用するリソースのセットを選択します。

    選択したスコープに対するロールの割り当ての書き込みアクセス許可がない場合は、インライン メッセージが表示されます。 

1. **[ロール]**  ボックスの一覧で、 **[仮想マシン共同作成者]**  などのロールを選択します。

   ![[ロールの割り当ての追加] ウィンドウ](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、選択されたスコープで、マネージド ID にロールが割り当てられます。

### <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

次の手順に従ってマネージド ID から開始することで、ユーザー割り当てマネージド ID にロールを割り当てます。

1. Azure portal で、ユーザー割り当てマネージド ID を開きます。

1. 左側のメニューで、 **[Azure でのロールの割り当て]** をクリックします。

    選択したユーザー割り当てマネージド ID に、既にロールが割り当てられている場合は、ロールの割り当ての一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![システム割り当てマネージド ID のロールの割り当て](./media/shared/role-assignments-user-assigned.png)

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

1. **[ロールの割り当ての追加 (プレビュー)]** をクリックします。

1. ドロップダウン リストを使用して、**サブスクリプション**、**リソース グループ**、リソースなど、ロールの割り当てを適用するリソースのセットを選択します。

    選択したスコープに対するロールの割り当ての書き込みアクセス許可がない場合は、インライン メッセージが表示されます。 

1. **[ロール]**  ボックスの一覧で、 **[仮想マシン共同作成者]**  などのロールを選択します。

   ![[ロールの割り当ての追加] ウィンドウ](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、選択されたスコープで、マネージド ID にロールが割り当てられます。

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

Azure RBAC では、Azure リソースからアクセス権を削除するために、ロールの割り当てを削除します。 ロールの割り当てを削除するには、次の手順に従います。

1. アクセス権を削除する管理グループ、サブスクリプション、リソース グループ、リソースなどのスコープで **[アクセス制御 (IAM)]** を開きます。

1. **[ロールの割り当て]** タブをクリックして、このサブスクリプションのすべてのロールの割り当てを表示します。

1. ロールの割り当ての一覧で、ロールの割り当てを削除するセキュリティ プリンシパルの隣にチェックマークを追加します。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **[削除]** をクリックします。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-portal/remove-role-assignment.png)

1. ロールの割り当ての削除メッセージが表示されたら、 **[はい]** をクリックします。

    継承されたロールの割り当てを削除できないというメッセージが表示された場合は、子スコープでロールの割り当てを削除しようとしています。 ロールが割り当てられたスコープでアクセス制御 (IAM) を開き、再試行してください。 **[スコープ]** 列を確認し、 **[(継承済み)]** の横にあるリンクをクリックすると、正しいスコープでアクセス制御 (IAM) をすばやく開くことができます。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-portal.md)
- [チュートリアル:Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する](quickstart-assign-role-user-portal.md)
- [Azure RBAC のトラブルシューティング](troubleshooting.md)
- [Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md)
