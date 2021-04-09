---
title: Azure PowerShell を使用して Azure 拒否割り当てを一覧表示する - Azure RBAC
description: Azure PowerShell と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、特定のスコープの特定の Azure リソース アクションへのアクセスが拒否されているユーザー、グループ、サービス プリンシパル、およびマネージド ID を一覧表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6cf379b051ba42be2d7df7d288f07cdc2a0002a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94657637"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>Azure PowerShell を使用して Azure 拒否割り当てを一覧表示する

[Azure 拒否割り当て](deny-assignments.md)を使用すると、ロールの割り当てでアクセスを許可されている場合であっても、特定の Azure リソース アクションをユーザーが実行できなくなります。 この記事では、Azure PowerShell を使用して拒否割り当てを一覧表示する方法を説明します。

> [!NOTE]
> 独自の拒否割り当てを直接作成することはできません。 拒否割り当てが作成されるしくみの詳細については、[Azure 拒否割り当て](deny-assignments.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

拒否割り当てに関する情報を取得するのに必要なものは次のとおりです:

- ほとんどの [Azure 組み込みロール](built-in-roles.md)に含まれている、`Microsoft.Authorization/denyAssignments/read` アクセス許可
- [Azure Cloud Shell の PowerShell](../cloud-shell/overview.md) または [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>拒否割り当てを一覧表示する

### <a name="list-all-deny-assignments"></a>すべての拒否割り当てを一覧表示する

現在のサブスクリプションに対するすべての拒否割り当てを一覧表示するには、[Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment) を使用します。

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>リソース グループ スコープの拒否割り当てを一覧表示する

リソース グループ スコープのすべての拒否割り当てを一覧表示するには、[Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment) を使用します。

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>サブスクリプション スコープの拒否割り当てを一覧表示する

サブスクリプション スコープのすべての拒否割り当てを一覧表示するには、[Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで、または [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) を使用して、その ID を見つけることができます。

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>次のステップ

- [Azure 拒否割り当てについて](deny-assignments.md)
- [Azure portal を使用して Azure 拒否割り当てを一覧表示する](deny-assignments-portal.md)
- [REST API を使用して Azure 拒否割り当てを一覧表示する](deny-assignments-rest.md)