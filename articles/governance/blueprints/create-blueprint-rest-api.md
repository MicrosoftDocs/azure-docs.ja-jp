---
title: クイック スタート:REST API を使用してブループリントを作成する
description: このクイックスタートでは、Azure Blueprints と REST API を使用して、成果物を作成、定義、デプロイします。
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: eaf6dbb2ff14106ba8d2798d86a8f093855de85e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915626"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>クイック スタート:REST API で Azure Blueprint を定義して割り当てる

ブループリントの作成方法と割り当て方法について学習すると、Azure Resource Manager テンプレート (ARM テンプレート)、ポリシー、セキュリティなどに基づいて、再利用可能かつ短時間でデプロイできる構成を開発するための共通パターンを定義することができます。 このチュートリアルでは、組織内のブループリントの作成、発行、および割り当てに関連する一般的ないくつかのタスクを実行するための、Azure Blueprint の使用方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。
- `Microsoft.Blueprint` リソースプロバイダーを登録します。 手順については、「[リソースプロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>REST API で作業を開始する

REST API に慣れていない場合は、最初に「[Azure REST API Reference](/rest/api/azure/)」(Azure REST API リファレンス) を読んで、REST API の基本を理解してください (具体的には要求 URI と要求の本文)。 この記事では、これらの概念を使用して Azure Blueprint を操作する方法を説明しており、それらの実践的な知識を前提としています。 [ARMClient](https://github.com/projectkudu/ARMClient) などのツールは認可を自動的に処理できるので、初めての方にお勧めします。

Azure Blueprints の仕様については、[Azure Blueprints REST API](/rest/api/blueprints/) に関するページを参照してください。

### <a name="rest-api-and-powershell"></a>REST API と PowerShell

REST API の呼び出しを行うためのツールがまだない場合は、PowerShell を使うことを検討してください。 Azure で認証を行うためのヘッダーのサンプルを次に示します。 認証ヘッダー (**ベアラー トークン** と呼ばれることもあります) を生成し、パラメーターまたは **要求本文** で接続する REST API の URI を提供します。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

上の **$restUri** 変数の `{subscriptionId}` を置き換えて、サブスクリプションに関する情報を取得します。 $response 変数には `Invoke-RestMethod` コマンドレットの結果が保持されており、[ConvertFrom-json](/powershell/module/microsoft.powershell.utility/convertfrom-json) などのコマンドレットで解析できます。 REST API サービス エンドポイントで **要求本文** が必要な場合は、JSON 形式の変数を `Invoke-RestMethod` の `-Body` パラメーターに提供します。

## <a name="create-a-blueprint"></a>ブループリントを作成する

コンプライアンスの標準的なパターンを定義する最初のステップは、使用可能なリソースからブループリントを作成することです。 この例では、"MyBlueprint" という名前のブループリントを作成して、サブスクリプションのロールとポリシーの割り当てを構成します。 その後、リソース グループ、ARM テンプレート、およびそのリソース グループに対するロールの割り当てを追加します。

> [!NOTE]
> REST API を使用すると、"_ブループリント_" オブジェクトが最初に作成されます。 追加する各 "_成果物_" でパラメーターを持つものについては、最初の "_ブループリント_" でパラメーターを事前に定義する必要があります。

各 REST API URI には、独自の値で置き換える必要のある変数があります。

- `{YourMG}` - 実際の管理グループの ID に置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます

> [!NOTE]
> ブループリントはサブスクリプション レベルで作成することもできます。 例については、[サブスクリプションでのブループリントの作成例](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint)を参照してください。

1. 初期 "_ブループリント_" オブジェクトを作成します。 **要求本文** には、ブループリントに関するプロパティ、作成するリソース グループ、ブループリント レベルのすべてのパラメーターが含まれています。 これらのパラメーターは、割り当ての間に設定されて、後のステップで追加される成果物によって使用されます。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - 要求本文

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
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

1. サブスクリプションでロールの割り当てを追加します。 **要求本文** では成果物の "_種類_" が定義されていて、プロパティはロール定義の識別子と整合しており、プリンシパルの ID は値の配列として渡されます。 次の例では、指定されたロールを付与されたプリンシパル ID が、ブループリントの割り当て中に設定されるパラメーターに対して構成されます。 この例では、GUID が `b24988ac-6180-42a0-ab88-20f7382dd24c` の "_共同作成者_" 組み込みロールを使用しています。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. サブスクリプションでポリシーの割り当てを追加します。 **要求本文** では成果物の "_種類_" が定義されていて、プロパティはポリシーまたはイニシアティブの定義と整合しており、ブループリントの割り当て中に構成される定義済みのブループリント パラメーターを使用するようにポリシー割り当てを構成します。 この例では、GUID が `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` の "_タグとその既定値のリソース グループへの適用_" 組み込みポリシーを使用しています。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. サブスクリプションでストレージ タグ (_storageAccountType_ パラメーターを再利用) に対してもう 1 つのポリシー割り当てを追加します。 この追加のポリシー割り当て成果物は、ブループリントで定義されたパラメーターを複数の成果物で使用できることを示します。 この例の **storageAccountType** は、リソース グループにタグを設定する目的で使用されます。 この値は、次のステップで作成するストレージ アカウントに関する情報を提供します。 この例では、GUID が `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` の "_タグとその既定値のリソース グループへの適用_" 組み込みポリシーを使用しています。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. リソース グループにテンプレートを追加します。 ARM テンプレートの **要求本文** では、テンプレートの通常の JSON コンポーネントが含まれ、**properties.resourceGroup** でターゲット リソース グループが定義されます。 また、このテンプレートでは、ブループリントの **storageAccountType**、**tagName**、**tagValue** の各パラメーターをテンプレートに渡すことによって再利用しています。 ブループリント パラメーターは **properties.parameters** を定義するとテンプレートから利用することができ、テンプレート JSON 内ではそのキーと値のペアを使用して値を挿入します。 ブループリントとテンプレート パラメーターの名前は同じでもかまいませんが、それぞれがブループリントからテンプレート成果物に渡す方法を示すために別のものにしてあります。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
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
                     "location": "[resourceGroups('storageRG').location]",
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
             },
             "resourceGroup": "storageRG",
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
     }
     ```

1. リソース グループにロールの割り当てを追加します。 前のロール割り当てエントリと同様に、次の例では、**所有者** ロールに対する定義識別子を使用し、ブループリントとは異なるパラメーターを提供します。 この例では、GUID が `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` の "_所有者_" 組み込みロールを使用しています。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - 要求本文

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

## <a name="publish-a-blueprint"></a>ブループリントを発行する

これで成果物がブループリントに追加されたので、発行することができます。 発行することで、サブスクリプションへの割り当てが可能となります。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

`{BlueprintVersion}` の値は、文字、数字、ハイフンの (スペースまたは他の特殊文字を含まない) 文字列であり、最大長は 20 文字です。 一意で内容がわかるものを使用します (例: **v20180622-135541**)。

## <a name="assign-a-blueprint"></a>ブループリントを割り当てる

REST API を使用してブループリントを発行した後は、それをサブスクリプションに割り当てることができます。 作成したブループリントを、管理グループ階層下のいずれかのサブスクリプションに割り当てます。 ブループリントは、サブスクリプションに保存された場合、そのサブスクリプションに対してのみ割り当てることができます。 **要求本文** では、割り当てるブループリントを指定し、ブループリント定義内のリソース グループに対する名前と場所を提供し、ブループリントで定義されていて 1 つ以上の接続された成果物で使用されるすべてのパラメーターを提供します。

各 REST API URI には、独自の値で置き換える必要のある変数があります。

- `{tenantId}` - 実際のテナント ID に置き換えます
- `{YourMG}` - 実際の管理グループの ID に置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます

1. Azure Blueprint のサービス プリンシパルに、ターゲット サブスクリプションでの **所有者** ロールを提供します。 AppId は静的 (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`) ですが、サービス プリンシパル ID はテナントによって異なります。 次の REST API を使用して、テナントに詳細を要求できます。 [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) が使用されますが、承認は異なります。

   - REST API URI

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. サブスクリプションに割り当てることにより、ブループリントのデプロイを実行します。 **contributors** および **owners** パラメーターにはロールの割り当てを付与するプリンシパルの objectId の配列が必要なので、[Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) を使用して、独自のユーザー、グループ、またはサービス プリンシパルに対する **要求本文** で使用するための objectId を収集します。

   - REST API URI

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - 要求本文

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

   - ユーザー割り当てマネージド ID

     ブループリントの割り当てでは、[ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用することもできます。
     この場合、要求本文の **identity** 部分が次のように変わります。 `{yourRG}` と `{userIdentity}` は、それぞれ実際のリソース グループの名前とユーザー割り当てマネージド ID の名前に置き換えてください。

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

ブループリントは、サブスクリプションから削除することができます。 多くの場合、アーティファクトのリソースが不要になったときは削除するようにします。 ブループリントを削除しても、そのブループリントの一部として割り当てられている成果物は後に残されます。 ブループリントの割り当てを削除するには、次の REST API 操作を使用します。

- REST API URI

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>ブループリントを削除する

ブループリント自体を削除するには、次の REST API 操作を使用します。

- REST API URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、REST API を使用して、ブループリントを作成、割り当て、削除しました。 Azure Blueprints の詳細については、ブループリントのライフサイクルに関する記事に進んでください。

> [!div class="nextstepaction"]
> [ブループリントのライフサイクルについて学習する](./concepts/lifecycle.md)