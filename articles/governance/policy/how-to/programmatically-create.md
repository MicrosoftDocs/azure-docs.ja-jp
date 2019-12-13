---
title: プログラムによるポリシーの作成
description: この記事では、Azure CLI、Azure PowerShell、および REST API を使用して、Azure Policy のポリシーをプログラムで作成して管理する方法について説明します。
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: e81f0ca43788d8f36dde0a58d2ecd4b1604fd77e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873065"
---
# <a name="programmatically-create-policies"></a>プログラムによるポリシーの作成

この記事では、ポリシーをプログラムで作成して管理する方法について説明します。 Azure Policy の定義によって、さまざまなルールや効果がリソースに適用されます。 適用することで、リソースは会社の標準やサービス レベル アグリーメントに準拠した状態で維持されます。

コンプライアンスについては、「[コンプライアンス データの取得](get-compliance-data.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の前提条件が満たされていることを確認します。

1. [ARMClient](https://github.com/projectkudu/ARMClient) をまだインストールしていない場合はインストールします。 Azure Resource Manager ベースの API に HTTP 要求を送信するツールです。

1. Azure PowerShell モジュールを最新バージョンに更新します。 詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 最新バージョンについて詳しくは、[Azure PowerShell](https://github.com/Azure/azure-powershell/releases) をご覧ください。

1. Azure PowerShell を使用して Azure Policy Insights リソース プロバイダーを登録し、サブスクリプションがリソース プロバイダーで確実に動作することを検証します。 リソース プロバイダーを登録するには、リソース プロバイダーのアクションの登録操作を実行するためのアクセス許可が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。 リソース プロバイダーを登録する以下のコマンドを実行します。

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   リソース プロバイダーの登録と表示の詳細については、「[リソース プロバイダーと種類](../../../azure-resource-manager/resource-manager-supported-services.md)」を参照してください。

1. Azure CLI をまだインストールしていない場合はインストールします。 最新バージョンは、「[Windows での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)」で入手できます。

## <a name="create-and-assign-a-policy-definition"></a>ポリシー定義を作成して割り当てる

リソースの可視性を向上させるには、まず、ポリシーを作成してリソースに割り当てます。 次の手順では、ポリシーをプログラムで作成して割り当てる方法について説明します。 このサンプル ポリシーは、すべてのパブリック ネットワークに開かれているストレージ アカウントを、PowerShell、Azure CLI、および HTTP 要求を使用して監査します。

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>PowerShell でポリシー定義を作成して割り当てる

1. 次の JSON スニペットを使用して、AuditStorageAccounts.jsonという名前の JSON ファイルを作成します。

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   ポリシー定義の作成方法の詳細については、「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を参照してください。

1. AuditStorageAccounts.json ファイルを使用し、次のコマンドを実行してポリシー定義を作成します。

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   このコマンドは、_Audit Storage Accounts Open to Public Networks_ という名前のポリシー定義を作成します。
   使用できるその他のパラメーターの詳細については、「[New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)」を参照してください。

   場所のパラメーターを指定せずに呼び出す場合、`New-AzPolicyDefinition` は、既定により、セッション コンテキストの選択されたサブスクリプションにポリシー定義を保存することになります。 定義を別の場所に保存するには、次のパラメーターを使用します。

   - **SubscriptionId** -別のサブスクリプションに保存します。 _GUID_ 値が必要です。
   - **ManagementGroupName** -管理グループに保存します。 _string_ 値が必要です。

1. ポリシー定義を作成したら、次のコマンドを実行してポリシー割り当てを作成できます。

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   _ContosoRG_ を対象とするリソース グループの名前に置き換えます。

   `New-AzPolicyAssignment` の **Scope** パラメーターは、管理グループ、サブスクリプション、リソース グループ、または単一のリソースに使用できます。 このパラメーターは、`Get-AzResourceGroup` の **ResourceId** プロパティが返す完全なリソース パスを使用します。 各コンテナーの **Scope** のパターンは、次のとおりです。 `{rName}`、`{rgName}`、`{subId}`、および `{mgName}` を、それぞれリソース名、リソース グループ名、サブスクリプション ID、および管理グループ名と置き換えます。
   `{rType}` は、そのリソースの**リソースの種類** (VM の場合は `Microsoft.Compute/virtualMachines` など) に置き換えられます。

   - リソース - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - リソース グループ - `/subscriptions/{subId}/resourceGroups/{rgName}`
   - サブスクリプション - `/subscriptions/{subId}/`
   - 管理グループ - `/providers/Microsoft.Management/managementGroups/{mgName}`

Azure Resource Manager PowerShell モジュールを使用したリソース ポリシーの管理の詳細については、「[Az.Resources](/powershell/module/az.resources/#policies)」をご覧ください。

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>ARMClient を使用してポリシー定義を作成して割り当てる

次の手順に従って、ポリシー定義を作成します。

1. 次の JSON スニペットをコピーして JSON ファイルを作成します。 このファイルは次の手順で呼び出します。

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. 次の呼び出しを使用して、ポリシー定義を作成します。

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   前の {subscriptionId} をサブスクリプションの ID と置き換えるか、{managementGroupId} を[管理部グループ](../../management-groups/overview.md)の ID と置き換えます。

   クエリの構造の詳細については、「[Azure Policy Definitions – Create or Update (Azure Policy の定義 - 作成または更新)](/rest/api/resources/policydefinitions/createorupdate)」および「[Policy Definitions – Create or Update At Management Group (ポリシー定義 - 管理グループでの作成または更新)](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)」をご覧ください。

次の手順を使用してポリシー割り当てを作成し、ポリシー定義をリソース グループ レベルに割り当てます。

1. 次の JSON スニペットをコピーして、ポリシー割り当ての JSON ファイルを作成します。 例の &lt;&gt; 記号内の情報を独自の値に置き換えます。

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. 次の呼び出しを使用して、ポリシー割り当てを作成します。

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   例の &lt;&gt; 記号内の情報を独自の値に置き換えます。

   REST API への HTTP 呼び出しを作成する方法については、[Azure REST API のリソース](/rest/api/resources/)に関するページを参照してください。

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Azure CLI でポリシー定義を作成して割り当てる

次の手順に従って、ポリシー定義を作成します。

1. 次の JSON スニペットをコピーして、ポリシー割り当ての JSON ファイルを作成します。

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   ポリシー定義の作成方法の詳細については、「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を参照してください。

1. 次のコマンドを実行してポリシー定義を作成します。

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   このコマンドは、_Audit Storage Accounts Open to Public Networks_ という名前のポリシー定義を作成します。
   使用できるその他のパラメーターの詳細については、「[az policy definition create](/cli/azure/policy/definition#az-policy-definition-create)」を参照してください。

   場所のパラメーターを指定せずに呼び出す場合、`az policy definition creation` は、既定により、セッション コンテキストの選択されたサブスクリプションにポリシー定義を保存することになります。 定義を別の場所に保存するには、次のパラメーターを使用します。

   - **--subscription** - 別のサブスクリプションに保存します。 サブスクリプション ID の _GUID_ 値、またはサブスクリプション名の _string_ 値を指定する必要があります。
   - **--management-group** - 管理グループに保存します。 _string_ 値が必要です。

1. 次のコマンドを使用して、ポリシー割り当てを作成します。 例の &lt;&gt; 記号内の情報を独自の値に置き換えます。

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   `az policy assignment create` の **- scope** パラメーターは、管理グループ、サブスクリプション、リソース グループ、または単一のリソースに使用できます。 このパラメーターは完全なリソース パスを使用します。 各コンテナーの **--scope** のパターンは、次のとおりです。 `{rName}`、`{rgName}`、`{subId}`、および `{mgName}` を、それぞれリソース名、リソース グループ名、サブスクリプション ID、および管理グループ名と置き換えます。 `{rType}` は、そのリソースの**リソースの種類** (VM の場合は `Microsoft.Compute/virtualMachines` など) に置き換えられます。

   - リソース - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - リソース グループ - `/subscriptions/{subID}/resourceGroups/{rgName}`
   - サブスクリプション - `/subscriptions/{subID}`
   - 管理グループ - `/providers/Microsoft.Management/managementGroups/{mgName}`

Azure Policy の定義 ID は、PowerShell で次のコマンドを実行して取得できます。

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

作成したポリシー定義のポリシー定義 ID は、次の例のようになります。

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Azure CLI を使用してリソース ポリシーを管理する方法の詳細については、[Azure CLI リソース ポリシー](/cli/azure/policy?view=azure-cli-latest)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

この記事のコマンドとクエリの詳細については、次の記事をご覧ください。

- [Azure REST API リソース](/rest/api/resources/)
- [Azure PowerShell モジュール](/powershell/module/az.resources/#policies)
- [Azure CLI Policy コマンド](/cli/azure/policy?view=azure-cli-latest)
- [Azure Policy Insights Resource Provider REST API リファレンス](/rest/api/policy-insights)
- [Azure 管理グループでリソースを整理する](../../management-groups/overview.md)