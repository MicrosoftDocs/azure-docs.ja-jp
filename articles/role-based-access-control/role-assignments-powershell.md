---
title: Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する - Azure RBAC
description: Azure PowerShell と Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9b0df4337a5e5faff3427222fb66caf8e02184a3
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146661"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] この記事では、Azure PowerShell を使用してロールを割り当てる方法について説明します。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

ロールの割り当てを追加または削除するには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)
- [Azure Cloud Shell の PowerShell](/azure/cloud-shell/overview) または [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>オブジェクト ID を取得する

ロールの割り当てを追加または削除するには、オブジェクトの一意の ID の指定が必要になることがあります。 ID の形式は `11111111-1111-1111-1111-111111111111` です。 この ID は、Azure portal または Azure PowerShell を使用して取得できます。

### <a name="user"></a>User

Azure AD ユーザーのオブジェクト ID を取得するには、[Get-AzADUser](/powershell/module/az.resources/get-azaduser) を使用できます。

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>グループ

Azure AD グループのオブジェクト ID を取得するには、[Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) を使用できます。

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Application

Azure AD サービス プリンシパルのオブジェクト ID (アプリケーションによって使用される ID) を取得するには、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) を使用できます。 サービス プリンシパルの場合は、アプリケーション ID **ではなく**、オブジェクト ID を使用します。

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>ロールの割り当てを追加する

Azure RBAC でアクセス権を付与するには、ロールの割り当てを追加します。

### <a name="user-at-a-resource-group-scope"></a>リソース グループをスコープとするユーザー

リソース グループのスコープでユーザーにロールの割り当てを追加するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>一意のロール ID の使用

ロール名が変更されるときがあります。たとえば次のような場合です。

- 独自のカスタム ロールを使用していて、名前を変更することにした場合。
- 名前に **(プレビュー)** が含まれるプレビュー ロールを使用している場合。 ロールがリリースされると、ロールの名前が変更されます。

> [!IMPORTANT]
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ロールの名前が変更される場合でも、ロールの ID は変わりません。 スクリプトまたはオートメーションを使用してロールの割り当てを作成する場合は、ロール名ではなく一意のロール ID を使用するのがベスト プラクティスです。 そうすれば、ロールの名前が変更されても、スクリプトが動作する可能性が高くなります。

ロール名ではなく一意のロール ID を使用してロールの割り当てを追加するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -Scope <resource_group_name/resource/management groups>
```

次の例では、*pharma-sales* リソース グループで、*alain\@example.com* ユーザーに[仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを付与します。 一意のロール ID を取得するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) を使用するか、「[Azure 組み込みロール](built-in-roles.md)」を参照してください。

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>リソースをスコープとするグループ

リソースのスコープでグループにロールの割り当てを追加するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。 グループのオブジェクト ID を取得する方法については、「[オブジェクト ID を取得する](#get-object-ids)」を参照してください。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>サブスクリプションをスコープとするアプリケーション

サブスクリプションのスコープでアプリケーションにロールの割り当てを追加するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。 アプリケーションのオブジェクト ID を取得する方法については、「[オブジェクト ID を取得する](#get-object-ids)」を参照してください。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>管理グループをスコープとするユーザー

管理グループのスコープでユーザーにロールの割り当てを追加するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup) を使用できます。

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

Azure RBAC でアクセス権を削除するには、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) を使用してロールの割り当てを削除します。

次の例では、*pharma-sales* リソース グループの *alain\@example.com* ユーザーから、"*仮想マシンの共同作成者*" ロールの割り当てを削除します。

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

次の例では、サブスクリプション スコープで <object_id> から <role_name> ロールを削除します。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

次の例では、管理グループ スコープで <object_id> から <role_name> ロールを削除します。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

"The provided information does not map to a role assignment" (指定された情報は、ロールの割り当てにマップされていません) というエラー メッセージが表示された場合、`-Scope` または `-ResourceGroupName` パラメーターも指定してください。 詳細については、「[Azure RBAC のトラブルシューティング](troubleshooting.md#role-assignments-with-identity-not-found)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure PowerShell を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-powershell.md)
- [チュートリアル:Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する](tutorial-role-assignments-group-powershell.md)
- [チュートリアル:Azure PowerShell を使用して Azure カスタム ロールを作成する](tutorial-custom-role-powershell.md)
- [Azure PowerShell でリソースを管理する](../azure-resource-manager/management/manage-resources-powershell.md)
