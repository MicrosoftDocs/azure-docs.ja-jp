---
title: Azure サブスクリプションの管理者としてユーザーを割り当てる - Azure RBAC
description: Azure portal と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザーを Azure サブスクリプションの管理者にする方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556835"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Azure サブスクリプションの管理者としてユーザーを割り当てる

ユーザーを Azure サブスクリプションの管理者にするには、サブスクリプションのスコープで、そのユーザーに[所有者](built-in-roles.md#owner)ロールを割り当てます。 所有者ロールにより、他のユーザーにアクセス権を付与する権限を含め、サブスクリプションにあるすべてのリソースへのフル アクセス権がユーザーに付与されます。 次の手順は、他のロールの割り当てと同じです。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>手順 1: サブスクリプションを開く

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部の検索ボックスで、サブスクリプションを検索します。

    ![Azure portal でリソース グループを検索する](./media/shared/sub-portal-search.png)

1. 使用するサブスクリプションをクリックします。

    次にサブスクリプションの例を示します。

    ![リソース グループの概要](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>手順 2: [ロールの割り当ての追加] ウィンドウを開く

**アクセス制御 (IAM)** は、一般的には、ロールを割り当てて Azure リソースへのアクセスを付与するために使用するページです。 "ID とアクセス管理 (IAM)" とも呼ばれており、Azure portal のいくつかの場所に表示されます。

1. **[アクセス制御 (IAM)]** をクリックします。

    サブスクリプションの [アクセス制御 (IAM)] ページの例を次に示します。

    ![リソース グループの [アクセス制御 (IAM)] ページ](./media/shared/sub-access-control.png)

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。
   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[ロールの割り当ての追加] メニュー](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ウィンドウが開きます。

   ![[ロールの割り当ての追加] ウィンドウ](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>手順 3: [所有者] ロールを選択する

Azure RBAC でロールを割り当てる権限を含め、すべてのリソースを管理するためのフル アクセスは、[所有者](built-in-roles.md#owner)ロールによって付与されます。 侵害された所有者による侵害の可能性を減らすため、サブスクリプション所有者は最大 3 人までにします。

- **[ロール]** の一覧で、 **[所有者]** ロールを選択します。

   ![[ロールの割り当ての追加] ウィンドウで [所有者] ロールを選択する](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>手順 4: アクセスを必要とするユーザーを選択する

1. **[アクセスの割り当て先]** の一覧で、 **[ユーザー、グループ、またはサービス プリンシパル]** を選択します。

1. **[選択]** セクションで、文字列を入力するかリストをスクロールして、ユーザーを検索します。

   ![[ロールの割り当ての追加] でユーザーを選択する](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. ユーザーが見つかったら、クリックして選択します。

## <a name="step-5-assign-role"></a>手順 5:ロールを割り当てる

1. ロールを割り当てるには、 **[保存]** をクリックします。

   しばらくすると、選択されたスコープで、ユーザーにロールが割り当てられます。

1. **[ロールの割り当て]** タブで、リストにロールの割り当てが表示されていることを確認します。

    ![保存されたロールの割り当ての追加](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Azure ロールを割り当てる](role-assignments-portal.md)
- [Azure portal を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-portal.md)
- [Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md)
