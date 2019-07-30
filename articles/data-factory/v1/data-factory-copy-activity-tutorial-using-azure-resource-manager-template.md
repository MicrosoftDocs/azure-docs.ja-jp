---
title: チュートリアル:Resource Manager テンプレートを使用してパイプラインを作成する | Microsoft Docs
description: このチュートリアルでは、Azure Resource Manager テンプレートを使用して、Azure Data Factory パイプラインを作成します。 このパイプラインは、Azure Blob Storage から Azure SQL データベースにデータをコピーします。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f4d972620d03bb856f06040f0a9b2d6f53f5873b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836614"
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>チュートリアル:Azure Resource Manager テンプレートを使用して、データをコピーする Data Factory パイプラインを作成する 
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コピー アクティビティのチュートリアル](../quickstart-create-data-factory-dot-net.md)に関するページを参照してください。 

このチュートリアルでは、Azure Resource Manager テンプレートを使用して Azure データ ファクトリを作成する方法を説明します。 このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 入力データを変換して出力データを生成するのではありません。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換するパイプラインを作成する方法のチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。

このチュートリアルでは、1 つのアクティビティ (コピー アクティビティ) が含まれたパイプラインを作成します。 コピー アクティビティは、サポートされているデータ ストアからサポートされているシンク データ ストアにデータをコピーします。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションを参照してください。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

1 つのパイプラインには複数のアクティビティを含めることができます。 また、1 つのアクティビティの出力データセットを別のアクティビティの入力データセットとして指定することで、2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) ことができます。 詳細については、「[パイプライン内の複数アクティビティ](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)」を参照してください。 

> [!NOTE] 
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換するパイプラインを作成する方法のチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [チュートリアルの概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に目を通し、**前提条件**の手順を完了します。
* 「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview) 」に記載されている手順に従って、コンピューターに Azure PowerShell の最新バージョンをインストールします。 このチュートリアルでは、PowerShell を使用して Data Factory エンティティをデプロイします。 
* (省略可能)「[Azure Resource Manager テンプレートの作成](../../azure-resource-manager/resource-group-authoring-templates.md)」を参照して、Azure Resource Manager テンプレートについて学びます。

## <a name="in-this-tutorial"></a>このチュートリアルの内容
このチュートリアルでは、次の Data Factory エンティティを含むデータ ファクトリを作成します。

| エンティティ | 説明 |
| --- | --- |
| Azure Storage のリンクされたサービス |Azure Storage アカウントをデータ ファクトリにリンクします。 このチュートリアルのコピー アクティビティでは、Azure Storage がソース データ ストアであり、Azure SQL データベースがシンク データ ストアです。 コピー アクティビティの入力データを格納するストレージ アカウントを指定します。 |
| Azure SQL Database のリンクされたサービス |Azure SQL データベースをデータ ファクトリにリンクします。 コピー アクティビティの出力データを保持する Azure SQL データベースを指定します。 |
| Azure BLOB の入力データセット |Azure Storage のリンクされたサービスを参照します。 リンクされたサービスは、Azure Storage アカウントを参照します。Azure BLOB データセットは、入力データを保持するストレージ内のコンテナー、フォルダー、ファイルの名前を指定します。 |
| Azure SQL の出力データセット |Azure SQL のリンクされたサービスを参照します。 Azure SQL のリンクされたサービスは、Azure SQL サーバーを参照します。Azure SQL データセットは、出力データを保持するテーブルの名前を指定します。 |
| データ パイプライン |パイプラインには、Azure BLOB データセットを入力として取得し、Azure SQL データセットを出力として取得する Copy 型のアクティビティが 1 つ含まれます。 コピー アクティビティでは、Azure BLOB から Azure SQL データベースのテーブルにデータをコピーします。 |

データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインには、1 つまたは複数のアクティビティを含めることができます。 アクティビティには、[データ移動アクティビティ](data-factory-data-movement-activities.md)と[データ変換アクティビティ](data-factory-data-transformation-activities.md)の 2 種類があります。 このチュートリアルでは、アクティビティ (コピー アクティビティ) を 1 つ含むパイプラインを作成します。

