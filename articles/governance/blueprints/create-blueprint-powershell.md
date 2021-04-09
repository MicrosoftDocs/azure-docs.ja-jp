---
title: クイック スタート:PowerShell を使用してブループリントを作成する
description: このクイックスタートでは、Azure Blueprints と PowerShell を使用して、成果物を作成、定義、デプロイします。
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 65d573d0aec7d5f292bc985483e1f12c350ae03a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918281"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>クイック スタート:PowerShell で Azure Blueprint を定義して割り当てる

ブループリントの作成方法と割り当て方法について学習すると、Azure Resource Manager テンプレート (ARM テンプレート)、ポリシー、セキュリティなどに基づいて、再利用可能かつ短時間でデプロイできる構成を開発するための共通パターンを定義することができます。 このチュートリアルでは、組織内のブループリントの作成、発行、および割り当てに関連する一般的ないくつかのタスクを実行するための、Azure Blueprint の使用方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。
- まだインストールされていない場合は、「[Az.Blueprint モジュールを追加する](./how-to/manage-assignments-ps.md#add-the-azblueprint-module)」の手順に従って、PowerShell ギャラリーから **Az.Blueprint** モジュールをインストールして検証します。
- 過去に Azure Blueprints を使用したことがない場合は、Azure PowerShell から `Register-AzResourceProvider -ProviderNamespace Microsoft.Blueprint` を使用してリソース プロバイダーを登録してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>ブループリントを作成する

コンプライアンスの標準的なパターンを定義する最初のステップは、使用可能なリソースからブループリントを作成することです。 この例では、"MyBlueprint" という名前のブループリントを作成して、サブスクリプションのロールとポリシーの割り当てを構成します。 その後、リソース グループ、ARM テンプレート、およびそのリソース グループに対するロールの割り当てを追加します。

> [!NOTE]
> PowerShell を使用するときは、最初に "_ブループリント_" オブジェクトを作成します。 追加する各 "_成果物_" でパラメーターを持つものについては、最初の "_ブループリント_" でパラメーターを事前に定義する必要があります。

1. 初期 "_ブループリント_" オブジェクトを作成します。 **BlueprintFile** パラメーターは、ブループリントに関するプロパティ、作成するリソース グループ、ブループリント レベルのすべてのパラメーターが含まれている JSON ファイルを受け取ります。 これらのパラメーターは、割り当ての間に設定されて、後のステップで追加される成果物によって使用されます。

   - JSON ファイル - blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell コマンド

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > ブループリントの定義をプログラムから作成するときは、_blueprint.json_ というファイル名を使用します。
     > [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact) を呼び出すときに、このファイル名が使用されます。

     既定では、ブループリント オブジェクトが既定のサブスクリプションに作成されます。 管理グループを指定するには、**ManagementGroupId** パラメーターを使用します。 サブスクリプションを指定するには、**SubscriptionId** パラメーターを使用します。

1. サブスクリプションでロールの割り当てを追加します。 **ArtifactFile** では成果物の "_種類_" が定義されていて、プロパティはロール定義の識別子と整合しており、プリンシパルの ID は値の配列として渡されます。 次の例では、指定されたロールを付与されたプリンシパル ID が、ブループリントの割り当て中に設定されるパラメーターに対して構成されます。 この例では、GUID が `b24988ac-6180-42a0-ab88-20f7382dd24c` の "_共同作成者_" 組み込みロールを使用しています。

   - JSON ファイル - \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell コマンド

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. サブスクリプションでポリシーの割り当てを追加します。 **ArtifactFile** では成果物の "_種類_" が定義されていて、プロパティはポリシーまたはイニシアティブの定義と整合しており、ブループリントの割り当て中に構成される定義済みのブループリント パラメーターを使用するようにポリシー割り当てを構成します。 この例では、GUID が `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` の "_タグとその既定値のリソース グループへの適用_" 組み込みポリシーを使用しています。

   - JSON ファイル - \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell コマンド

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. サブスクリプションでストレージ タグ (_storageAccountType_ パラメーターを再利用) に対してもう 1 つのポリシー割り当てを追加します。 この追加のポリシー割り当て成果物は、ブループリントで定義されたパラメーターを複数の成果物で使用できることを示します。 この例の **storageAccountType** は、リソース グループにタグを設定する目的で使用されます。 この値は、次のステップで作成するストレージ アカウントに関する情報を提供します。 この例では、GUID が `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` の "_タグとその既定値のリソース グループへの適用_" 組み込みポリシーを使用しています。

   - JSON ファイル - \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell コマンド

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. リソース グループにテンプレートを追加します。 ARM テンプレートの **TemplateFile** には、テンプレートの標準的な JSON コンポーネントが含まれています。 また、このテンプレートでは、ブループリントの **storageAccountType**、**tagName**、**tagValue** の各パラメーターをテンプレートに渡すことによって再利用しています。 ブループリント パラメーターは、**TemplateParameterFile** パラメーターを使用してテンプレートに提供され、テンプレート JSON 内ではそのキーと値のペアを使用して値を挿入します。 ブループリントとテンプレート パラメーターの名前は同じでもかまいません。

   - JSON ARM テンプレート ファイル - \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON ARM テンプレート パラメーター ファイル - \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - PowerShell コマンド

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. リソース グループにロールの割り当てを追加します。 前のロール割り当てエントリと同様に、次の例では、**所有者** ロールに対する定義識別子を使用し、ブループリントとは異なるパラメーターを提供します。 この例では、GUID が `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` の "_所有者_" 組み込みロールを使用しています。

   - JSON ファイル - \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell コマンド

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>ブループリントを発行する

これで成果物がブループリントに追加されたので、発行することができます。 発行することで、サブスクリプションへの割り当てが可能となります。

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

`{BlueprintVersion}` の値は、文字、数字、ハイフンの (スペースまたは他の特殊文字を含まない) 文字列であり、最大長は 20 文字です。 一意で内容がわかるものを使用します (例: **v20180622-135541**)。

## <a name="assign-a-blueprint"></a>ブループリントを割り当てる

PowerShell を使用してブループリントを発行した後は、それをサブスクリプションに割り当てることができます。 作成したブループリントを、管理グループ階層下のいずれかのサブスクリプションに割り当てます。 ブループリントは、サブスクリプションに保存された場合、そのサブスクリプションに対してのみ割り当てることができます。 割り当てるブループリントは、**Blueprint** パラメーターで指定します。 名前、場所、ID、ロック、ブループリント パラメーターを指定するには、対応する PowerShell パラメーターを `New-AzBlueprintAssignment` コマンドレットで使用するか、**AssignmentFile** パラメーターの JSON ファイルでそれらを指定します。

1. サブスクリプションに割り当てることにより、ブループリントのデプロイを実行します。 **contributors** および **owners** パラメーターにはロールの割り当てを付与するプリンシパルの objectId の配列が必要なので、[Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) を使用して、独自のユーザー、グループ、またはサービス プリンシパルに対する **AssignmentFile** で使用するための objectId を収集します。

   - JSON ファイル - blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell コマンド

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - ユーザー割り当てマネージド ID

     ブループリントの割り当てでは、[ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用することもできます。
     この場合、JSON 割り当てファイルの **identity** 部分が次のように変わります。 `{tenantId}`、`{subscriptionId}`、`{yourRG}`、`{userIdentity}` は、それぞれ実際の tenantId、subscriptionId、リソース グループ名、ユーザー割り当てマネージド ID の名前に置き換えてください。

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     **ユーザー割り当てマネージド ID** は、ブループリントを割り当てるユーザーにアクセス許可があれば、どのサブスクリプションおよびどのリソース グループに属していてもかまいません。

     > [!IMPORTANT]
     > Azure Blueprints は、ユーザー割り当てのマネージド ID を管理しません。 ユーザーが各自で十分なロールとアクセス許可を割り当てる必要があります。そうしないと、ブループリントの割り当てに失敗します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

### <a name="unassign-a-blueprint"></a>ブループリントを割り当て解除する

ブループリントは、サブスクリプションから削除することができます。 多くの場合、アーティファクトのリソースが不要になったときは削除するようにします。 ブループリントを削除しても、そのブループリントの一部として割り当てられている成果物は後に残されます。 ブループリントの割り当てを解除するには、`Remove-AzBlueprintAssignment` コマンドレットを使用します。

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、PowerShell を使用して、ブループリントを作成、割り当て、削除しました。 Azure Blueprints の詳細については、ブループリントのライフサイクルに関する記事に進んでください。

> [!div class="nextstepaction"]
> [ブループリントのライフサイクルについて学習する](./concepts/lifecycle.md)