---
title: 複数のリソース インスタンスの作成
description: Azure Resource Manager テンプレート (ARM テンプレート) を作成して、複数の Azure リソース インスタンスを作成する方法を説明します。
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: e669e27547633639a88674ffee499fb1d84facdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673955"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>チュートリアル:ARM テンプレートを使用した複数のリソース インスタンスの作成

Azure Resource Manager テンプレート (ARM テンプレート) で反復処理して、Azure リソースの複数のインスタンスを作成する方法について説明します。 このチュートリアルでは、3 つのストレージ アカウント インスタンスが作成されるようテンプレートを変更します。

![Azure Resource Manager による複数のインスタンスの作成の図](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

リソースのコピーについて取り上げた Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Visual Studio Code と Resource Manager ツール拡張機能。 「[クイック スタート:Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)は、ARM テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) と呼ばれます。 テンプレートにより、Azure ストレージ アカウント リソースが定義されます。

1. Visual Studio Code から、 **[ファイル]**  >  **[ファイルを開く]** を選択します。
1. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. **[開く]** を選択して、ファイルを開きます。
1. テンプレートに、`Microsoft.Storage/storageAccounts` リソースが定義されています。 テンプレートを[テンプレート リファレンス](/azure/templates/Microsoft.Storage/storageAccounts)と比較します。 カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
1. **[ファイル]**  >  **[名前を付けて保存]** を選択し、ファイルを _azuredeploy.json_ としてご自身のローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

既存のテンプレートでは、1 つのストレージ アカウントが作成されます。 3 つのストレージ アカウントが作成されるよう、テンプレートをカスタマイズします。

Visual Studio Code で、次の 4 つの変更を行います。

![Azure Resource Manager で複数のインスタンスを作成する](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. ストレージ アカウントのリソース定義に `copy` 要素を追加します。 `copy` 要素には、このループの反復回数と変数を指定します。 数値は正の整数で、800 を超えることはできません。
2. `copyIndex()` 関数は、ループ内の現在の繰り返しを返します。 インデックスを名前のプレフィックスとして使用します。 `copyIndex()` は 0 から始まります。 インデックス値をオフセットするには、`copyIndex()` 関数に値を渡します。 たとえば、「 `copyIndex(1)` 」のように入力します。
3. `variables` 要素を削除します。以降では使用しないためです。
4. `outputs` 要素を削除します。 これは不要になりました。

完成したテンプレートは、次のようになります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

複数のインスタンスの作成の詳細については、「[ARM テンプレートでのリソースの反復処理](./copy-resources.md)」を参照してください

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. [Azure Cloud Shell](https://shell.azure.com) にサインインします。

1. 左上の **[PowerShell]** または **[Bash]** (CLI の場合) を選択して、希望の環境を選択します。 切り替えた場合は、シェルを再起動する必要があります。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **[ファイルのアップロード/ダウンロード]** を選択し、 **[アップロード]** を選択します。 先のスクリーンショットをご覧ください。 前のセクションで保存したファイルを選択します。 ファイルをアップロードした後、`ls` コマンドと `cat` コマンドを使用して、ファイルが正常にアップロードされたことを確認できます。

1. Cloud Shell で次のコマンドを実行します。 PowerShell コードまたは CLI コードを表示するタブを選択します。

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

テンプレートのデプロイが正常に完了したら、指定したリソース グループに作成された 3 つのストレージ アカウントを表示できます。 テンプレートの名前定義とストレージ アカウント名を比較します。

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 3 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、複数のストレージ アカウント インスタンスを作成する方法について説明しました。 次のチュートリアルでは、複数のリソースと複数のリソース タイプを含むテンプレートを作成します。 一部のリソースには依存リソースがあります。

> [!div class="nextstepaction"]
> [依存リソースの作成](./template-tutorial-create-templates-with-dependent-resources.md)
