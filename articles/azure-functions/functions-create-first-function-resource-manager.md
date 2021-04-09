---
title: Azure Resource Manager テンプレートを使用して初めての関数を作成する
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して、HTTP によってトリガーされる単純なサーバーレス関数を作成し、Azure にデプロイします。
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 1e623405faa89ff41eccdaa57578bc8ac94cd78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422826"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>クイック スタート:ARM テンプレートから Azure Functions リソースを作成してデプロイする

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、HTTP 要求に応答する関数を作成します。 

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

### <a name="azure-account"></a>Azure アカウント 

開始する前に、アクティブなサブスクリプションを含む Azure アカウントが必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

### <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

この記事では、作成した Azure リソースに対して動作するローカル関数コード プロジェクトが必要です。 最初にプロジェクトを作成、発行しておかないと、この記事のデプロイ セクションを完遂できません。 

ご希望の言語で関数アプリを作成するために、次のいずれかのタブを選択し、リンク先のセクションの説明に従ってください。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code を使用し、選択した言語でローカル関数プロジェクトを作成します。  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Visual Studio でローカル関数プロジェクトを作成する](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[コマンド ライン](#tab/command-line)

コマンド ラインから、選択した言語でローカル関数プロジェクトを作成します。

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

プロジェクトをローカルで作成したら、Azure で新しい関数を実行するために必要なリソースを作成します。 

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/)からのものです。

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

このテンプレートでは、次の 4 つの Azure リソースが作成されます。

+ [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): Functions に必要な Azure Storage アカウントを作成します。
+ [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): 関数アプリ用のサーバーレス従量課金制ホスティング プランを作成します。
+ [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): 関数アプリを作成します。
+ [**microsoft.insights/components**](/azure/templates/microsoft.insights/components): 監視用の Application Insights インスタンスを作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>デプロイの検証

次に、プロジェクトを Azure に発行し、関数の HTTP エンドポイントを呼び出すことによって、作成した関数アプリのホスティング リソースを検証します。

### <a name="publish-the-function-project-to-azure"></a>Azure に関数プロジェクトを発行する

新しい Azure リソースにプロジェクトを発行するには、次の手順に従います。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

出力内容から、HTTP トリガーの URL をコピーしてください。 Azure で実行されている関数をテストする際に使用します。 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[発行]** を選択します。

1. **[発行先を選択]** の **[Azure Functions の従量課金制プラン]** で **[既存のものを選択]** を選択し、 **[プロファイルの作成]** を選択します。

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="既存の発行先を選択する":::

1. 該当する **サブスクリプション** を選択し、リソース グループを展開して関数アプリを選択し、 **[OK]** を選択します。

1. 発行が完了したら、 **[サイトの URL]** をコピーします。

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="発行の概要からサイトの URL をコピーする":::

1. `/api/<FUNCTION_NAME>?name=Functions` というパスを追加します (`<FUNCTION_NAME>` は関数の名前)。 HTTP トリガー関数を呼び出す URL は、次の形式になります。

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

この URL を使用して、Azure で実行されている HTTP トリガー関数をテストします。

# <a name="command-line"></a>[コマンド ライン](#tab/command-line)

Azure で ローカル コードを関数アプリに発行するには、`publish` コマンドを使用します。

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

この例では、`<FUNCTION_APP_NAME>` をお使いの関数アプリの名前に置き換えます。 場合によっては、`az login` を使用して、再度サインインする必要があります。 

出力内容から、HTTP トリガーの URL をコピーしてください。 Azure で実行されている関数をテストする際に使用します。

---

### <a name="invoke-the-function-on-azure"></a>Azure 上の関数を呼び出す

コピーした HTTP 要求の URL をブラウザーのアドレス バーに貼り付け、その URL の末尾に `name` クエリ文字列が `?name=Functions` 形式で追加されていることを確認して、要求を実行します。 

次のように、応答が表示されます。

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順に進んで Azure Storage キュー出力バインディングを追加する場合、既存の作業をベースにするので、リソースはすべてそのままにしておいてください。

それ以外の場合は、追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

`<RESOURCE_GROUP_NAME>` は、リソース グループの名前に置き換えます。

## <a name="next-steps"></a>次のステップ

初めての関数を発行した後は、理解を深めるために、出力バインディングを関数に追加してみましょう。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[コマンド ライン](#tab/command-line)

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md)

---
