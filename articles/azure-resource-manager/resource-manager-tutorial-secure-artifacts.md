---
title: テンプレートでの成果物のセキュリティ保護
description: Azure Resource Manager テンプレート内で使用される成果物をセキュリティで保護する方法について説明します。
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1a9d209e843d8e9a1735a3c6907b00d85be6580b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971735"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>チュートリアル: Azure Resource Manager テンプレートのデプロイ時に成果物をセキュリティで保護する

Azure Resource Manager テンプレート内で使用される成果物を Azure Storage アカウントの Shared Access Signature (SAS) を使用してセキュリティで保護する方法について説明します。 デプロイの成果物は、メイン テンプレート ファイルに加え、デプロイを完了するために必要なすべてのファイルです。 たとえば「[チュートリアル: Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)」では、Azure SQL Database がメイン テンプレートによって作成されます。テーブルを作成してデータを挿入するために BACPAC ファイルも呼び出されます。 BACPAC ファイルは成果物として、Azure ストレージ アカウントに格納されます。 成果物へのアクセスには、ストレージ アカウント キーが使用されています。 このチュートリアルでは、SAS を使用して、ご自分の Azure Storage アカウント内の BACPAC ファイルへの制限付きアクセスを許可します。 SAS の詳細については、「[Shared Access Signatures (SAS) の使用](../storage/common/storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

リンクされたテンプレートをセキュリティで保護する方法については、「[チュートリアル: リンクされた Azure Resource Manager テンプレートの作成](./resource-manager-tutorial-create-linked-templates.md)」を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * BACPAC ファイルを準備する
> * 既存のテンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ
> * デプロイを検証する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Visual Studio Code と Resource Manager ツール拡張機能。 「[Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](./resource-manager-tools-vs-code.md)」を参照してください。
* 「[チュートリアル: Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)」を確認します。 このチュートリアルで使用するテンプレートは、そのチュートリアルで開発したものです。 この記事では、完成したテンプレートのダウンロード リンクを提供しています。
* セキュリティを向上させるために、生成されたパスワードを SQL Server 管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、「[チュートリアル: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="prepare-a-bacpac-file"></a>BACPAC ファイルを準備する

このセクションでは、Resource Manager テンプレートをデプロイするときに、セキュリティで保護された方法で BACPAC ファイルにアクセスできるようにそのファイルを準備します。 このセクションには、5 つの手順があります。

* BACPAC ファイルをダウンロードします。
* Azure ストレージ アカウントを作成します。
* ストレージ アカウントに BLOB コンテナーを作成します。
* BACPAC ファイルをコンテナーにアップロードします。
* BACPAC ファイルの SAS トークンを取得します。

1. **[試してみる]** を選択し、Cloud Shell を開いて、シェル ウィンドウに次の PowerShell スクリプトを貼り付けます。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

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
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. BACPAC ファイルの URL と SAS トークンを書き留めます。 これらの値は、テンプレートをデプロイするときに必要です。

## <a name="open-an-existing-template"></a>既存のテンプレートを開く

このセッションでは、「[チュートリアル: Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)」で作成したテンプレートを変更し、SAS トークンを使用して BACPAC ファイルを呼び出します。  SQL 拡張機能チュートリアルで開発されたテンプレートは、[GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json) で共有されています。

1. Visual Studio Code から、 **[ファイル]** > **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

3. **[開く]** を選択して、ファイルを開きます。

    テンプレート内に定義されているリソースは 4 つあります。

   * `Microsoft.Sql/servers` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers)をご覧ください。
   * `Microsoft.SQL/servers/firewallRules` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules)をご覧ください。
   * `Microsoft.SQL/servers/databases`  [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)をご覧ください。
   * `Microsoft.SQL/server/databases/extensions`  [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)をご覧ください。

     カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
4. **[ファイル]** > **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

1. storageAccountKey のパラメーター定義を次のパラメーター定義に置き換えます。

    ```json
    "_artifactsLocationSasToken": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the SAS token required to access the artifact location."
      }
    },
    ```

    ![Resource Manager チュートリアルの成果物をセキュリティで保護するためのパラメーター](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

2. SQL 拡張リソースの 3 つの要素 (下記) の値を更新します。

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

完成したテンプレートは、次のようになります。

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

デプロイ手順については、「[テンプレートのデプロイ](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template)」セクションを参照してください。 代わりに次の PowerShell デプロイ スクリプトを使用します。

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

生成されたパスワードを使用します。 「[前提条件](#prerequisites)」を参照してください。
_artifactsLocation、_artifactsLocationSasToken、および bacpacFileName の値については、「[BACPAC ファイルを準備する](#prepare-a-bacpac-file)」を参照してください。

## <a name="verify-the-deployment"></a>デプロイを検証する

ポータルで、新しくデプロイされたリソース グループから SQL データベースを選択します。 **[クエリ エディター (プレビュー)]** を選択してから、管理者の資格情報を入力します。 データベースにインポートされた 2 つのテーブルが表示されます。

![Azure Resource Manager による SQL 拡張機能 BACPAC のデプロイ](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、SQL サーバーと SQL データベースをデプロイし、SAS トークンを使用して BACPAC ファイルをインポートしました。 Azure パイプラインを作成し、継続的に Resource Manager テンプレートを開発およびデプロイする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Pipelines を使用した継続的インテグレーション](./resource-manager-tutorial-use-azure-pipelines.md)
