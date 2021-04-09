---
title: Cloud Shell を使用したテンプレートのデプロイ
description: Azure Resource Manager と Azure Cloud Shell を使用してリソースを Azure にデプロイします。 リソースは Azure Resource Manager テンプレート (ARM テンプレート) で定義されます。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028609"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Azure Cloud Shell から ARM テンプレートをデプロイする

[Azure Cloud Shell](../../cloud-shell/overview.md) を使用して、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイできます。 リモートに格納されている ARM テンプレートをデプロイするか、Cloud Shell のローカル ストレージ アカウントに格納されている ARM テンプレートをデプロイできます。

任意のスコープにデプロイできます。 この記事では、リソース グループへのデプロイについて説明します。

## <a name="deploy-remote-template"></a>リモート テンプレートのデプロイ

外部テンプレートをデプロイするには、外部デプロイの場合とまったく同じように、テンプレートの URI を指定します。 外部テンプレートは、GitHub リポジトリまたは外部ストレージ アカウントに存在する可能性があります。

1. Cloud Shell のプロンプトを開きます。

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell を開く":::

1. テンプレートをデプロイするには、次のコマンドを使用します。

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

ローカル テンプレートをデプロイするには、最初に Cloud Shell セッションに接続されているストレージ アカウントにテンプレートをアップロードする必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Cloud Shell リソース グループを選択します。 名前のパターンは `cloud-shell-storage-<region>` です。

   ![リソース グループの選択](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Cloud Shell のストレージ アカウントを選択します。

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="ストレージ アカウントを選択する":::

1. **[ファイル共有]** を選択します。

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="[ファイル共有] を選択する":::

1. Cloud Shell の既定のファイル共有を選択します。 ファイル共有の名前の形式は `cs-<user>-<domain>-com-<uniqueGuid>` です。

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="既定のファイル共有":::

1. テンプレートを保持する新しいディレクトリを追加します。 このディレクトリを選択します。

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="ディレクトリを追加する":::

1. **[アップロード]** を選択します。

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="テンプレートをアップロードする":::

1. テンプレートを見つけてアップロードします。

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="テンプレートを選択する":::

1. Cloud Shell のプロンプトを開きます。

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell を開く":::

1. **clouddrive** ディレクトリに移動します。 テンプレートを保持するために追加したディレクトリに移動します。

1. テンプレートをデプロイするには、次のコマンドを使用します。

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>次のステップ

- デプロイ コマンドの詳細については、「[ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)」および「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
- テンプレートをデプロイする前に変更をプレビューするには、「[ARM テンプレートのデプロイの what-if 操作](template-deploy-what-if.md)」を参照してください。
