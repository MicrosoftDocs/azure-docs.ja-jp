---
title: Azure Resource Manager テンプレートを使用して Azure Data Explorer クラスターとデータベースを作成する
description: Azure Resource Manager テンプレートを使用して Azure Data Explorer クラスターとデータベースを作成する方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: oflipman
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 4a95804bcff3653df42186907f03d0bc27a603ea
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869716"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager テンプレート](create-cluster-database-resource-manager.md)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースに取り込み (読み込み) ます。 

この記事では、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-overview.md)を使用して Azure Data Explorer クラスターとデータベースを作成します。 記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。 テンプレートの作成の詳細については、[Azure Resource Manager テンプレートのオーサリング](/azure/azure-resource-manager/resource-group-authoring-templates)に関する記事をご覧ください。 テンプレートで使用する JSON の構文とプロパティについては、「[Microsoft.Kusto のリソースの種類](/azure/templates/microsoft.kusto/allversions)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>クラスターとデータベースを作成するための Azure Resource Manager テンプレート

この記事では、[既存のクイックスタート テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)を使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
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
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

テンプレートのその他のサンプルについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)」をご覧ください。

## <a name="deploy-the-template-and-verify-template-deployment"></a>テンプレートをデプロイし、テンプレートのデプロイを確認する

[Azure portal を使用](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment)するか [PowerShell を使用](#use-powershell-to-deploy-the-template-and-verify-template-deployment)して、Azure Resource Manager テンプレートをデプロイできます。

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Azure portal を使用してテンプレートをデプロイし、テンプレートのデプロイを確認する

1. クラスターとデータベースを作成するには、次のボタンを使用してデプロイを開始します。 この記事の残りの手順を実行できるよう、右クリックして **[新しいウィンドウで開く]** を選択してください。

    [![Azure へのデプロイ](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    **[Deploy to Azure]\(Azure へのデプロイ\)** ボタンをクリックして Azure portal に移動し、デプロイ フォームに入力します。

    ![Deploy to Azure (Azure へのデプロイ)](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    フォームを使用して、[Azure portal でテンプレートの編集とデプロイを実行](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template)できます。

1. **[基本]** セクションと **[設定]** セクションを完了します。 一意のクラスター名とデータベース名を選択します。
Azure Data Explorer クラスターとデータベースの作成には数分かかります。

1. デプロイを確認するには、[Azure portal](https://portal.azure.com) でリソース グループを開き、新しいクラスターとデータベースを検索します。 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>PowerShell を使用してテンプレートをデプロイし、テンプレートのデプロイを確認する

#### <a name="deploy-the-template-using-powershell"></a>PowerShell を使用してテンプレートをデプロイする

1. 次のコード ブロックの **[試してみる]** を選択し、指示に従って Azure Cloud Shell にサインインします。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. **[コピー]** を選択し、PowerShell スクリプトがコピーされます。
1. シェル コンソールを右クリックし、 **[貼り付け]** を選択します。
Azure Data Explorer クラスターとデータベースの作成には数分かかります。

#### <a name="verify-the-deployment-using-powershell"></a>PowerShell を使用してデプロイを確認する

デプロイを確認するには、次の Azure PowerShell スクリプトを使用します。  Cloud Shell がまだ開いている場合は、最初の行 (Read-Host) をコピー/実行する必要はありません。 PowerShell での Azure Data Explorer リソースの管理の詳細については、[Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0) を参照してください。 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>次の手順

[Azure Data Explorer クラスターとデータベースへのデータの取り込み](ingest-data-overview.md)
