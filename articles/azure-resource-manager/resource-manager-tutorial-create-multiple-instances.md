---
title: Azure Resource Manager を使用した複数のリソース インスタンスの作成 |Microsoft Docs
description: Azure Resource Manager テンプレートを作成して、複数の Azure リソース インスタンスを作成する方法を説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ce882ecee25f9570082a978b72a76ca903bfc12f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59279383"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>チュートリアル:Resource Manager テンプレートを使用した複数のリソース インスタンスの作成

Azure Resource Manager テンプレートで反復処理して、Azure リソースの複数のインスタンスを作成する方法について説明します。 このチュートリアルでは、3 つのストレージ アカウント インスタンスが作成されるようテンプレートを変更します。

![Azure Resource Manager による複数のインスタンスの作成の図](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/) と [Resource Manager ツール拡張機能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)は、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) と呼ばれます。 テンプレートにより、Azure ストレージ アカウント リソースが定義されます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. テンプレートで "Microsoft.Storage/storageAccounts" リソースが定義されています。 テンプレートを[テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)と比較します。 カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
5. **[ファイル]**>**[名前を付けて保存]** を選択し、ファイルを **azuredeploy.json** としてご自身のローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

既存のテンプレートでは、1 つのストレージ アカウントが作成されます。 3 つのストレージ アカウントが作成されるよう、テンプレートをカスタマイズします。  

Visual Studio Code で、次の 4 つの変更を行います。

![Azure Resource Manager で複数のインスタンスを作成する](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. ストレージ アカウントのリソース定義に `copy` 要素を追加します。 copy 要素には、そのループの反復回数と変数を指定します。 数値は正の整数で、800 を超えることはできません。
2. `copyIndex()` 関数は、ループ内の現在の繰り返しを返します。 インデックスを名前のプレフィックスとして使用します。 `copyIndex()` は 0 から始まります。 インデックス値をオフセットするには、copyIndex() 関数に値を渡します。 たとえば、 *copyindex (1)* のように指定します。
3. **variables** 要素を削除します。これ以上使われないためです。
4. **outputs** 要素を削除します。 これは不要になりました。

完成したテンプレートは、次のようになります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

複数のインスタンスの作成の詳細については、「[Azure Resource Manager テンプレートでリソースまたはプロパティの複数のインスタンスをデプロイする](./resource-group-create-multiple.md)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、Visual Studio Code のクイック スタートの「[テンプレートのデプロイ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)」セクションを参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

3 つのストレージ アカウントをすべて一覧表示するには、--name パラメータを省略します。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

テンプレートの名前定義とストレージ アカウント名を比較します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、複数のストレージ アカウント インスタンスを作成する方法について説明しました。  次のチュートリアルでは、複数のリソースと複数のリソース タイプを含むテンプレートを作成します。 一部のリソースには依存リソースがあります。

> [!div class="nextstepaction"]
> [依存リソースの作成](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
