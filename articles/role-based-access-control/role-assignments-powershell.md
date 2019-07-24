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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ace70dea5277b4e9d8c8b0e6e0cdc49da0b674e0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432228"
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
Get-AzRoleDefinition <role name>
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
Get-AzRoleDefinition <role name> | ConvertTo-Json
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
Get-AzRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role name>).Actions
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

### <a name="list-role-assignments-at-a-specific-scope"></a>特定のスコープでのロールの割り当ての表示

指定したサブスクリプション、リソース グループ、またはリソースに対するすべてのロールの割り当てを表示できます。 たとえば、リソース グループのすべてのアクティブな割り当てを表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

指定したユーザーに割り当てられているすべてのロールを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -SignInName <email, userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

特定のユーザーに割り当てられたすべてのロールと、そのユーザーが所属するグループに割り当てられたロールを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -SignInName <email, userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
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

サブスクリプション ID がわからない場合は、Azure Portal の **[サブスクリプション]** ブレードで、または [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) を使用して、その ID を見つけることができます。

Azure AD グループのオブジェクト ID を取得するには、[Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) を使用します。

```azurepowershell
Get-AzADGroup -SearchString <group name in quotes>
```

Azure AD サービス プリンシパル、つまりアプリケーションのオブジェクト ID を取得するには、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) を使用します。

```azurepowershell
Get-AzADServicePrincipal -SearchString <service name in quotes>
```

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>サブスクリプションをスコープするアプリケーションのロールの割り当てを作成する

サブスクリプションのスコープでアプリケーションにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

```azurepowershell
New-AzRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope /subscriptions/<subscription id>
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

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>リソース グループをスコープとするユーザーのロールの割り当てを作成する

リソース グループのスコープでユーザーにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

```azurepowershell
New-AzRoleAssignment -SignInName <email, userprincipalname> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>リソースをスコープとするグループのロールの割り当てを作成する

リソース グループのスコープでユーザーにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。

```azurepowershell
New-AzRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
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

## <a name="remove-access"></a>アクセス権の削除

RBAC でアクセス権を削除するには、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) を使用してロールの割り当てを削除します。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="next-steps"></a>次の手順

- [チュートリアル:RBAC と Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する](tutorial-role-assignments-group-powershell.md)
- [チュートリアル:Azure PowerShell を使用して Azure リソースのカスタム ロールを作成する](tutorial-custom-role-powershell.md)
- [Azure PowerShell でリソースを管理する](../azure-resource-manager/manage-resources-powershell.md)
