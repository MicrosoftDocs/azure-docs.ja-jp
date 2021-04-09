---
title: Azure ロールの割り当ての削除 - Azure RBAC
description: Azure portal、Azure PowerShell、Azure CLI、または REST API を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を削除する方法について説明します。
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561182"
---
# <a name="remove-azure-role-assignments"></a>Azure ロールの割り当ての削除

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../articles/role-based-access-control/overview.md) は、Azure のリソースに対するアクセスを管理するために使用する認可システムです。 Azure リソースからアクセス権を削除するために、ロールの割り当てを削除します。 この記事では、Azure portal、Azure PowerShell、Azure CLI、および REST API を使用して、ロールの割り当てを削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

ロールの割り当てを削除するには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/delete` アクセス許可 ([ユーザー アクセス管理者](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator)や[所有者](../../articles/role-based-access-control/built-in-roles.md#owner)など)

## <a name="azure-portal"></a>Azure portal

1. アクセス権を削除する管理グループ、サブスクリプション、リソース グループ、リソースなどのスコープで **[アクセス制御 (IAM)]** を開きます。

1. **[ロールの割り当て]** タブをクリックして、このスコープのすべてのロールの割り当てを表示します。

1. ロールの割り当ての一覧で、ロールの割り当てを削除するセキュリティ プリンシパルの隣にチェックマークを追加します。

   ![削除対象として選択されたロールの割り当て](./media/role-assignments-remove/rg-role-assignments-select.png)

1. **[削除]** をクリックします。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-remove/remove-role-assignment.png)

1. ロールの割り当ての削除メッセージが表示されたら、 **[はい]** をクリックします。

    継承されたロールの割り当てを削除できないというメッセージが表示された場合は、子スコープでロールの割り当てを削除しようとしています。 ロールが割り当てられたスコープでアクセス制御 (IAM) を開き、再試行してください。 **[スコープ]** 列を確認し、 **[(継承済み)]** の横にあるリンクをクリックすると、正しいスコープでアクセス制御 (IAM) をすばやく開くことができます。

   ![継承されたロールの割り当てに対するロールの割り当ての削除メッセージ](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell では、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) を使用してロールの割り当てを削除します。

次の例では、*pharma-sales* リソース グループの *patlong\@contoso.com* ユーザーから、"[仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)" ロールの割り当てを削除します。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

[閲覧者](built-in-roles.md#reader)ロールを、サブスクリプション スコープで、ID が 22222222-2222-2222-2222-222222222222 の *Ann Mack Team* グループから削除します。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

[課金データ閲覧者](built-in-roles.md#billing-reader)ロールを、管理グループ スコープで、*alain\@example.com* ユーザーから削除します。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

"The provided information does not map to a role assignment" (指定された情報は、ロールの割り当てにマップされていません) というエラー メッセージが表示された場合、`-Scope` または `-ResourceGroupName` パラメーターも指定してください。 詳細については、「[Azure RBAC のトラブルシューティング](troubleshooting.md#role-assignments-with-identity-not-found)」を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI では、[az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) を使用してロールの割り当てを削除します。

次の例では、*pharma-sales* リソース グループの *patlong\@contoso.com* ユーザーから、"[仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)" ロールの割り当てを削除します。

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

[閲覧者](built-in-roles.md#reader)ロールを、サブスクリプション スコープで、ID が 22222222-2222-2222-2222-222222222222 の *Ann Mack Team* グループから削除します。

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

[課金データ閲覧者](built-in-roles.md#billing-reader)ロールを、管理グループ スコープで、*alain\@example.com* ユーザーから削除します。

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>REST API

REST API では、[ロールの割り当て - 削除](/rest/api/authorization/roleassignments/delete)を使用してロールの割り当てを削除します。

1. ロールの割り当ての識別子 (GUID) を取得します。 この識別子は、ロールの割り当てを最初に作成するときに返されます。あるいは、ロールの割り当てを一覧表示することで取得できます。

1. 次の要求から開始します。

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. URI の *{scope}* を、ロールの割り当てを削除するためのスコープに変更します。

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    > | `subscriptions/{subscriptionId1}` | サブスクリプション |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | リソース |

1. *{roleAssignmentId}* を、ロールの割り当ての GUID 識別子に置き換えます。

次の要求は、サブスクリプション スコープで指定されたロールの割り当てを削除します。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

出力例を次に示します。

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>ARM テンプレート

Azure Resource Manager テンプレート (ARM テンプレート) を使用してロールの割り当てを削除する方法はありません。 ロールの割り当てを削除するには、Azure portal、Azure PowerShell、Azure CLI、REST API などの他のツールを使用する必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-portal.md)
- [Azure PowerShell を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-powershell.md)
- [Azure RBAC のトラブルシューティング](troubleshooting.md)
