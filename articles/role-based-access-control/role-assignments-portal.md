---
title: Azure portal を使用して Azure ロールを割り当てる - Azure RBAC
description: Azure portal と Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: e25bbe4e1a96e4efaaa13732aea571d26d4b006e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555275"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てる

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] この記事では、Azure portal を使用してロールを割り当てる方法について説明します。

Azure Active Directory で管理者ロールを割り当てる必要がある場合は、「[Azure Active Directory で管理者ロールを表示して割り当てる](../active-directory/roles/manage-roles-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>手順 1:必要なスコープを特定する

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)]スコープの詳細については、[スコープについての理解](scope-overview.md)に関する記事を参照してください。

![Azure RBAC のスコープ レベル](../../includes/role-based-access-control/media/scope-levels.png)

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部にある検索ボックスで、アクセス権を付与するスコープを検索します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、または特定のリソースを検索します。

    ![Azure portal でのリソース グループの検索](./media/shared/rg-portal-search.png)

1. そのスコープの特定のリソースをクリックします。

    リソース グループの例を次に示します。

    ![リソース グループの概要](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>手順 2: [ロールの割り当ての追加] ウィンドウを開く

**アクセス制御 (IAM)** は、一般的には、ロールを割り当てて Azure リソースへのアクセスを付与するために使用するページです。 "ID とアクセス管理 (IAM)" とも呼ばれており、Azure portal のいくつかの場所に表示されます。

1. **[アクセス制御 (IAM)]** をクリックします。

    リソース グループの [アクセス制御 (IAM)] ページの例を次に示します。

    ![リソース グループの [アクセス制御 (IAM)] ページ](./media/shared/rg-access-control.png)

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。
   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[ロールの割り当ての追加] メニュー](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ウィンドウが開きます。

   ![[ロールの割り当ての追加] ウィンドウ](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-appropriate-role"></a>手順 3: 適切なロールを選択する

1. **[ロール]** リストで、割り当てるロールを検索またはスクロールします。

    適切なロールを判断するため、情報アイコンをポイントすると、ロールの説明が表示されます。 詳細については、「[Azure 組み込みロール](built-in-roles.md)」の記事をご覧ください。

   ![[ロールの割り当ての追加] でロールを選択する](./media/role-assignments-portal/add-role-assignment-role.png)

1. クリックしてロールを選択します。

## <a name="step-4-select-who-needs-access"></a>手順 4:アクセスを必要とするユーザーを選択する

1. **[アクセスの割り当て先]** リストで、アクセス権を割り当てるセキュリティ プリンシパルの種類を選択します。

    | Type | 説明 |
    | --- | --- |
    | **ユーザー、グループ、またはサービス プリンシパル** | ロールをユーザー、グループ、またはサービス プリンシパル (アプリケーション) に割り当てるには、この種類を選択します。 |
    | **ユーザー割り当てマネージド ID** | [ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) にロールを割り当てるには、この種類を選択します。 |
    | *システム割り当てマネージド ID* | [システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) にロールを割り当てるには、マネージド ID が配置されている Azure サービス インスタンスを選択します。 |

   ![[ロールの割り当ての追加] でセキュリティ プリンシパルの種類を選択する](./media/role-assignments-portal/add-role-assignment-type.png)

1. ユーザー割り当てマネージド ID またはシステム割り当てマネージド ID を選択した場合は、マネージド ID が配置されている **サブスクリプション** を選択します。

1. **[選択]** セクションで、文字列を入力するかリストをスクロールして、セキュリティ プリンシパルを検索します。

   ![[ロールの割り当ての追加] でユーザーを選択する](./media/role-assignments-portal/add-role-assignment-user.png)

1. セキュリティ プリンシパルが見つかったら、クリックして選択します。

## <a name="step-5-assign-role"></a>手順 5:ロールを割り当てる

1. ロールを割り当てるには、 **[保存]** をクリックします。

   しばらくすると、セキュリティ プリンシパルに選択されたスコープのロールが割り当てられます。

1. **[ロールの割り当て]** タブで、リストにロールの割り当てが表示されていることを確認します。

    ![保存されたロールの割り当ての追加](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>次のステップ

- [Azure サブスクリプションの管理者としてユーザーを割り当てる](role-assignments-portal-subscription-admin.md)
- [Azure ロールの割り当ての削除](role-assignments-remove.md)
- [Azure RBAC のトラブルシューティング](troubleshooting.md)
