---
title: Azure PowerShell を使用して Azure カスタム ロールを作成または更新する - Azure RBAC
description: Azure PowerShell と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、カスタム ロールを一覧表示、作成、更新、または削除する方法について説明します。
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 799475db567c88f067192d027589e9185ee1782b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369176"
---
# <a name="create-or-update-azure-custom-roles-using-azure-powershell"></a>Azure PowerShell を使用して Azure カスタム ロールを作成または更新する

> [!IMPORTANT]
> `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure の組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 この記事では、Azure PowerShell を使用して、カスタム ロールを一覧表示、作成、更新、または削除する方法について説明します。

カスタム ロールの作成方法に関するステップバイステップのチュートリアルが必要な場合は、「[チュートリアル:Azure PowerShell を使用して Azure カスタム ロールを作成する](tutorial-custom-role-powershell.md)」をご覧ください。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

カスタム ロールを作成するには、次のものが必要です。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可
- [Azure Cloud Shell](../cloud-shell/overview.md) または [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

特定のスコープで割り当て可能なロールを一覧表示するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドを使用します。 次の例では、選択したサブスクリプションで割り当て可能なすべてのロールが一覧表示されます。

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

次の例では、選択したサブスクリプションで割り当て可能なカスタム ロールだけが一覧表示されます。

```azurepowershell
Get-AzRoleDefinition -Custom | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

選択したサブスクリプションがロールの `AssignableScopes` にない場合、カスタム ロールは一覧表示されません。

## <a name="list-a-custom-role-definition"></a>カスタム ロールの定義の一覧表示

カスタム ロールの定義を一覧表示するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) を使用します。 これは、組み込みロールに使用するコマンドと同じです。

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

次の例では、ロールのアクションのみを一覧表示します。

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、[New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) コマンドを使用します。 ロールを構成する方法は 2 つあります。`PSRoleDefinition` オブジェクトを使用するか JSON テンプレートを使用します。 

### <a name="get-operations-for-a-resource-provider"></a>リソース プロバイダー操作を取得する

カスタム ロールを作成するときは、リソース プロバイダーから可能なすべての操作を理解しておくことが重要です。
この情報を取得するには、[リソース プロバイダー操作](resource-provider-operations.md)のリストを表示するか、[Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) コマンドを使用します。
たとえば、仮想マシンで使用可能なすべての操作を確認する場合は、次のコマンドを使います。

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>PSRoleDefinition オブジェクトを使用したカスタム ロールの作成

PowerShell を使ってカスタム ロールを作成する場合は、[組み込みのロール](built-in-roles.md)を出発点として使うことも、ゼロから始めることもできます。 このセクションの最初の例では、組み込みのロールから始めて、さらに多くのアクセス許可を持つようにカスタマイズします。 その属性を編集し、必要に応じて `Actions`、`NotActions`、または `AssignableScopes`を追加して、変更内容を新しいロールとして保存します。

以下の例では、[仮想マシンの共同作業者](built-in-roles.md#virtual-machine-contributor)組み込みロールを土台として、*仮想マシン オペレーター* というカスタム ロールを作成しています。 この新しいロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作を許可し、仮想マシンの起動、再起動、監視を許可します。 カスタム ロールは 2 つのサブスクリプションで使用できます。

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

次の例では、*仮想マシン オペレーター* カスタム ロールを作成する別の方法を示しています。 まず、新しい `PSRoleDefinition` オブジェクトを作成します。 このアクション操作は、`perms` 変数で指定され、`Actions` プロパティに設定されます。 `NotActions` プロパティは、[仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)組み込みロールから `NotActions` を読み取ることで設定されます。 [仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)には `NotActions` がないため、この行は必要ありませんが、別のロールから情報を取得する方法を示しています。

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>JSON テンプレートを使用したカスタム ロールの作成

カスタム ロールのソース定義として JSON テンプレートを使用できます。 次の例では、ストレージと計算リソースへの読み取りアクセス、サポートへのアクセスを許可するカスタム ロールを作成し、そのロールを 2 つのサブスクリプションに追加します。 以下のコード例を含んだ新しいファイル `C:\CustomRoles\customrole1.json` を作成します。 新しい ID が自動的に生成されるため、最初のロール作成では ID を `null` に設定する必要があります。 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

ロールをサブスクリプションに追加するには、次の PowerShell コマンドを実行します。

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールの作成と同様に、`PSRoleDefinition` オブジェクトまたは JSON テンプレートを使用して既存のカスタム ロールを変更できます。

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>PSRoleDefinition オブジェクトを使用したカスタム ロールの更新

カスタム ロールを修正するには、まず [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドを使用してロール定義を取得します。 次に、必要に応じてロール定義を変更します。 最後に、[Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) コマンドを使用して変更したロール定義を保存します。

次の例では、 `Microsoft.Insights/diagnosticSettings/*` 操作が *仮想マシン オペレーター* カスタム ロールに追加されます。

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

次の例では、Azure サブスクリプションが *仮想マシン オペレーター* カスタム ロールの割り当て可能なスコープに追加されます。

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

次の例では、*仮想マシン オペレーター* カスタム ロールの `AssignableScopes` に管理グループが追加されます。 `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>JSON テンプレートを使用したカスタム ロールの更新

前の JSON テンプレートを使用して、既存のカスタム ロールを簡単に変更して、操作を追加または削除できます。 次の例のように、JSON テンプレートを更新し、ネットワークの読み取り操作を追加します。 テンプレートに示されている定義は、既存の定義に累積的には適用されません。つまり、ロールは、テンプレートに指定したとおりに表れます。 また、Id フィールドをロールの ID で更新する必要もあります。 この値が不明な場合は、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドレットを使用してこの情報を取得できます。

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

既存のロールを更新するには、次の PowerShell コマンドを実行します。

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>カスタム ロールの削除

カスタム ロールを削除するには、[Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) コマンドを使用します。

次の例では、 *仮想マシン オペレーター* カスタム ロールが削除されます。

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure PowerShell を使用して Azure カスタム ロールを作成する](tutorial-custom-role-powershell.md)
- [Azure カスタム ロール](custom-roles.md)
- [Azure リソース プロバイダーの操作](resource-provider-operations.md)
