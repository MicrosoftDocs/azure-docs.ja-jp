---
title: PowerShell を使用した割り当ての管理方法
description: 公式の Azure Blueprints PowerShell モジュールである Az.Blueprint でブループリント割り当てを管理する方法について説明します。
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: d60fb887e07b4697b8e86a4e2fd74a735ac0bb58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919378"
---
# <a name="how-to-manage-assignments-with-powershell"></a>PowerShell を使用した割り当ての管理方法

ブループリント割り当ては、**Az.Blueprint** Azure PowerShell モジュールを使用して管理できます。 このモジュールは、割り当てのフェッチ、作成、更新、および削除をサポートしています。 モジュールはまた、既存のブループリント定義の詳細をフェッチすることもできます。 この記事では、モジュールをインストールして使用し始める方法について説明します。

## <a name="add-the-azblueprint-module"></a>Az.Blueprint モジュールを追加する

Azure PowerShell でブループリント割り当てを管理できるようにするには、モジュールを追加する必要があります。 このモジュールは、ローカルにインストールされた PowerShell、[Azure Cloud Shell](https://shell.azure.com)、または [Azure PowerShell Docker イメージ](https://hub.docker.com/r/azuresdk/azure-powershell/)で使用できます。

### <a name="base-requirements"></a>基本要件

Azure Blueprints モジュールには、次のソフトウェアが必要です。

- Azure PowerShell 1.5.0 以降。 インストールされていない場合は、こちらの[手順](/powershell/azure/install-az-ps)に従ってください。
- PowerShellGet 2.0.1 以上。 インストールされていない場合、または更新されていない場合は、こちらの[手順](/powershell/scripting/gallery/installing-psget)に従ってください。

### <a name="install-the-module"></a>モジュールのインストール

PowerShell の Azure Blueprints モジュールは **Az.Blueprint** です。

1. **管理** PowerShell プロンプトで次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > **Az.Accounts** が既にインストールされている場合は、`-AllowClobber` を使用して強制的にインストールすることが必要になる可能性があります。

1. モジュールがインポートされていて、適切なバージョン (0.2.6) であることを確認します。

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
  - 各リソース グループのアーティファクト プレースホルダーには、そのリソース グループ アーティファクトに **Name** および **Location** を動的に設定するための、キー/値のペアがあります
  - リソース グループ パラメーターが指定されておらず、**defaultValue** がない場合、リソース グループ パラメーターは省略できません
- **AssignmentFile** (省略可能)
  - ブループリント割り当ての JSON ファイル表現へのパス
  - このパラメーターは、**Name**、**Blueprint**、**SubscriptionId**、および共通のパラメーターのみを含む PowerShell パラメーター セットの一部です。

### <a name="example-1-provide-parameters"></a>例 1:パラメーターを指定する

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

ブループリント割り当ての作成の出力例は、次のようになります。

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>例 2:JSON 割り当て定義ファイルを使用する

以下の例では、[例 1](#example-1-provide-parameters) とほぼ同じ割り当てを作成します。 パラメーターをコマンドレットに渡す代わりに、この例では、JSON 割り当て定義ファイルと **AssignmentFile** パラメーターの使用について示します。 さらに、**excludedPrincipals** プロパティを **locks** の一部として構成します。 **excludedPrincipals** に対応する PowerShell パラメーターは存在せず、このプロパティは JSON 割り当て定義ファイルを通じて設定することによってのみ構成できます。

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

ユーザー割り当てマネージド ID の JSON 割り当て定義ファイルの例については、REST API の[ユーザー割り当てマネージド ID による割り当ての例](/rest/api/blueprints/assignments/createorupdate#examples)の要求本文を参照してください。

## <a name="update-blueprint-assignments"></a>ブループリント割り当てを更新する

既に作成されているブループリント割り当てを更新する必要が生じる場合があります。 `Set-AzBlueprintAssignment` コマンドレットがこのアクションを処理します。 このコマンドレットは、`New-AzBlueprintAssignment` コマンドレットと同じパラメーターの多くを使用し、割り当てで設定されたすべてのものを更新できるようにします。 例外は、_Name_、_Blueprint_、および _SubscriptionId_ です。 指定された値だけが更新されます。

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
  - 各リソース グループのアーティファクト プレースホルダーには、そのリソース グループ アーティファクトに **Name** および **Location** を動的に設定するための、キー/値のペアがあります
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

ブループリント割り当てを削除するときは、`Remove-AzBlueprintAssignment` コマンドレットがこのアクションを処理します。 このコマンドレットは、削除するブループリント割り当てを指定するために **Name** と **InputObject** のどちらかを取ります。 **SubscriptionId** は _必須_ であり、すべての場合で指定する必要があります。

次の例では、`Get-AzBlueprintAssignment` で既存のブループリント割り当てをフェッチした後、`{subId}` と表される特定のサブスクリプションからこれを削除します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>コードの例

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
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

## <a name="next-steps"></a>次のステップ

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。