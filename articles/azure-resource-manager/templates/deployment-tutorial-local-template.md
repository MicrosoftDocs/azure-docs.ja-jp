---
title: チュートリアル - ローカル Azure Resource Manager テンプレートをデプロイする
description: ローカル コンピューターから Azure Resource Manager テンプレート (ARM テンプレート) をデプロイする方法について説明します
ms.date: 02/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: d8d54acfa345994edcc401170e70495b3826bfdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384233"
---
# <a name="tutorial-deploy-a-local-arm-template"></a>チュートリアル:ローカル ARM テンプレートをデプロイする

ローカル マシンから Azure Resource Manager テンプレート (ARM テンプレート) をデプロイする方法について説明します。 所要時間は約 **8 分** です。

これは、シリーズの最初のチュートリアルです。 このシリーズに取り組む過程で、リンク済みテンプレートを作成してテンプレートをモジュール化し、リンク済みテンプレートをストレージ アカウントに保存します。また、SAS トークンを使用して、リンク済みテンプレートをセキュリティで保護します。テンプレートをデプロイするための DevOps パイプラインを作成する方法も学びます。 このシリーズでは、テンプレートのデプロイについて重点的に説明します。 テンプレートの開発について知りたい方は、[初心者向けチュートリアル](./template-tutorial-create-first-template.md)を参照してください。

## <a name="get-tools"></a>ツールを入手する

まず、テンプレートをデプロイするために必要なツールがあることを確認していきましょう。

### <a name="command-line-deployment"></a>コマンド ライン デプロイ

テンプレートをデプロイするには、Azure PowerShell または Azure CLI が必要です。 インストール手順については、以下を参照してください。

- [Azure PowerShell をインストールするには](/powershell/azure/install-az-ps)
- [Windows での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)
- [Linux での Azure CLI のインストール](/cli/azure/install-azure-cli-linux)
- [macOS での Azure CLI のインストール](/cli/azure/install-azure-cli-macos)

Azure PowerShell または Azure CLI をインストールした後で、初回サインインを行います。 ヘルプ情報については、[PowerShell でのサインイン](/powershell/azure/install-az-ps#sign-in)または [Azure CLI でのサインイン](/cli/azure/get-started-with-azure-cli#sign-in)に関するセクションを参照してください。

### <a name="editor-optional"></a>エディター (省略可能)

テンプレートは JSON ファイルです。 テンプレートを確認または編集するには、適切な JSON エディターが必要です。 Visual Studio Code と Resource Manager Tools 拡張機能をお勧めします。 これらのツールをインストールする必要がある場合は、「[クイックスタート: Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。

## <a name="review-template"></a>テンプレートを確認する

このテンプレートは、ストレージ アカウント、App Service プラン、Web アプリをデプロイするものです。 テンプレートの作成に興味がある場合は、[クイックスタート テンプレートのチュートリアル](template-tutorial-quickstart-template.md)を参照してください。 ただし、このチュートリアルに取り組むうえで必須というわけではありません。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用する必要があります。 名前は一意である必要があります。 このテンプレートでは、プロジェクト名に **store** を追加したものがストレージ アカウント名になります。プロジェクト名は 3 文字を超え、11 文字未満であることが必要です。 そのためプロジェクト名は、ストレージ アカウント名の要件を満たしていること、また 11 文字未満であることが必要となります。

テンプレートのコピーに _.json_ という拡張子を付けてローカル コンピューターに保存します (例: _azuredeploy.json_)。 このテンプレートは、後でこのチュートリアルの中でデプロイします。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure PowerShell または Azure CLI を使用して、テンプレートのデプロイを開始するには、Azure の資格情報を使用してサインインします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

複数の Azure サブスクリプションがある場合は、使用するサブスクリプションを選択します。 `[SubscriptionID/SubscriptionName]` と角かっこ `[]` を実際のサブスクリプション情報に置き換えます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>リソース グループの作成

テンプレートをデプロイするときは、リソースを格納するリソース グループを指定します。 デプロイ コマンドを実行する前に、Azure CLI または Azure PowerShell を使用してリソース グループを作成します。 次のコード セクションのタブを選択し、Azure PowerShell または Azure CLI を選択してください。 この記事の CLI の例は、Bash シェルを対象として記述されています。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>テンプレートのデプロイ

一方または両方のデプロイ オプションを使用してテンプレートをデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Azure PowerShell を使用してテンプレートをデプロイする方法の詳細については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](./deploy-powershell.md)」をご覧ください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Azure CLI を使用してテンプレートをデプロイする方法の詳細については、「[ARM テンプレートと Azure CLI でリソースをデプロイする](./deploy-cli.md)」をご覧ください。

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

ローカル テンプレートをデプロイする方法について説明しました。 次のチュートリアルでは、テンプレートをメイン テンプレートとリンク済みテンプレートに分けたうえで、リンク済みテンプレートを保存してセキュリティを確保する方法について説明します。

> [!div class="nextstepaction"]
> [リンク済みテンプレートをデプロイする](./deployment-tutorial-linked-template.md)
