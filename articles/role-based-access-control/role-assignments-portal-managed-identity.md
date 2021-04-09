---
title: Azure ロールをマネージド ID に割り当てる (プレビュー) - Azure RBAC
description: まずマネージド ID から始めることで Azure ロールを割り当てて、次に Azure portal と Azure ロールベースのアクセス制御 (Azure RBAC) を使用してスコープとロールを選択する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 57c8c00a64996bc6223fbe7e514db9db38ccdcc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556856"
---
# <a name="assign-azure-roles-to-a-managed-identity-preview"></a>Azure ロールをマネージド ID に割り当てる (プレビュー)

[Azure portal を使用した Azure ロールの割り当て](role-assignments-portal.md)に関するページで説明したように、 **[アクセス制御 (IAM)]** ページを使用してマネージド ID にロールを割り当てることができます。 [アクセス制御 (IAM)] ページを使用するときには、スコープから開始し、次にマネージド ID とロールを選択します。 この記事では、マネージド ID のためにロールを割り当てる別の方法について説明します。 これらの手順を使用して、マネージド ID から開始し、スコープとロールを選択します。

> [!IMPORTANT]
> 代わりになるこれらの手順を使用してマネージド ID のロールを割り当てることは、現在プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

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

   ![システム割り当てマネージド ID の [ロールの割り当ての追加] ペイン](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、選択されたスコープで、マネージド ID にロールが割り当てられます。

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

次の手順に従ってマネージド ID から開始することで、ユーザー割り当てマネージド ID にロールを割り当てます。

1. Azure portal で、ユーザー割り当てマネージド ID を開きます。

1. 左側のメニューで、 **[Azure でのロールの割り当て]** をクリックします。

    選択したユーザー割り当てマネージド ID に、既にロールが割り当てられている場合は、ロールの割り当ての一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![ユーザー割り当てマネージド ID のロールの割り当て](./media/shared/role-assignments-user-assigned.png)

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

1. **[ロールの割り当ての追加 (プレビュー)]** をクリックします。

1. ドロップダウン リストを使用して、**サブスクリプション**、**リソース グループ**、リソースなど、ロールの割り当てを適用するリソースのセットを選択します。

    選択したスコープに対するロールの割り当ての書き込みアクセス許可がない場合は、インライン メッセージが表示されます。 

1. **[ロール]**  ボックスの一覧で、 **[仮想マシン共同作成者]**  などのロールを選択します。

   ![ユーザー割り当てマネージド ID の [ロールの割り当ての追加] ペイン](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、選択されたスコープで、マネージド ID にロールが割り当てられます。

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)
- [Azure portal を使用して Azure ロールを割り当てる](role-assignments-portal.md)
- [Azure portal を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-portal.md)
