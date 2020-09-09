---
title: テンプレートでデプロイ スクリプトの開発環境を構成する | Microsoft Docs
description: Azure Resource Manager テンプレートでデプロイ スクリプトの開発環境を構成します。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: jgao
ms.openlocfilehash: 232a1ae5d125a2ea1d5723e85073fb3dd02420cc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293830"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates-preview"></a>テンプレートでデプロイ スクリプトの開発環境を構成する (プレビュー)

デプロイ スクリプト イメージを使用してデプロイ スクリプトを開発およびテストするための開発環境を作成する方法について説明します。 [Azure Container Instance](../../container-instances/container-instances-overview.md) を作成することも、[Docker](https://docs.docker.com/get-docker/) を使用することもできます。 この記事では、この両方について説明します。

## <a name="prerequisites"></a>前提条件

デプロイ スクリプトがない場合は、次の内容で **hello.ps1** ファイルを作成してください。

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Azure Container Instance を使用する

ご自身のコンピューターでスクリプトを作成するには、ストレージ アカウントを作成し、そのストレージ アカウントをコンテナー インスタンスにマウントする必要があります。 これにより、スクリプトをストレージ アカウントにアップロードし、コンテナー インスタンスでスクリプトを実行できるようになります。

> [!NOTE]
> スクリプトをテストするために作成するストレージ アカウントは、スクリプトを実行するためにデプロイ スクリプト サービスで使用されるストレージ アカウントと同じではありません。 デプロイ スクリプト サービスは、実行のたびに、ファイル共有として一意の名前を作成します。

### <a name="create-an-azure-container-instance"></a>Azure Container Instance を作成する

次の ARM テンプレートは、コンテナー インスタンスとファイル共有を作成し、ファイル共有をコンテナー イメージにマウントします。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```
マウント パスの既定値は **deploymentScript** です。  これは、ファイル共有にマウントされるコンテナー インスタンス内のパスです。

テンプレートに指定されている既定のコンテナー イメージは **mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3** です。  サポートされている Azure PowerShell のバージョンおよび Azure CLI のバージョンの一覧については、「[Azure PowerShell または Azure CLI](./deployment-script-template.md#prerequisites)」を参照してください。

このテンプレートは、コンテナー インスタンスを 1800 秒間中断します。 コンテナー インスタンスが終了状態になり、セッションが終了するまでの時間は 30 分間です。

テンプレートをデプロイするには、次の手順に従います。

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>デプロイ スクリプトをアップロードする

デプロイ スクリプトをストレージ アカウントにアップロードします。 PowerShell の例を次に示します。

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

また、Azure portal と Azure CLI を使用してファイルをアップロードすることもできます。

### <a name="test-the-deployment-script"></a>デプロイ スクリプトをテストする

1. Azure portal から、コンテナー インスタンスとストレージ アカウントをデプロイしたリソース グループを開きます。
1. コンテナー グループを開きます。 既定のコンテナー グループ名は、プロジェクト名に **cg** が追加されたものです。 コンテナー インスタンスが **実行**状態であることがわかります。
1. 左側のメニューから **[コンテナー]** を選択します。 コンテナー インスタンスが表示されます。  コンテナー インスタンス名は、プロジェクト名に **container** が追加されたものです。

    ![デプロイ スクリプト、コンテナー インスタンスへの接続](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. **[接続]** を選択し、次に **[接続]** を選択します。 コンテナー インスタンスに接続できない場合は、コンテナー グループを再起動して、再試行してください。
1. コンソール ペインで、次のコマンドを実行します。

    ```
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    出力は **Hello John Dole** です。

    ![デプロイ スクリプト、コンテナー インスタンスのテスト](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-docker"></a>Docker を使用する

デプロイ スクリプト開発環境として、事前構成済みの docker コンテナー イメージを使用できます。 Docker をインストールするには、「[Docker を取得する](https://docs.docker.com/get-docker/)」を参照してください。
また、デプロイ スクリプトが含まれているディレクトリを Docker コンテナーにマウントするようにファイル共有を構成する必要もあります。

1. デプロイ スクリプトのコンテナー イメージをローカル コンピューターにプルします。

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    この例では、PowerShell バージョン 4.3.0 を使用しています。

    Microsoft Container Registry (MCR) から CLI イメージをプルするには、次のようにします。

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    この例では、CLI バージョン 2.0.80 を使用します。 デプロイ スクリプトでは、[こちら](https://hub.docker.com/_/microsoft-azure-cli)にある既定の CLI コンテナー イメージが使用されます。

1. Docker イメージをローカルで実行します。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **&lt;host driver letter>** および **&lt;host directory name>** を、共有ドライブ上の既存のフォルダーに置き換えます。  このフォルダーは、コンテナー内の **/data** フォルダーにマップされます。 D:\docker をマップする例を次に示します。

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** は、コンテナー イメージを維持したままにすることを意味します。

    CLI の例:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 次のスクリーンショットは、共有ドライブに helloworld.ps1 ファイルがある場合に、PowerShell スクリプトを実行する方法を示しています。

    ![Resource Manager テンプレートのデプロイ スクリプト Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

テストが正常に完了したスクリプトは、テンプレート内のデプロイ スクリプトとして使用できます。

## <a name="next-steps"></a>次のステップ

この記事では、デプロイ スクリプトの使用方法について学習しました。 デプロイ スクリプトのチュートリアルを詳しく見るには、次を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Resource Manager テンプレートでデプロイ スクリプトを使用する](./template-tutorial-deployment-script.md)
