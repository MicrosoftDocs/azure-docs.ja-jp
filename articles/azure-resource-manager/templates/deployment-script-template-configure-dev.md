---
title: テンプレートでデプロイ スクリプトの開発環境を構成する | Microsoft Docs
description: Azure Resource Manager テンプレート (ARM テンプレート) でデプロイ スクリプトの開発環境を構成します。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: b2e1ffb3cbd513766945864e33589c46284bf942
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200939"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>ARM テンプレートでデプロイ スクリプトの開発環境を構成する

デプロイ スクリプト イメージを使用して ARM テンプレートのデプロイ スクリプトを開発およびテストするための開発環境を作成する方法を学習します。 [Azure コンテナー インスタンス](../../container-instances/container-instances-overview.md)を作成することも、[Docker](https://docs.docker.com/get-docker/) を使用することもできます。 この記事では、両方のオプションについて説明します。

## <a name="prerequisites"></a>前提条件

### <a name="azure-powershell-container"></a>Azure PowerShell コンテナー

Azure PowerShell デプロイ スクリプトがない場合は、次の内容を使用して *hello.ps1* ファイルを作成できます。

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Azure CLI コンテナー

Azure CLI コンテナー イメージの場合は、次の内容を使用して *hello.sh* ファイルを作成できます。

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> Azure CLI デプロイ スクリプトを実行すると、`AZ_SCRIPTS_OUTPUT_PATH` という環境変数にスクリプト出力ファイルの場所が格納されます。 環境変数は、開発環境のコンテナーでは使用できません。 Azure CLI 出力の操作の詳細については、「[CLI スクリプトからの出力を操作する](deployment-script-template.md#work-with-outputs-from-cli-script)」を参照してください。

## <a name="use-azure-powershell-container-instance"></a>Azure PowerShell コンテナー インスタンスを使用する

ご自身のコンピューターでスクリプトを作成するには、ストレージ アカウントを作成し、そのストレージ アカウントをコンテナー インスタンスにマウントする必要があります。 これにより、スクリプトをストレージ アカウントにアップロードし、コンテナー インスタンスでスクリプトを実行できるようになります。

> [!NOTE]
> スクリプトをテストするために作成するストレージ アカウントは、スクリプトを実行するためにデプロイ スクリプト サービスで使用されるストレージ アカウントと同じではありません。 デプロイ スクリプト サービスは、実行のたびに、ファイル共有として一意の名前を作成します。

### <a name="create-an-azure-powershell-container-instance"></a>Azure PowerShell コンテナー インスタンスを作成する

次の Azure Resource Manager テンプレート (ARM テンプレート) は、コンテナー インスタンスとファイル共有を作成し、ファイル共有をコンテナー イメージにマウントします。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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

マウント パスの既定値は `/mnt/azscripts/azscriptinput` です。 これは、ファイル共有にマウントされるコンテナー インスタンス内のパスです。

テンプレートに指定されている既定のコンテナー イメージは **mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2** です。 [サポートされているすべての Azure PowerShell バージョン](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)の一覧を参照してください。

このテンプレートでは、コンテナー インスタンスが 1,800 秒後に中断されます。 コンテナー インスタンスが終了状態になり、セッションが終了するまでの時間は 30 分間です。

テンプレートをデプロイするには、次の手順に従います。

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>デプロイ スクリプトをアップロードする

デプロイ スクリプトをストレージ アカウントにアップロードします。 PowerShell スクリプトの例を次に示します。

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

また、Azure portal または Azure CLI を使用してファイルをアップロードすることもできます。

### <a name="test-the-deployment-script"></a>デプロイ スクリプトをテストする

1. Azure portal で、コンテナー インスタンスとストレージ アカウントをデプロイしたリソース グループを開きます。
2. コンテナー グループを開きます。 既定のコンテナー グループ名は、プロジェクト名に *cg* が追加されたものです。 コンテナー インスタンスは **実行** 状態になっています。
3. リソース メニューで **[コンテナー]** を選択します。 コンテナー インスタンス名は、プロジェクト名に *container* が追加されたものです。

    ![Azure portal 内のデプロイ スクリプトのコンテナー インスタンス接続のスクリーンショット。](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. **[接続]** を選択し、次に **[接続]** を選択します。 コンテナー インスタンスに接続できない場合は、コンテナー グループを再起動して、もう一度試してみてください。
5. コンソール ペインで、次のコマンドを実行します。

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    出力は **Hello John Dole** です。

    ![コンソール内のデプロイ スクリプトのコンテナー インスタンス接続のテスト出力のスクリーンショット。](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Azure CLI コンテナー インスタンスを使用する

ご自身のコンピューターでスクリプトを作成するには、ストレージ アカウントを作成し、そのストレージ アカウントをコンテナー インスタンスにマウントします。 これにより、スクリプトをストレージ アカウントにアップロードし、コンテナー インスタンスでスクリプトを実行できるようになります。

> [!NOTE]
> スクリプトをテストするために作成するストレージ アカウントは、スクリプトを実行するためにデプロイ スクリプト サービスで使用されるストレージ アカウントと同じではありません。 デプロイ スクリプト サービスでは、実行のたびに、ファイル共有として一意の名前が作成されます。

### <a name="create-an-azure-cli-container-instance"></a>Azure CLI コンテナー インスタンスを作成する

次の ARM テンプレートにより、コンテナー インスタンスとファイル共有が作成され、ファイル共有がコンテナー イメージにマウントされます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
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

マウント パスの既定値は `/mnt/azscripts/azscriptinput` です。 これは、ファイル共有にマウントされるコンテナー インスタンス内のパスです。

テンプレートに指定されている既定のコンテナー イメージは **mcr.microsoft.com/azure-cli:2.9.1** です。 [サポートされている Azure CLI バージョン](https://mcr.microsoft.com/v2/azure-cli/tags/list)の一覧を参照してください。

> [!IMPORTANT]
> デプロイ スクリプトでは、Microsoft Container Registry (MCR) から入手可能な CLI イメージが使用されます。 デプロイ スクリプトの CLI イメージの認定には、1 か月ほどかかります。 30 日以内にリリースされた CLI バージョンは使用しないでください。 イメージのリリース日を確認するには、「[Azure CLI リリース ノート](/cli/azure/release-notes-azure-cli)」を参照してください。 サポートされていないバージョンを使用している場合、エラー メッセージにサポートされているバージョンが一覧表示されます。

このテンプレートでは、コンテナー インスタンスが 1,800 秒後に中断されます。 コンテナー インスタンスが終了状態になり、セッションが終了するまでの時間は 30 分間です。

テンプレートをデプロイするには、次の手順に従います。

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>デプロイ スクリプトをアップロードする

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

また、Azure portal または Azure CLI を使用してファイルをアップロードすることもできます。

### <a name="test-the-deployment-script"></a>デプロイ スクリプトをテストする

1. Azure portal で、コンテナー インスタンスとストレージ アカウントをデプロイしたリソース グループを開きます。
1. コンテナー グループを開きます。 既定のコンテナー グループ名は、プロジェクト名に *cg* が追加されたものです。 コンテナー インスタンスは **実行** 状態になっています。
1. リソース メニューで **[コンテナー]** を選択します。 コンテナー インスタンス名は、プロジェクト名に *container* が追加されたものです。

    ![デプロイ スクリプト、コンテナー インスタンスへの接続](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. **[接続]** を選択し、次に **[接続]** を選択します。 コンテナー インスタンスに接続できない場合は、コンテナー グループを再起動して、もう一度試してみてください。
1. コンソール ペインで、次のコマンドを実行します。

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    出力は **Hello John Dole** です。

    ![デプロイ スクリプト、コンテナー インスタンスのテスト](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>Docker を使用する

デプロイ スクリプト開発環境として、事前構成済みの Docker コンテナー イメージを使用できます。 Docker をインストールするには、「[Docker を取得する](https://docs.docker.com/get-docker/)」を参照してください。
また、デプロイ スクリプトが含まれているディレクトリを Docker コンテナーにマウントするようにファイル共有を構成する必要もあります。

1. デプロイ スクリプトのコンテナー イメージをローカル コンピューターにプルします。

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    この例では、PowerShell バージョン 4.3.0 を使用しています。

    MCR から CLI イメージをプルするには、次のコマンドを使用します。

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    この例では、CLI バージョン 2.0.80 を使用します。 デプロイ スクリプトでは、[こちら](https://hub.docker.com/_/microsoft-azure-cli)にある既定の CLI コンテナー イメージが使用されます。

1. Docker イメージをローカルで実行します。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **&lt;host driver letter>** および **&lt;host directory name>** を、共有ドライブ上の既存のフォルダーに置き換えます。 このフォルダーは、コンテナー内の _/data_ フォルダーにマップされます。 _D:\docker_ をマップする例を次に示します。

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** は、コンテナー イメージを維持したままにすることを意味します。

    CLI の例:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 次のスクリーンショットは、共有ドライブに *helloworld.ps1* ファイルがある場合に、PowerShell スクリプトを実行する方法を示しています。

    ![Resource Manager テンプレートのデプロイ スクリプト Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

テストが正常に完了したスクリプトは、テンプレート内のデプロイ スクリプトとして使用できます。

## <a name="next-steps"></a>次のステップ

この記事では、デプロイ スクリプトの使用方法について学習しました。 デプロイ スクリプトのチュートリアルを詳しく見るには、次を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル: ARM テンプレートでデプロイ スクリプトを使用する](./template-tutorial-deployment-script.md)
