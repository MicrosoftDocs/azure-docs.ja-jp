---
title: クイック スタート:Azure CLI を使用してブループリントを作成する
description: このクイックスタートでは、Azure Blueprints と Azure CLI を使用して、成果物を作成、定義、デプロイします。
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 6ce3031c93f973c2efb251fad371a6f3750ae0fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920242"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>クイック スタート:Azure CLI で Azure Blueprint を定義して割り当てる

ブループリントの作成方法と割り当て方法について学習すると、Azure Resource Manager テンプレート (ARM テンプレート)、ポリシー、セキュリティなどに基づいて、再利用可能かつ短時間でデプロイできる構成を開発するための共通パターンを定義することができます。 このチュートリアルでは、組織内のブループリントの作成、発行、および割り当てに関連する一般的ないくつかのタスクを実行するための、Azure Blueprint の使用方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。
- 過去に Azure Blueprints を使用したことがない場合は、Azure CLI から `az provider register --namespace Microsoft.Blueprint` を使用してリソース プロバイダーを登録してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>ブループリント拡張機能を追加する

Azure CLI でブループリントの定義と割り当てを管理できるようにするには、拡張機能を追加する必要があります。
[Windows 10上の bash](/windows/wsl/install-win10)、 [Cloud Shell](https://shell.azure.com) (スタンドアロンと内部の両方のポータル)、 [Azure CLI Docker 画像](https://hub.docker.com/_/microsoft-azure-cli)、または、ローカルにインストールされた場合を含め、この拡張機能はAzure CLI を使用できる場合はいつでも動作します。

1. 最新の Azure CLI がインストールされていることを確認します (**2.0.76** 以降)。 インストールされていない場合は、こちらの[手順](/cli/azure/install-azure-cli-windows)に従ってください。

1. 選択された Azure CLI 環境では、次のコマンドでインポートします。

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. 拡張機能がインストールされていて、必要なバージョン (**0.1.0** 以降) であることを検証します。

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>ブループリントを作成する

コンプライアンスの標準的なパターンを定義する最初のステップは、使用可能なリソースからブループリントを作成することです。 この例では、"MyBlueprint" という名前のブループリントを作成して、サブスクリプションのロールとポリシーの割り当てを構成します。 その後、リソース グループ、ARM テンプレート、およびそのリソース グループに対するロールの割り当てを追加します。

> [!NOTE]
> Azure CLI を使用するときは、最初に "_ブループリント_" オブジェクトを作成します。 追加する各 "_成果物_" でパラメーターを持つものについては、最初の "_ブループリント_" でパラメーターを事前に定義する必要があります。

1. 初期 "_ブループリント_" オブジェクトを作成します。 **parameters** パラメーターは、すべてのブループリント レベルのパラメーターを含む JSON ファイルを受け取ります。 これらのパラメーターは、割り当ての間に設定されて、後のステップで追加される成果物によって使用されます。

   - JSON ファイル - blueprintparms.json

     ```json
     {
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
     }
     ```

   - Azure CLI コマンド

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > ブループリントの定義をインポートするときは、_blueprint.json_ というファイル名を使用します。
     > このファイル名は [az blueprint import](/cli/azure/ext/blueprint/blueprint#ext_blueprint_az_blueprint_import) を呼び出すときに使用されます。

     既定では、ブループリント オブジェクトが既定のサブスクリプションに作成されます。 管理グループを指定するには、**managementgroup** パラメーターを使用します。 サブスクリプションを指定するには、**subscription** パラメーターを使用します。

1. ストレージの成果物のリソース グループを定義に追加します。

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. サブスクリプションでロールの割り当てを追加します。 次の例では、指定されたロールを付与されたプリンシパル ID が、ブループリントの割り当て中に設定されるパラメーターに対して構成されます。 この例では、GUID が `b24988ac-6180-42a0-ab88-20f7382dd24c` の "_共同作成者_" 組み込みロールを使用しています。

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. サブスクリプションでポリシーの割り当てを追加します。 この例では、GUID が `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` の "_タグとその既定値のリソース グループへの適用_" 組み込みポリシーを使用しています。

   - JSON ファイル - artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI コマンド

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

     > [!NOTE]
     > `az blueprint` を Mac で使用する場合は、パスを含んだパラメーター値の `\` を `/` に置き換えてください。 このケースでは、**parameters** の値が `artifacts/policyTags.json` になります。

1. サブスクリプションでストレージ タグ (_storageAccountType_ パラメーターを再利用) に対してもう 1 つのポリシー割り当てを追加します。 この追加のポリシー割り当て成果物は、ブループリントで定義されたパラメーターを複数の成果物で使用できることを示します。 この例の **storageAccountType** は、リソース グループにタグを設定する目的で使用されます。 この値は、次のステップで作成するストレージ アカウントに関する情報を提供します。 この例では、GUID が `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` の "_タグとその既定値のリソース グループへの適用_" 組み込みポリシーを使用しています。

   - JSON ファイル - artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLI コマンド

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

     > [!NOTE]
     > `az blueprint` を Mac で使用する場合は、パスを含んだパラメーター値の `\` を `/` に置き換えてください。 このケースでは、**parameters** の値が `artifacts/policyStorageTags.json` になります。

1. リソース グループにテンプレートを追加します。 ARM テンプレートの **template** パラメーターには、テンプレートの標準的な JSON コンポーネントが含まれています。 また、このテンプレートでは、ブループリントの **storageAccountType**、**tagName**、**tagValue** の各パラメーターをテンプレートに渡すことによって再利用しています。 ブループリント パラメーターは、**parameters** パラメーターを使用してテンプレートで使用でき、テンプレート JSON 内ではそのキーと値のペアを使用して値を挿入します。 ブループリントとテンプレート パラメーターの名前は同じでもかまいません。

   - JSON ARM テンプレート ファイル - artifacts\templateStorage.json

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

   - JSON ARM テンプレート パラメーター ファイル - artifacts\templateStorageParams.json

     ```json
     {
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
     ```

   - Azure CLI コマンド

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

     > [!NOTE]
     > `az blueprint` を Mac で使用する場合は、パスを含んだパラメーター値の `\` を `/` に置き換えてください。 このケースでは、**template** の値が `artifacts/templateStorage.json` に、**parameters** の値が `artifacts/templateStorageParams.json` になります。

1. リソース グループにロールの割り当てを追加します。 前のロール割り当てエントリと同様に、次の例では、**所有者** ロールに対する定義識別子を使用し、ブループリントとは異なるパラメーターを提供します。 この例では、GUID が `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` の "_所有者_" 組み込みロールを使用しています。

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>ブループリントを発行する

これで成果物がブループリントに追加されたので、発行することができます。 発行することで、サブスクリプションへの割り当てが可能となります。

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

`{BlueprintVersion}` の値は、文字、数字、ハイフンの (スペースまたは他の特殊文字を含まない) 文字列であり、最大長は 20 文字です。 一意で内容がわかるものを使用します (例: **v20200605-135541**)。

## <a name="assign-a-blueprint"></a>ブループリントを割り当てる

Azure CLI を使用してブループリントを発行した後は、それをサブスクリプションに割り当てることができます。 作成したブループリントを、管理グループ階層下のいずれかのサブスクリプションに割り当てます。 ブループリントは、サブスクリプションに保存された場合、そのサブスクリプションに対してのみ割り当てることができます。 割り当てるブループリントは、**blueprint-name** パラメーターで指定します。 名前、場所、ID、ロック、ブループリントのパラメーターを指定するには、対応する Azure CLI パラメーターを `az blueprint assignment create` コマンドで使用するか、**parameters** JSON ファイルでそれらを指定します。

1. サブスクリプションに割り当てることにより、ブループリントのデプロイを実行します。 **contributors** および **owners** パラメーターにはロールの割り当てを付与するプリンシパルの objectId の配列が必要なので、[Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) を使用して、独自のユーザー、グループ、またはサービス プリンシパルに対する **parameters** で使用するための objectId を収集します。

   - JSON ファイル - blueprintAssignment.json

     ```json
     {
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
     ```

   - Azure CLI コマンド

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - ユーザー割り当てマネージド ID

     ブループリントの割り当てでは、[ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用することもできます。
     この場合、**identity-type** パラメーターは _UserAssigned_ に設定され、**user-assigned-identities** パラメーターは ID を指定します。 `{userIdentity}` は、ご自分のユーザー割り当てのマネージド ID の名前に置き換えます。

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     **ユーザー割り当てマネージド ID** は、ブループリントを割り当てるユーザーにアクセス許可があれば、どのサブスクリプションおよびどのリソース グループに属していてもかまいません。

     > [!IMPORTANT]
     > Azure Blueprints は、ユーザー割り当てのマネージド ID を管理しません。 ユーザーが各自で十分なロールとアクセス許可を割り当てる必要があります。そうしないと、ブループリントの割り当てに失敗します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

### <a name="unassign-a-blueprint"></a>ブループリントを割り当て解除する

ブループリントは、サブスクリプションから削除することができます。 多くの場合、アーティファクトのリソースが不要になったときは削除するようにします。 ブループリントを削除しても、そのブループリントの一部として割り当てられている成果物は後に残されます。 ブループリントの割り当てを解除するには、`az blueprint assignment delete` コマンドを使用します。

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure CLI を使用して、ブループリントを作成、割り当て、削除しました。 Azure Blueprints の詳細については、ブループリントのライフサイクルに関する記事に進んでください。

> [!div class="nextstepaction"]
> [ブループリントのライフサイクルについて学習する](./concepts/lifecycle.md)