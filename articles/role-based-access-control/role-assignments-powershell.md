---
title: RBAC と Azure PowerShell を使用して Azure リソースへのアクセスを管理する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) と Azure PowerShell を使用して、ユーザー、グループ、およびアプリケーションの Azure リソースへのアクセスを管理する方法について説明します。 具体的には、アクセス権の一覧表示、付与、削除などを取り上げます。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4eaf59200295a25498d3c8b84196e73a703b055d
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995248"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>RBAC と Azure PowerShell を使用して Azure リソースへのアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 この記事では、RBAC と Azure PowerShell を使用してユーザー、グループ、アプリケーションのアクセス権を管理する方法を説明します。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

アクセスを管理するには、次のいずれかが必要です。

* [Azure Cloud Shell の PowerShell](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>ロールの一覧表示

### <a name="list-all-available-roles"></a>使用可能なすべてのロールの表示

割り当てることができる RBAC のロールを一覧表示したり、アクセス権が付与されている操作を調べたりするには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) を使用します。

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>特定のロールを一覧表示する

特定のロールを一覧表示するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) を使用します。

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>ロール定義を一覧表示する

### <a name="list-a-role-definition-in-json-format"></a>JSON 形式でロール定義を一覧表示します

JSON 形式でロール定義を一覧表示するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) を使用します。

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>ロールのアクションの表示

特定のロールのアクションを一覧表示するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) を使用します。

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="list-access"></a>アクセス権の表示

RBAC でアクセス権を一覧表示するには、ロールの割り当てを一覧表示します。

### <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>リソース グループ をスコープとするロールの割り当てを一覧表示する

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

### <a name="list-role-assignments-at-a-subscription-scope"></a>サブスクリプションをスコープとするロールの割り当てを一覧表示する

サブスクリプション スコープですべてのロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで、または [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) を使用して、その ID を見つけることができます。

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>管理グループをスコープとするロールの割り当てを一覧表示する

管理グループのスコープですべてのロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup) を使用できます。

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>従来のサービス管理者と共同管理者のロールの割り当てを一覧表示する

従来のサブスクリプション管理者と共同管理者のロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>アクセス権の付与

RBAC でアクセス権を付与するには、ロールの割り当てを作成します。

### <a name="search-for-object-ids"></a>オブジェクト ID の検索

ロールを割り当てるには、オブジェクト (ユーザー、グループ、またはアプリケーション) とスコープの両方を特定する必要があります。

サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで、または [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) を使用して、その ID を見つけることができます。

Azure AD ユーザーのオブジェクト ID を取得するには、[Get-AzADUser](/powershell/module/az.resources/get-azaduser) を使用します。

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

Azure AD グループのオブジェクト ID を取得するには、[Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) を使用します。

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

Azure AD サービス プリンシパル、つまりアプリケーションのオブジェクト ID を取得するには、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) を使用します。

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>リソース グループをスコープとするユーザーのロールの割り当てを作成する

リソース グループのスコープでユーザーにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

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

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>一意のロール ID を使用してロールの割り当てを作成する

ロール名が変更されるときがあります。たとえば次のような場合です。

- 独自のカスタム ロールを使用していて、名前を変更することにした場合。
- 名前に **(プレビュー)** が含まれるプレビュー ロールを使用している場合。 ロールがリリースされると、ロールの名前が変更されます。

> [!IMPORTANT]
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ロールの名前が変更される場合でも、ロールの ID は変わりません。 スクリプトまたはオートメーションを使用してロールの割り当てを作成する場合は、ロール名ではなく一意のロール ID を使用するのがベスト プラクティスです。 そうすれば、ロールの名前が変更されても、スクリプトが動作する可能性が高くなります。

ロール名ではなく一意のロール ID を使用してロールの割り当てを作成するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

次の例では、*pharma-sales* リソース グループのスコープで、 *alain@example.com* ユーザーに "[仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)" ロールを割り当てます。 一意のロール ID を取得するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) を使用するか、「[Azure リソースの組み込みロール](built-in-roles.md)」を参照してください。

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

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>リソースをスコープとするグループのロールの割り当てを作成する

リソースのスコープでグループにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

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

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>サブスクリプションをスコープするアプリケーションのロールの割り当てを作成する

サブスクリプションのスコープでアプリケーションにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

```azurepowershell
New-AzRoleAssignment -ObjectId <application_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
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

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>管理グループをスコープとするユーザーのロールの割り当てを作成する

管理グループのスコープでユーザーにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup) を使用できます。

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

## <a name="remove-access"></a>アクセス権の削除

RBAC でアクセス権を削除するには、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) を使用してロールの割り当てを削除します。

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

"The provided information does not map to a role assignment" (指定された情報は、ロールの割り当てにマップされていません) というエラー メッセージが表示された場合、`-Scope` または `-ResourceGroupName` パラメーターも指定してください。 詳細については、「[Azure リソースの RBAC のトラブルシューティング](troubleshooting.md#role-assignments-without-a-security-principal)」を参照してください。

## <a name="next-steps"></a>次の手順

- [チュートリアル:RBAC と Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する](tutorial-role-assignments-group-powershell.md)
- [チュートリアル:Azure PowerShell を使用して Azure リソースのカスタム ロールを作成する](tutorial-custom-role-powershell.md)
- [Azure PowerShell でリソースを管理する](../azure-resource-manager/manage-resources-powershell.md)
