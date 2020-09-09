---
title: チュートリアル - リンク済みテンプレートをデプロイする
description: リンク済みテンプレートをデプロイする方法について説明します
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63383f810b6f5643bad9feb86360745cd2f9b000
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501135"
---
# <a name="tutorial-deploy-a-linked-template"></a>チュートリアル:リンク済みテンプレートをデプロイする

[前のチュートリアル](./deployment-tutorial-local-template.md)では、ローカル コンピューターに保存されたテンプレートをデプロイする方法について説明しました。 複雑なソリューションをデプロイするする場合は、テンプレートを複数に分割し、メイン テンプレートを使用してそれらをデプロイすることができます。 このチュートリアルでは、リンク済みテンプレートへの参照を含んだメイン テンプレートをデプロイする方法について説明します。 メイン テンプレートがデプロイされると、そこからリンク済みテンプレートのデプロイがトリガーされます。 リンク済みテンプレートを保存し、SAS トークンを使用してそのセキュリティを確保する方法についても説明します。 所要時間は約 **12 分**です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、前のチュートリアルを済ませておくことをお勧めします。

## <a name="review-template"></a>テンプレートを確認する

これまでのチュートリアルでは、ストレージ アカウント、App Service プラン、Web アプリを作成するテンプレートをデプロイしました。 使用したテンプレートは次のとおりです。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>リンク済みテンプレートを作成する

リンク済みテンプレートに、ストレージ アカウントのリソースを分離することができます。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

次のテンプレートがメイン テンプレートです。  強調表示された **Microsoft.Resources/deployments** オブジェクトは、リンク済みテンプレートの呼び出し方法を示しています。 リンク済みテンプレートはローカル ファイルに保存できず、また、ローカル ネットワーク上でしか利用できないファイルに保存することもできません。 *http* または *https* のいずれかを含む URI 値のみを指定できます。 Resource Manager は、テンプレートにアクセスできる必要があります。 1 つと選択肢として、ストレージ アカウントにリンク済みテンプレートを配置し、その項目の URI を使用できます。 URI は、パラメーターを使用してテンプレートに渡されます。 強調表示されたパラメーターの定義を参照してください。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

メイン テンプレートのコピーに .json という拡張子を付けてローカル コンピューターに保存します (例: azuredeploy.json)。 リンク済みテンプレートのコピーを保存する必要はありません。  リンク済みテンプレートは GitHub リポジトリからストレージ アカウントにコピーされます。

## <a name="store-the-linked-template"></a>リンク済みテンプレートを保存する

以下の PowerShell スクリプトは、ストレージ アカウントとコンテナーを作成し、そのコンテナーに GitHub リポジトリからリンク済みテンプレートをコピーするものです。 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json) には、リンク済みテンプレートのコピーが格納されています。

**[使ってみる]** を選択して Cloud Shell を開き、 **[コピー]** を選択して PowerShell スクリプトをコピーしたら、シェル ペインを右クリックしてスクリプトを貼り付けます。

> [!IMPORTANT]
> ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用する必要があります。 名前は一意である必要があります。 このテンプレートでは、プロジェクト名に "store" を追加した文字列がストレージ アカウント名になります。プロジェクト名は 3 文字を超え、11 文字未満であることが必要です。 そのためプロジェクト名は、ストレージ アカウント名の要件を満たしていること、また 11 文字未満であることが必要となります。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>テンプレートのデプロイ

ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを生成してテンプレートの URI に含めます。 デプロイの完了に必要な時間を確保できるように有効期限を設定します。 テンプレートを含む BLOB は、アカウントの所有者のみがアクセスできます。 ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。 もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。 SAS トークンはお使いのテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。

まだリソース グループを作成していない場合は、「[リソース グループの作成](./deployment-tutorial-local-template.md#create-resource-group)」を参照してください。

> [!NOTE]
> 下記の Azure CLI コードでは、日付パラメーター -d は macOS の無効な引数です。 そのため、macOS のユーザーは、macOS のターミナルで現在の時刻に 2 時間を追加するために、-v+2H を使用する必要があります。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

リンク済みテンプレートをデプロイする方法について説明しました。 次のチュートリアルでは、テンプレートをデプロイするための DevOp パイプラインを作成する方法について説明します。

> [!div class="nextstepaction"]
> [パイプラインを作成する。](./deployment-tutorial-pipeline.md)
