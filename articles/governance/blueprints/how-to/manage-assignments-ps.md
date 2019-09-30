---
title: PowerShell を使用した割り当ての管理方法
description: 公式の Azure Blueprints PowerShell モジュールである Az.Blueprint でブループリント割り当てを管理する方法について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: beaa3f4c5ab272592e7fae5a95b40a9b586aaf65
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232893"
---
# <a name="how-to-manage-assignments-with-powershell"></a>PowerShell を使用した割り当ての管理方法

ブループリント割り当ては、**Az.Blueprint** Azure PowerShell モジュールを使用して管理できます。 このモジュールは、割り当てのフェッチ、作成、更新、および削除をサポートしています。 モジュールはまた、既存のブループリント定義の詳細をフェッチすることもできます。 この記事では、モジュールをインストールして使用し始める方法について説明します。

## <a name="add-the-azblueprint-module"></a>Az.Blueprint モジュールを追加する

Azure PowerShell でブループリント割り当てを管理できるようにするには、モジュールを追加する必要があります。 このモジュールは、ローカルにインストールされた PowerShell、[Azure Cloud Shell](https://shell.azure.com)、または [Azure PowerShell Docker イメージ](https://hub.docker.com/r/azuresdk/azure-powershell/)で使用できます。

### <a name="base-requirements"></a>基本要件

Azure Blueprints モジュールには、次のソフトウェアが必要です。

- Azure PowerShell 1.5.0 以降。 インストールされていない場合は、こちらの[手順](/powershell/azure/install-az-ps)に従ってください。
- PowerShellGet 2.0.1 以上。 インストールされていない場合、または更新されていない場合は、こちらの[手順](/powershell/gallery/installing-psget)に従ってください。

### <a name="install-the-module"></a>モジュールのインストール

PowerShell の Blueprints モジュールは **Az.Blueprint** です。

1. **管理** PowerShell プロンプトで次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > **Az.Accounts** が既にインストールされている場合は、`-AllowClobber` を使用して強制的にインストールすることが必要になる可能性があります。

1. モジュールがインポートされており、適切なバージョン (0.1.0) であることを確認します。

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>ブループリントの定義を取得する

割り当てを扱うには、多くの場合、最初にブループリントの定義への参照を得る必要があります。
`Get-AzBlueprint` コマンドレットは、ブループリントの 1 つ以上の定義を取得します。 このコマンドレットは、`-ManagementGroupId {mgId}` を使用して管理グループから、または `-SubscriptionId {subId}` を使用してサブスクリプションからブループリントの定義を取得できます。 **Nave** パラメーターは、ブループリントの定義を取得しますが、**ManagementGroupId** または **SubscriptionId** と使用する必要があります。 返されるブループリントの定義をより明確にするために、**Version** を **Name** と一緒に使用できます。 **Version** の代わりにスイッチ `-LatestPublished` を使用すれば、最も新しく公開されたバージョンが取得されます。

次の例では、`Get-AzBlueprint` を使用して、`{subId}` と表される特定のサブスクリプションから、「101-blueprints-definition-subscription」というブループリントの定義のすべてのバージョンを取得します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

複数のバージョンがあるブループリントの定義の出力例は次のようになります。

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

ブループリントの定義の[ブループリントのパラメーター](../concepts/parameters.md#blueprint-parameters)を展開して詳細情報を表示できます。

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>ブループリント割り当てを取得する

ブループリント割り当てが既に存在する場合、その参照を `Get-AzBlueprintAssignment` コマンドレットで取得できます。 このコマンドレットは、省略可能なパラメーターとして **SubscriptionId** と **Name** を取ります。 **SubscriptionId** が指定されていない場合、現在のサブスクリプション コンテキストが使用されます。

次の例では `Get-AzBlueprintAssignment` を使用して、「Assignment-lock-resource-groups」という単一のブループリント割り当てを、`{subId}` として表される特定のサブスクリプションから取得します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

ブループリント割り当ての出力例は、次のようになります。

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>ブループリント割り当てを作成する

ブループリント割り当てがまだ存在しない場合は、`New-AzBlueprintAssignment` コマンドレットを使用して作成できます。 このコマンドレットは、次のパラメーターを使用します。

- **Name** [必須]
  - ブループリント割り当ての名前を指定します
  - 一意であり、**SubscriptionId** にまだ存在していないことが必要です
- **Blueprint** [必須]
  - 割り当てるブループリントの定義を指定します
  - `Get-AzBlueprint` を使用して参照オブジェクトを取得します
- **Location** [必須]
  - システム割り当てマネージド ID およびサブスクリプションのデプロイ オブジェクトを作成するリージョンを選択します
- **Subscription** (オプション)
  - 割り当てのデプロイ先のサブスクリプションを指定します
  - 指定されていない場合、既定で現在のサブスクリプション コンテキストに設定されます
- **Lock** (オプション)
  - デプロイされたリソースに使用する[ブループリントのリソース ロック](../concepts/resource-locking.md)を定義します
  - サポートされているオプション:_None_、_AllResourcesReadOnly_、_AllResourcesDoNotDelete_
  - 指定しない場合、既定で _None_ に設定されます
- **SystemAssignedIdentity** (オプション)
  - 割り当てのシステム割り当てマネージド ID を作成し、リソースをデプロイするときに選択します
  - 「identity」パラメーター セットの既定値
  - **UserAssignedIdentity** とは使用できません
- **UserAssignedIdentity** (オプション)
  - 割り当てに使用し、リソースをデプロイするユーザー割り当てマネージド ID を指定します
  - 「identity」パラメーター セットの一部
  - **SystemAssignedIdentity** とは使用できません
- **Parameter** (オプション)
  - ブループリント割り当てで[動的パラメーター](../concepts/parameters.md#dynamic-parameters)を設定するためのキー/値のペアの[ハッシュ テーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動的パラメーターの既定値は定義の **defaultValue** です
  - パラメーターが指定されておらず、**defaultValue** がない場合、パラメーターは省略できません

    > [!NOTE]
    > **Parameter** は secureStrings をサポートしていません。

- **ResourceGroupParameter** (オプション)
  - リソース グループのアーティファクトの[ハッシュ テーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 各リソース グループのアーティファクト プレースホルダーには、そのリソース グループ アーティファクト上で **Name** または **Location** あるいはその両方を動的に設定するために、キー/値のペアが指定されます
  - リソース グループ パラメーターが指定されておらず、**defaultValue** がない場合、リソース グループ パラメーターは省略できません

次の例では、`Get-AzBlueprint` でフェッチされたバージョン「1.1」の「my-blueprint」ブループリント定義の新しい割り当てを作成し、マネージド ID と割り当てオブジェクトの場所を「westus2」に設定し、_AllResourcesReadOnly_ でリソースをロックし、**Parameters** と **ResourceGroupParameter** の両方のハッシュ テーブルを、`{subId}` と表される特定のサブスクリプション上で設定します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

ブループリント割り当ての作成の出力例は、次のようになります。

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>ブループリント割り当てを更新する

既に作成されているブループリント割り当てを更新する必要が生じる場合があります。 `Set-AzBlueprintAssignment` コマンドレットがこのアクションを処理します。 このコマンドレットは、`New-AzBlueprintAssignment` コマンドレットと同じパラメーターの多くを使用し、割り当てで設定されたすべてのものを更新できるようにします。 この例外は、_Name_、_Blueprint_、および _SubscriptionId_ です。 指定された値だけが更新されます。

ブループリント割り当ての更新時に行われる処理を理解するには、「[割り当ての更新の規則](./update-existing-assignments.md#rules-for-updating-assignments)」を参照してください。

- **Name** [必須]
  - 更新するブループリント割り当ての名前を指定します
  - 割り当てを変更するのではなく、更新する割り当てを検索するために使用します
- **Blueprint** [必須]
  - ブループリント割り当てのブループリント定義を指定します
  - `Get-AzBlueprint` を使用して参照オブジェクトを取得します
  - 割り当てを変更するのではなく、更新する割り当てを検索するために使用します
- **Location** (オプション)
  - システム割り当てマネージド ID およびサブスクリプションのデプロイ オブジェクトを作成するリージョンを選択します
- **Subscription** (オプション)
  - 割り当てのデプロイ先のサブスクリプションを指定します
  - 指定されていない場合、既定で現在のサブスクリプション コンテキストに設定されます
  - 割り当てを変更するのではなく、更新する割り当てを検索するために使用します
- **Lock** (オプション)
  - デプロイされたリソースに使用する[ブループリントのリソース ロック](../concepts/resource-locking.md)を定義します
  - サポートされているオプション:_None_、_AllResourcesReadOnly_、_AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (オプション)
  - 割り当てのシステム割り当てマネージド ID を作成し、リソースをデプロイするときに選択します
  - 「identity」パラメーター セットの既定値
  - **UserAssignedIdentity** とは使用できません
- **UserAssignedIdentity** (オプション)
  - 割り当てに使用し、リソースをデプロイするユーザー割り当てマネージド ID を指定します
  - 「identity」パラメーター セットの一部
  - **SystemAssignedIdentity** とは使用できません
- **Parameter** (オプション)
  - ブループリント割り当てで[動的パラメーター](../concepts/parameters.md#dynamic-parameters)を設定するためのキー/値のペアの[ハッシュ テーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動的パラメーターの既定値は定義の **defaultValue** です
  - パラメーターが指定されておらず、**defaultValue** がない場合、パラメーターは省略できません

    > [!NOTE]
    > **Parameter** は secureStrings をサポートしていません。

- **ResourceGroupParameter** (オプション)
  - リソース グループのアーティファクトの[ハッシュ テーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 各リソース グループのアーティファクト プレースホルダーには、そのリソース グループ アーティファクト上で **Name** または **Location** あるいはその両方を動的に設定するために、キー/値のペアが指定されます
  - リソース グループ パラメーターが指定されておらず、**defaultValue** がない場合、リソース グループ パラメーターは省略できません

次の例では、ロック モードを変更することによって `Get-AzBlueprint` でフェッチされたバージョン「1.1」の「my-blueprint」ブループリント定義の割り当てを更新します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

ブループリント割り当ての作成の出力例は、次のようになります。

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>ブループリント割り当てを削除する

ブループリント割り当てを削除するときは、`Remove-AzBlueprintAssignment` コマンドレットがこのアクションを処理します。 このコマンドレットは、削除するブループリント割り当てを指定するために **Name** と **InputObject** のどちらかを取ります。 **SubscriptionId** は_必須_であり、すべての場合で指定する必要があります。

次の例では、`Get-AzBlueprintAssignment` で既存のブループリント割り当てをフェッチした後、`{subId}` と表される特定のサブスクリプションからこれを削除します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>エンド ツー エンドのコード例

次の例ではすべての手順をまとめ、ブループリント定義を取得し、`{subId}` と表される特定のサブスクリプションでブループリント割り当てを作成、更新、および削除します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>次の手順

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。