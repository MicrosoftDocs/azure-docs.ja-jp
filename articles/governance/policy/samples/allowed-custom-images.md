---
title: サンプル - 承認された VM イメージ
description: このサンプル ポリシー定義は、承認されたカスタム イメージのみが環境にデプロイされることを要求します。
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 8def11c2d92af618054d0353fa2687d2e88e1134
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266717"
---
# <a name="sample---approved-virtual-machine-images"></a>サンプル - 承認された仮想マシン イメージ

このポリシーは、承認されたカスタム イメージのみを環境にデプロイすることが要件です。 承認されたイメージ ID の配列を指定します。

このサンプル ポリシーは、次の方法でデプロイすることができます。

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>サンプル ポリシー

### <a name="policy-definition"></a>ポリシー定義

次に示したのは、作成済みの完全な JSON ポリシー定義です。REST API や [Azure へのデプロイ] ボタンのほか、ポータルから手動で使用します。

[!code-json[full](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> ポリシーをポータルから手動で作成する場合は、上記の **properties.parameters** 部分と **properties.policyRule** 部分を使用してください。 有効な JSON とするために、中かっこ `{}` で 2 つのセクションをラップしてください。

### <a name="policy-rules"></a>ポリシー ルール

ポリシーのルールを定義する JSON は次のとおりです。Azure CLI と Azure PowerShell から使用します。

[!code-json[rule](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>ポリシー パラメーター

ポリシーのパラメーターを定義する JSON は次のとおりです。Azure CLI と Azure PowerShell から使用します。

[!code-json[parameters](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>parameters

|Name |Type |フィールド |説明 |
|---|---|---|---|
|imageIds |Array |Microsoft.Compute/imageIds |承認された VM イメージの一覧|

PowerShell または Azure CLI を使って割り当てを作成するときは、パラメーターの値を JSON として渡すことができます。JSON として渡すパラメーターは、文字列で渡すか、または `-PolicyParameter` (PowerShell) あるいは `--params` (Azure CLI) を使用してファイルで渡します。
PowerShell では、`-PolicyParameterObject` もサポートされます。この場合は、コマンドレットに Name/Value ハッシュ テーブルを渡す必要があります。**Name** にはパラメーターの名前を、**Value** には、割り当て時に渡す値の配列または単一値を指定します。

この例のパラメーターで使用できるのは、リソース グループ _YourResourceGroup_ 内の _ContosoStdImage_、または米国中部にある Windows Server 2016 Datacenter の May 2018 イメージ バージョンのみです。

```json
{
    "imageIds": {
        "value": [
            "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
            "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure ポータル

[![ポリシーのサンプルを Azure にデプロイする](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)
[![ポリシーのサンプルを Azure Gov にデプロイする](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Azure PowerShell でのデプロイ

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-custom-images-assignment' -DisplayName 'Approved VM images Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Azure PowerShell での削除

以前の割り当てと定義を削除するには、次のコマンドを実行します。

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell の説明

デプロイおよび削除のスクリプトには、次のコマンドが使用されています。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | 新しい Azure Policy の定義を作成します。 |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | 単一のリソース グループを取得します。 |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | 新しい Azure Policy の割り当てを作成します。 この例では定義を渡していますが、イニシアチブを渡すこともできます。 |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | 既存の Azure Policy の割り当てを削除します。 |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | 既存の Azure Policy の定義を削除します。 |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Azure CLI での削除

以前の割り当てと定義を削除するには、次のコマンドを実行します。

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI の説明

| command | メモ |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | 新しい Azure Policy の定義を作成します。 |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | 単一のリソース グループを取得します。 |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | 新しい Azure Policy の割り当てを作成します。 この例では定義を渡していますが、イニシアチブを渡すこともできます。 |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | 既存の Azure Policy の割り当てを削除します。 |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | 既存の Azure Policy の定義を削除します。 |

## <a name="rest-api"></a>REST API

Resource Manager REST API の対話操作には、[ARMClient](https://github.com/projectkudu/ARMClient) や PowerShell などいくつかのツールを使用できます。 PowerShell から REST API を呼び出す例は、[ポリシーの定義の構造](../concepts/definition-structure.md#aliases)に関するページの「**エイリアス**」セクションでご覧いただけます。

### <a name="deploy-with-rest-api"></a>REST API でのデプロイ

- ポリシーの定義を作成します (サブスクリプション スコープ)。 要求本文には、[ポリシー定義](#policy-definition)の JSON を使用します。

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- ポリシーの割り当てを作成します (リソース グループ スコープ)。

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  要求本文には、次の JSON の例を使用してください。

  ```json
  {
      "properties": {
          "displayName": "Approved VM images Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images",
          "parameters": {
              "imageIds": {
                  "value": [
                      "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
                      "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>REST API での削除

- ポリシーの割り当てを削除する

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- ポリシーの定義を削除する

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>REST API の説明

| Service | Group | Operation | メモ |
|---|---|---|---|
| リソース管理 | ポリシーの定義 | [作成](/rest/api/resources/policydefinitions/createorupdate) | 新しい Azure Policy 定義をサブスクリプションで作成します。 代替手段:[管理グループで作成する](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| リソース管理 | ポリシーの割り当て | [作成](/rest/api/resources/policyassignments/create) | 新しい Azure Policy の割り当てを作成します。 この例では定義を渡していますが、イニシアチブを渡すこともできます。 |
| リソース管理 | ポリシーの割り当て | [削除](/rest/api/resources/policyassignments/delete) | 既存の Azure Policy の割り当てを削除します。 |
| リソース管理 | ポリシーの定義 | [削除](/rest/api/resources/policydefinitions/delete) | 既存の Azure Policy の定義を削除します。 代替手段:[管理グループで削除する](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>次の手順

- その他の [Azure Policy サンプル](index.md)を確認する
- [Azure Policy の定義の構造](../concepts/definition-structure.md)を確認する
- [Windows VM にポリシーを適用する](../../../virtual-machines/windows/policy.md)方法に関するページで Virtual Machines 用の Azure Policy の例を参照する