![Azure BLOB から Azure SQL Database へのコピー](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

次のセクションでは、このチュートリアルを速やかに実行し、テンプレートをテストできるように、Data Factory エンティティを定義するための完全な Resource Manager テンプレートを提供します。 各 Data Factory エンティティを定義する方法については、「[テンプレートの Data Factory エンティティ](#data-factory-entities-in-the-template)」をご覧ください。

## <a name="data-factory-json-template"></a>Data Factory JSON テンプレート
データ ファクトリを定義するための大まかな Resource Manager テンプレートは次のとおりです。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
**C:\ADFGetStarted** フォルダーに、次の内容を記述した **ADFCopyTutorialARM.json** という名前の JSON ファイルを作成します。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureStorage",
              "description": "Azure Storage linked service",
              "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
              }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
              }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureBlob",
              "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]",
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>Parameters JSON
Azure Resource Manager テンプレートのパラメーターを含む **ADFCopyTutorialARM-Parameters.json** という名前の JSON ファイルを作成します。 

> [!IMPORTANT]
> storageAccountName パラメーターと storageAccountKey パラメーターに、Azure Storage アカウントの名前とキーを指定します。  
> 
> sqlServerName、databaseName、sqlServerUserName、sqlServerPassword の各パラメーターに、Azure SQL サーバー、データベース、ユーザー、パスワードを指定します。  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the Azure SQL server>" },
        "databaseName": { "value": "<Name of the Azure SQL database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
    }
}
```

> [!IMPORTANT]
> 開発環境、テスト環境、運用環境用にそれぞれ別の parameter JSON ファイルを作成して、同じ Data Factory JSON テンプレートで使用できます。 PowerShell スクリプトを使用して、これらの環境への Data Factory エンティティのデプロイを自動化できます。  
> 
> 

## <a name="create-data-factory"></a>データ ファクトリの作成
1. **Azure PowerShell** を起動し、次のコマンドを実行します。
   * 次のコマンドを実行して、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。
   
    ```PowerShell
    Connect-AzAccount       
    ```  
   * 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。
   
    ```PowerShell
    Get-AzSubscription
    ```   
   * 次のコマンドを実行して、使用するサブスクリプションを選択します。
    
    ```PowerShell
    Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
    ```    
2. 次のコマンドを実行し、手順 1. で作成した Resource Manager テンプレートを使用して Data Factory エンティティをデプロイします。

    ```PowerShell   
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>パイプラインを監視する

