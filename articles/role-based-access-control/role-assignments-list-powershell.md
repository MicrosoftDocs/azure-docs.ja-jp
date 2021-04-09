---
title: Azure PowerShell を使用して Azure でのロールの割り当てを一覧表示する - Azure RBAC
description: Azure PowerShell と Azure ロールベースのアクセス制御 (RBAC) を使用して、リソース ユーザー、グループ、サービス プリンシパル、およびマネージド ID のアクセスを決定する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bf5445f6ca04e56aab466e97967a58c3e4b735a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556944"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>Azure PowerShell を使用して Azure でのロールの割り当てを一覧表示する

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] この記事では、Azure PowerShell を使用してロールの割り当てを一覧表示する方法を説明します。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> 組織で、[Azure の委任されたリソース管理](../lighthouse/concepts/azure-delegated-resource-management.md)を使用するサービス プロバイダーに管理機能を外部委託している場合、そのサービス プロバイダーによって承認されているロールの割り当てはここに表示されません。

## <a name="prerequisites"></a>前提条件

- [Azure Cloud Shell の PowerShell](../cloud-shell/overview.md) または [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>現在のサブスクリプションにおけるロールの割り当ての一覧表示

現在のサブスクリプション内の (ルートと管理グループから継承されたロールの割り当てを含む) すべてのロールの割り当ての一覧を取得するもっと簡単な方法は、パラメーターの指定なしで [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用することです。

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>サブスクリプションのロールの割り当ての一覧表示

サブスクリプション スコープですべてのロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで、または [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) を使用して、その ID を見つけることができます。

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

指定したユーザーに割り当てられているすべてのロールを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

特定のユーザーに割り当てられたすべてのロールと、そのユーザーが所属するグループに割り当てられたロールを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>リソース グループに対するロールの割り当ての一覧表示

リソース グループのスコープですべてのロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>管理グループに対するロールの割り当ての一覧表示

管理グループのスコープですべてのロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup) を使用できます。

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>リソースに対するロールの割り当ての一覧表示

特定のリソースに対するロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) と `-Scope` パラメーターを使用します。 スコープは、リソースによって異なります。 スコープを取得するには、パラメーターを指定しないで `Get-AzRoleAssignment` を実行して、すべてのロールの割り当てを表示し、一覧表示するスコープを見つけます。

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

次の例は、ストレージ アカウントのロールの割り当てを一覧表示する方法を示しています。 このコマンドを実行すると、このストレージ アカウントに適用される、リソース グループやサブスクリプションといった上位のスコープでのロールの割り当ても一覧表示されることに注意してください。

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

リソースに直接割り当てられているロールの割り当てのみを一覧表示する場合は、[Where-object](/powershell/module/microsoft.powershell.core/where-object) コマンドを使用して一覧をフィルター処理できます。

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>従来のサービス管理者と共同管理者のロールの割り当てを一覧表示する

従来のサブスクリプション管理者と共同管理者のロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>マネージド ID のロールの割り当ての一覧表示

1. システム割り当てまたはユーザー割り当てのマネージド ID のオブジェクト ID を取得します。 

    ユーザー割り当てのマネージド ID のオブジェクト ID を取得するには、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) を使用します。

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. ロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>次のステップ

- [Azure PowerShell を使用して Azure ロールを割り当てる](role-assignments-powershell.md)