---
title: Bicep ファイルを作成する - Visual Studio Code
description: Visual Studio Code と Bicep 拡張機能を使用して、Azure リソースをデプロイするための Bicepファイルを作成します
author: mumian
ms.date: 03/02/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: a5b5533cd87c417d03db960c477392c24fa59f8a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504152"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>クイックスタート: Visual Studio Code を使用して Bicep ファイルを作成する

Visual Studio Code の Bicep 拡張機能によって、言語サポートとリソース オートコンプリートが提供されます。 これらのツールは、[Bicep](./bicep-overview.md) ファイルの作成と検証を行うときに役立ちます。 このクイックスタートでは、拡張機能を使用して Bicep ファイルを一から作成します。 作業中に、この拡張機能の検証や入力候補などの機能を使用します。

このクイックスタートを完了するには、[Visual Studio Code](https://code.visualstudio.com/) と、[Bicep 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)のインストールが必要です。 さらに、最新の [Azure CLI](/cli/azure/) または最新の [Azure PowerShell モジュール](/powershell/azure/new-azureps-module-az)のどちらかがインストールされ、認証されている必要があります。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-a-bicep-file"></a>Bicep ファイルを作成する

Visual Studio Code で *azuredeploy.bicep* という名前の新しいファイルを作成して開きます。

## <a name="add-an-azure-resource"></a>Azure リソースを追加する

bicep ファイルに基本的なストレージ アカウント リソースを追加します。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

リソース宣言には、次の 4 つのコンポーネントがあります。

- **resource**: キーワード。
- **シンボリック名** (stg): シンボリック名は、bicep ファイルを通してリソースを参照するための識別子です。 これは、デプロイされるときに付けられるリソースの名前ではありません。 リソースの名前は、**name** プロパティによって定義されます。  この一覧の 4 番目のコンポーネントを参照してください。
- **リソースの種類** (Microsoft.Storage/storageAccounts@2019-06-01): リソース プロバイダー (Microsoft.Storage)、リソースの種類 (storageAccounts)、および apiVersion (2019-06-01) で構成されます。 リソース プロバイダーからは、それぞれ独自の API バージョンが公開されているため、これはその種類に固有の値となります。 [ARM テンプレート リファレンス](/azure/templates/)で、さまざまな Azure リソースの種類と apiVersion を 見つけることができます。
- **プロパティ** (= {...} 内のすべて): リソースの種類のプロパティを指定します。 すべてのリソースに、`name` プロパティがあります。 ほとんどのリソースには、リソースのデプロイ先リージョンを設定する `location` プロパティがあります。 その他のプロパティは、リソースの種類と API バージョンにより異なります。

## <a name="completion-and-validation"></a>入力候補と検証

拡張機能の最も強力な機能の 1 つが、Azure スキーマとの統合です。 Azure スキーマは、検証とリソース対応の入力候補機能を備えた拡張機能を提供します。 ストレージ アカウントを変更して、検証と入力候補が動作することを確認してみましょう。

最初に、ストレージ アカウントの種類を、`megaStorage` などの無効な値に更新します。 このアクションでは、`megaStorage` が有効な値ではないことを示す警告が生成されます。

![無効なストレージ構成を示す画像](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

入力候補機能を使用するには、`megaStorage` を削除し、一重引用符の内側にカーソルを置き、`ctrl` + `space` を押します。 このアクションでは、有効な値の入力候補一覧が表示されます。

![オートコンプリートの拡張機能を示す画像](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>パラメーターを追加する

ここでは、ストレージ アカウント名を指定するためのパラメーターを作成して使用します。

ファイルの先頭に次のコードを追加します。

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Azure ストレージ アカウント名の長さは 3 文字以上、24 文字以内です。 `minLength` と `maxLength` を使用して、適切な値を指定します。

次に、ストレージ リソースで、パラメーターを使用するように name プロパティを更新します。 これを行うには、現在のストレージ リソース名を単一引用符も含めて削除します。 `ctrl` + `space` を押します。 一覧から **storageAccountName** パラメーターを選択します。 Bicep 内の名前を使用して、パラメーターを直接参照できることに注意してください。 JSON テンプレートには、parameter() 関数が必要です。

![Bicep リソース内のパラメーターを使用するオートコンプリートを示す図](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Bicep ファイルをデプロイする

`ctrl` + ```` ` ```` キーの組み合わせを使用して統合 Visual Studio Code ターミナルを開き、現在のディレクトリを Bicep ファイルが配置されている場所に変更します。次に、Azure CLI または Azure PowerShell モジュールを使用して、Bicep ファイルをデプロイします。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、Azure CLI か Azure PowerShell のどちらかのモジュールを使用してクイックスタート リソース グループを削除します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [初級者向け Bicep のチュートリアル](./bicep-tutorial-create-first-bicep.md)
