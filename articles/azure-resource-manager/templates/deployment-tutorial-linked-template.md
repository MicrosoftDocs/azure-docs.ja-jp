---
title: チュートリアル - リンク済みテンプレートをデプロイする
description: リンク済みテンプレートをデプロイする方法について説明します
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589274"
---
# <a name="tutorial-deploy-a-linked-template"></a>チュートリアル:リンク済みテンプレートをデプロイする

[前のチュートリアル](./deployment-tutorial-local-template.md)では、ローカル コンピューターに保存されたテンプレートをデプロイする方法について説明しました。 複雑なソリューションをデプロイするする場合は、テンプレートを複数に分割し、メイン テンプレートを使用してそれらをデプロイすることができます。 このチュートリアルでは、リンク済みテンプレートへの参照を含んだメイン テンプレートをデプロイする方法について説明します。 メイン テンプレートがデプロイされると、そこからリンク済みテンプレートのデプロイがトリガーされます。 テンプレートを保存し、SAS トークンを使用してそのセキュリティを確保する方法についても説明します。 所要時間は約 **12 分** です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、前のチュートリアルを済ませておくことをお勧めします。

## <a name="review-template"></a>テンプレートを確認する

これまでのチュートリアルでは、ストレージ アカウント、App Service プラン、Web アプリを作成するテンプレートをデプロイしました。 使用したテンプレートは次のとおりです。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>リンク済みテンプレートを作成する

リンク済みテンプレートに、ストレージ アカウントのリソースを分離することができます。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

次のテンプレートがメイン テンプレートです。 強調表示された `Microsoft.Resources/deployments` オブジェクトは、リンク済みテンプレートの呼び出し方法を示しています。 リンク済みテンプレートはローカル ファイルに保存できず、また、ローカル ネットワーク上でしか利用できないファイルに保存することもできません。 HTTP または HTTPS のいずれかを含むリンク済みテンプレートの URI 値を指定するか、_relativePath_ プロパティを使用して、親テンプレートに対して相対的な場所にリモートのリンク済みテンプレートを配置することができます。 1 つの選択肢として、メイン テンプレートとリンク済みテンプレートの両方を同じストレージ アカウントに配置する方法があります。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>リンク済みテンプレートを保存する

メイン テンプレートとリンク済みテンプレートは、どちらも GitHub に保存されてい。

次の PowerShell スクリプトにより、ストレージ アカウントとコンテナーが作成され、GitHub リポジトリからそのコンテナーに 2 つのテンプレートがコピーされます。 2 つのテンプレートは以下のとおりです。

- メイン テンプレート: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- リンク済みテンプレート: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

**[使ってみる]** を選択して Cloud Shell を開き、 **[コピー]** を選択して PowerShell スクリプトをコピーしたら、シェル ペインを右クリックしてスクリプトを貼り付けます。

> [!IMPORTANT]
> ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用する必要があります。 名前は一意である必要があります。 このテンプレートでは、プロジェクト名に **store** を追加したものがストレージ アカウント名になります。プロジェクト名は 3 文字を超え、11 文字未満であることが必要です。 そのためプロジェクト名は、ストレージ アカウント名の要件を満たしていること、また 11 文字未満であることが必要となります。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

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

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>テンプレートのデプロイ

ストレージ アカウントにテンプレートをデプロイするには、SAS トークンを生成し、これを _-QueryString_ パラメーターに指定します。 デプロイの完了に必要な時間を確保できるように有効期限を設定します。 テンプレートを含む BLOB は、アカウントの所有者のみがアクセスできます。 ただし、BLOB の SAS トークンを作成すると、その SAS トークンを持つ任意のユーザーが BLOB にアクセスできるようになります。 別のユーザーが URI と SAS トークンを傍受した場合、そのユーザーはテンプレートにアクセスできます。 SAS トークンはお使いのテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。

まだリソース グループを作成していない場合は、「[リソース グループの作成](./deployment-tutorial-local-template.md#create-resource-group)」を参照してください。

> [!NOTE]
> 下記の Azure CLI コードで、`date` パラメーターの `-d` は、macOS では無効な引数です。 そのため、macOS ユーザーは、macOS のターミナルで現在の時刻に 2 時間を追加するために、`-v+2H` を使用する必要があります。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
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

リンク済みテンプレートをデプロイする方法について説明しました。 次のチュートリアルでは、テンプレートをデプロイするための DevOps パイプラインを作成する方法について説明します。

> [!div class="nextstepaction"]
> [パイプラインを作成する。](./deployment-tutorial-pipeline.md)