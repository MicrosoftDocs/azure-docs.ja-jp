---
title: テンプレートを使用して SQL BACPAC ファイルをインポートする
description: SQL Database 拡張機能を使用して、Azure Resource Manager テンプレートで SQL BACPAC ファイルをインポートする方法について説明します。
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9f5e2e402e13076dc538a9c9d55e5b67e0d86d4f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978905"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>チュートリアル:Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする

Azure SQL Database 拡張機能を使用して Azure Resource Manager テンプレートで BACPAC ファイルをインポートする方法について説明します。 デプロイの成果物は、デプロイを完了するために必要なメイン テンプレート ファイルに加えたすべてのファイルです。 この BACPAC ファイルが成果物です。 このチュートリアルでは、テンプレートを作成して Azure SQL サーバー、SQL データベースをデプロイし、BACPAC ファイルをインポートします。 Azure Resource Manager テンプレートを使用して Azure 仮想マシン拡張機能をデプロイする方法については、「[チュートリアル:Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする](./resource-manager-tutorial-deploy-vm-extensions.md)」を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * BACPAC ファイルを準備する
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ
> * デプロイを検証する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Visual Studio Code と Resource Manager ツール拡張機能。 「[Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](./resource-manager-tools-vs-code.md)」を参照してください。
* セキュリティを向上させるために、生成されたパスワードを SQL Server 管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、「[チュートリアル: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="prepare-a-bacpac-file"></a>BACPAC ファイルを準備する

BACPAC ファイルは、[GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) で共有されています。 独自のものを作成するには、「[Azure SQL データベースを BACPAC ファイルにエクスポートする](../sql-database/sql-database-export.md)」を参照してください。 ファイルを独自の場所に発行する場合は、チュートリアルの後半でテンプレートを更新する必要があります。

BACPAC ファイルは、Resource Manager テンプレートを使用してインポートする前に、Azure Storage アカウントに格納する必要があります。 以下の PowerShell スクリプトは、次の手順で BACPAC ファイルを準備します。

* BACPAC ファイルをダウンロードします。
* Azure ストレージ アカウントを作成します。
* ストレージ アカウントに BLOB コンテナーを作成します。
* BACPAC ファイルをコンテナーにアップロードします。
* ストレージ アカウント キーと BLOB の URL を表示します。

1. **[試してみる]** を選択し、Cloud Shell を開いて、シェル ウィンドウに次の PowerShell スクリプトを貼り付けます。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. ストレージ アカウント キーと BACPAC ファイルの URL を書き留めます。 これらの値は、テンプレートをデプロイするときに必要です。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

このチュートリアルで使用されているテンプレートは、[GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json) に保存されています。

1. Visual Studio Code から、 **[ファイル]** > **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. **[開く]** を選択して、ファイルを開きます。

    テンプレートで定義されている 2 つのリソースがあります。

   * `Microsoft.Sql/servers` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers)をご覧ください。
   * `Microsoft.SQL.servers/databases`  [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)をご覧ください。

     カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
4. **[ファイル]** > **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

1. **parameters** セクションの末尾にさらに 2 つのパラメーターを追加して、ストレージ アカウント キーと BACPAC URL を設定します。

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    **adminPassword** の後にコンマを追加します。 JSON ファイルを VS Code から書式設定するには、**Shift + Alt + F** キーを押します。

    これら 2 つの値の取得については、「[BACPAC ファイルを準備する](#prepare-a-bacpac-file)」を参照してください。

1. テンプレートには 2 つのリソースをさらに追加します。

    * SQL データベース拡張機能で BACPAC ファイルをインポートできるようにするには、Azure サービスからのトラフィックを許可する必要があります。 次のファイアウォール規則定義を SQL サーバー定義の下に追加します。

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        テンプレートは次のようになります。

        ![Azure Resource Manager による SQL 拡張機能 BACPAC のデプロイ](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * 次の JSON を使用して、SQL データベース拡張機能リソースをデータベース定義に追加します。

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        テンプレートは次のようになります。

        ![Azure Resource Manager による SQL 拡張機能 BACPAC のデプロイ](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        リソース定義を理解するには、[SQL Database 拡張機能のリファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions)に関するページを参照してください。 以下にいくつかの重要な要素を示します。

        * **dependsOn**:拡張機能リソースは、SQL データベースが作成された後に作成される必要があります。
        * **storageKeyType**:使用するストレージ キーの種類を指定します。 値は `StorageAccessKey` と `SharedAccessKey` のいずれかにできます。 このチュートリアルでは `StorageAccessKey` を使用します。
        * **storageKey**:BACPAC ファイルが格納されているストレージ アカウントのキーを指定します。 ストレージ キーの種類が SharedAccessKey の場合は、前に "?" が付いている必要があります。
        * **storageUri**:ストレージ アカウントに格納されている BACPAC ファイルの URL を指定します。
        * **administratorLoginPassword**:SQL 管理者のパスワード。 生成されたパスワードを使用します。 「[前提条件](#prerequisites)」を参照してください。

完成したテンプレートは、次のようになります。

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

デプロイ手順については、「[テンプレートのデプロイ](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)」セクションを参照してください。 代わりに次の PowerShell デプロイ スクリプトを使用します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

すべてのリソースが同じリソース グループ内に格納されるように、bacpac ファイルを準備するときに使用したのと同じプロジェクト名を使用することを検討してください。  リソースのクリーンアップなど、リソースの管理がより簡単になります。 同じプロジェクト名を使用した場合は、**New-AzResourceGroup** コマンドをスクリプトから削除したり、既存のリソース グループを更新するかどうかを確認するメッセージが表示されたときに y または n で応答したりすることができます。

生成されたパスワードを使用します。 「[前提条件](#prerequisites)」を参照してください。

## <a name="verify-the-deployment"></a>デプロイを検証する

クライアント コンピューターから SQL サーバーにアクセスするには、他のファイアウォール規則を追加する必要があります。 詳細については、「[IP ファイアウォール規則の作成および管理](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules)」を参照してください。

ポータルで、新しくデプロイされたリソース グループから SQL データベースを選択します。 **[クエリ エディター (プレビュー)]** を選択してから、管理者の資格情報を入力します。 データベースにインポートされた 2 つのテーブルが表示されます。

![Azure Resource Manager による SQL 拡張機能 BACPAC のデプロイ](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、SQL サーバーと SQL データベースをデプロイして、BACPAC ファイルをインポートしました。 BACPAC ファイルは Azure ストレージ アカウントに格納されます。 URL が与えられたユーザーは誰でもファイルにアクセスできます。 BACPAC ファイル (成果物) をセキュリティで保護する方法については次を参照してください。

> [!div class="nextstepaction"]
> [成果物のセキュリティ保護](./resource-manager-tutorial-secure-artifacts.md)