1. Azure アカウントを使用して [Azure Portal](https://portal.azure.com) にログインします。
2. 左側のメニューの **[データ ファクトリ]** をクリックするか、 **[すべてのサービス]** をクリックし、 **[インテリジェンス + 分析]** カテゴリの **[データ ファクトリ]** をクリックします。
   
    ![[データ ファクトリ] メニュー](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. **[データ ファクトリ]** ページで、使用するデータ ファクトリ (AzureBlobToAzureSQLDatabaseDF) を検索して見つけます。 
   
    ![データ ファクトリの検索](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. 使用する Azure Data Factory をクリックします。 データ ファクトリのホーム ページが表示されます。
   
    ![データ ファクトリのホーム ページ](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
6. [データセットとパイプラインの監視](data-factory-monitor-manage-pipelines.md)に関するセクションの手順に従って、このチュートリアルで作成したパイプラインとデータセットを監視します。 現時点では、Visual Studio は Data Factory パイプラインの監視をサポートしていません。
7. スライスが**準備完了**状態になったら、Azure SQL データベースの **emp** テーブルにデータがコピーされていることを確認します。


Azure Portal のブレードを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法の詳細については、[データセットとパイプラインの監視](data-factory-monitor-manage-pipelines.md)に関するページを参照してください。

監視と管理アプリケーションを使用してデータ パイプラインを監視する方法については、[監視アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関するページを参照してください。

## <a name="data-factory-entities-in-the-template"></a>テンプレートの Data Factory エンティティ
### <a name="define-data-factory"></a>データ ファクトリの定義
次のサンプルに示されているように、Resource Manager テンプレートにデータ ファクトリを定義します。  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```

dataFactoryName は次のように定義されています。 

```json
"dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
```

これは、リソース グループ ID に基づく一意の文字列です。  

### <a name="defining-data-factory-entities"></a>Data Factory エンティティの定義
JSON テンプレートには、次の Data Factory エンティティが定義されています。 

1. [Azure Storage のリンクされたサービス](#azure-storage-linked-service)
2. [Azure SQL のリンクされたサービス](#azure-sql-database-linked-service)
3. [Azure BLOB データセット](#azure-blob-dataset)
4. [Azure SQL データセット](#azure-sql-dataset)
5. [コピー アクティビティを含むデータ パイプライン](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
AzureStorageLinkedService は、Azure ストレージ アカウントをデータ ファクトリにリンクします。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、コンテナーを作成し、データをこのストレージ アカウントにアップロードしました。 このセクションで、Azure Storage アカウントの名前とキーを指定します。 Azure Storage のリンクされたサービスの定義に使用する JSON プロパティの詳細については、「[Azure Storage のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-linked-service)」をご覧ください。 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```

connectionString では、storageAccountName パラメーターと storageAccountKey パラメーターを使用しています。 これらのパラメーターの値は、構成ファイルを使用して渡されます。 この定義では、テンプレートで定義された azureStorageLinkedService、dataFactoryName の各変数も使用しています。 

#### <a name="azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービス
AzureSqlLinkedService は、Azure SQL データベースをデータ ファクトリにリンクします。 Blob Storage からコピーされたデータは、このデータベースに格納されます。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、このデータベースに emp テーブルを作成しました。 このセクションで、Azure SQL サーバー名、データベース名、ユーザー名、ユーザー パスワードを指定します。 Azure SQL のリンクされたサービスの定義に使用する JSON プロパティの詳細については、[Azure SQL のリンクされたサービス](data-factory-azure-sql-connector.md#linked-service-properties)に関するセクションをご覧ください。  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlDatabase",
          "description": "Azure SQL linked service",
          "typeProperties": {
            "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
          }
    }
}
```

connectionString では、sqlServerName、databaseName、sqlServerUserName、sqlServerPassword の各パラメーターを使用しています。これらのパラメーターの値は、構成ファイルを使用して渡されます。 この定義では、テンプレートの azureSqlLinkedServiceName、dataFactoryName の各変数も使用しています。

#### <a name="azure-blob-dataset"></a>Azure BLOB データセット
Azure Storage のリンクされたサービスは、Data Factory サービスが実行時に Azure ストレージ アカウントへの接続に使用する接続文字列を指定します。 Azure BLOB データセット定義では、入力データが格納される BLOB コンテナー、フォルダー、ファイルの名前を指定します。 Azure BLOB データセットの定義に使用する JSON プロパティの詳細については、[Azure BLOB データセットのプロパティ](data-factory-azure-blob-connector.md#dataset-properties)に関するセクションをご覧ください。 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]",
      "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL データセット
Azure Blob Storage からコピーしたデータを保持する、Azure SQL データベースのテーブルの名前を指定します。 Azure SQL データセットの定義に使用する JSON プロパティの詳細については、[Azure SQL データセットのプロパティ](data-factory-azure-sql-connector.md#dataset-properties)に関するセクションをご覧ください。 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>データ パイプライン
Azure BLOB データセットから Azure SQL データセットにデータをコピーするパイプラインを定義します。 この例のパイプラインの定義に使用されている JSON 要素については、「[パイプライン JSON](data-factory-create-pipelines.md#pipeline-json)」をご覧ください。 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>テンプレートの再利用
このチュートリアルでは、Data Factory エンティティを定義するためのテンプレートと、パラメーターの値を渡すためのテンプレートを作成しました。 パイプラインでは、パラメーターで指定された Azure Storage アカウントから Azure SQL データベースにデータをコピーします。 同じテンプレートを使用して、Data Factory エンティティをさまざまな環境にデプロイするには、環境ごとにパラメーター ファイルを作成し、その環境にデプロイするときに使用します。     

例:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

最初のコマンドでは開発環境用、2 番目のコマンドではテスト環境用、3 番目のコマンドでは運用環境用のパラメーター ファイルをそれぞれ使用していることに注意してください。  

テンプレートを再利用して、繰り返されるタスクを実行することもできます。 たとえば、1 つ以上のパイプラインを持つ多数のデータ ファクトリを作成する必要があるとします。各データ ファクトリは同じロジックを実装しますが、それぞれ異なる Storage アカウントと SQL Database アカウントを使用します。 このシナリオでは、1 つの環境 (開発、テスト、または運用) で同じテンプレートと異なるパラメーター ファイルを使用してデータ ファクトリを作成します。   

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Blob Storage をコピー操作のソース データ ストア、Azure SQL データベースをターゲット データ ストアとして使用しました。 次の表は、コピー アクティビティによってソースおよびターゲットとしてサポートされているデータ ストアの一覧です。 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

データ ストアにデータをコピーしたり、データ ストアからデータをコピーしたりする方法を確認するには、表のデータ ストアのリンクをクリックしてください。
