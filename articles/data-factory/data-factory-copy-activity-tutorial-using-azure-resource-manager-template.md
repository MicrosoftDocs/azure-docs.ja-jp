---
title: "チュートリアル: Resource Manager テンプレートを使用してパイプラインを作成する | Microsoft Docs"
description: "このチュートリアルでは、Azure Resource Manager テンプレートを使用して、コピー アクティビティを含む Azure Data Factory パイプラインを作成します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 977fcf74c86e2ab312d99b3f1821cfb7efbe9745
ms.lasthandoff: 03/24/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>チュートリアル: Azure Resource Manager テンプレートを使用してコピー アクティビティを含むパイプラインを作成する
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
> * [Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

このチュートリアルでは、Azure Resource Manager テンプレートを使用して、Azure Data Factory を作成し、監視する方法について説明します。 このデータ ファクトリのパイプラインでは、Azure Blob Storage から Azure SQL Database にデータをコピーします。

> [!NOTE]
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 入力データを変換して出力データを生成するのではありません。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換する最初のパイプラインを作成する方法に関するチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。
> 
> 2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) には、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 詳細については、[Data Factory でのスケジュールと実行](data-factory-scheduling-and-execution.md)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件
* [チュートリアルの概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に目を通し、**前提条件**の手順を完了します。
* 「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」に記載されている手順に従って、コンピューターに Azure PowerShell の最新バージョンをインストールします。 このチュートリアルでは、PowerShell を使用して Data Factory エンティティをデプロイします。 
* (省略可能)「[Azure Resource Manager テンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照して、Azure Resource Manager テンプレートについて学びます。

## <a name="in-this-tutorial"></a>このチュートリアルの内容
このチュートリアルでは、次の Data Factory エンティティを含むデータ ファクトリを作成します。

| エンティティ | Description |
| --- | --- |
| Azure Storage のリンクされたサービス |Azure Storage アカウントをデータ ファクトリにリンクします。 このチュートリアルのコピー アクティビティでは、Azure Storage がソース データ ストアであり、Azure SQL Database がシンク データ ストアです。 コピー アクティビティの入力データを格納するストレージ アカウントを指定します。 |
| Azure SQL Database のリンクされたサービス |Azure SQL Database をデータ ファクトリにリンクします。 コピー アクティビティの出力データを保持する Azure SQL Database を指定します。 |
| Azure BLOB の入力データセット |Azure Storage のリンクされたサービスを参照します。 リンクされたサービスは、Azure Storage アカウントを参照します。Azure BLOB データセットは、入力データを保持するストレージ内のコンテナー、フォルダー、ファイルの名前を指定します。 |
| Azure SQL の出力データセット |Azure SQL のリンクされたサービスを参照します。 Azure SQL のリンクされたサービスは、Azure SQL サーバーを参照します。Azure SQL データセットは、出力データを保持するテーブルの名前を指定します。 |
| データ パイプライン |パイプラインには、Azure BLOB データセットを入力として取得し、Azure SQL データセットを出力として取得する Copy 型のアクティビティが 1 つ含まれます。 コピー アクティビティでは、Azure BLOB から Azure SQL Database のテーブルにデータをコピーします。 |

データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインには、1 つまたは複数のアクティビティを含めることができます。 アクティビティには、[データ移動アクティビティ](data-factory-data-movement-activities.md)と[データ変換アクティビティ](data-factory-data-transformation-activities.md)の 2 種類があります。 このチュートリアルでは、アクティビティ (コピー アクティビティ) を 1 つ含むパイプラインを作成します。

![Azure BLOB から Azure SQL Database へのコピー](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

次のセクションでは、このチュートリアルを速やかに実行し、テンプレートをテストできるように、Data Factory エンティティを定義するための完全な Resource Manager テンプレートを提供します。 各 Data Factory エンティティを定義する方法については、「[テンプレートの Data Factory エンティティ](#data-factory-entities-in-the-template)」をご覧ください。

## <a name="data-factory-json-template"></a>Data Factory JSON テンプレート
データ ファクトリを定義するための大まかな Resource Manager テンプレートは次のとおりです。 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "frequency": "Day",
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
                "frequency": "Day",
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
              "start": "2016-10-02T00:00:00Z",
              "end": "2016-10-03T00:00:00Z"
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
> **storageAccountName** パラメーターと **storageAccountKey** パラメーターに、Azure Storage アカウントの名前とキーを指定します。  
> 
> 

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
    Login-AzureRmAccount       
    ```  
   * 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。
    ```PowerShell
    Get-AzureRmSubscription
    ```   
   * 次のコマンドを実行して、使用するサブスクリプションを選択します。 
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```    
2. 次のコマンドを実行し、手順 1. で作成した Resource Manager テンプレートを使用して Data Factory エンティティをデプロイします。

    ```PowerShell   
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>パイプラインを監視する

1. Azure アカウントを使用して [Azure Portal](https://portal.azure.com) にログインします。
2. 左側のメニューの **[データ ファクトリ]** をクリックするか、**[その他のサービス]** をクリックし、**[インテリジェンス + 分析]** カテゴリの**[データ ファクトリ]** をクリックします。
   
    ![[データ ファクトリ] メニュー](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. **[データ ファクトリ]** ページで、使用するデータ ファクトリを検索して見つけます。 
   
    ![データ ファクトリの検索](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. 使用する Azure Data Factory をクリックします。 データ ファクトリのホーム ページが表示されます。
   
    ![データ ファクトリのホーム ページ](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. **[ダイアグラム]** タイルをクリックすると、データ ファクトリのダイアグラム ビューが表示されます。
   
    ![データ ファクトリのダイアグラム ビュー](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. ダイアグラム ビューで、**SQLOutputDataset** データセットをダブルクリックします。 スライスの状態が表示されます。 コピー操作が完了すると、状態が **[準備完了]** に設定されます。
   
    ![準備完了状態の出力スライス](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. スライスが **[準備完了]** 状態になったら、Azure SQL Database の **emp** テーブルにデータがコピーされていることを確認します。

Azure ポータル ブレードを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については、 [データセットとパイプラインの監視](data-factory-monitor-manage-pipelines.md) に関するページを参照してください。

データ パイプラインは、監視と管理アプリを使用して監視することもできます。 このアプリケーションの使い方の詳細については、 [監視アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md) に関する記事を参照してください。

## <a name="data-factory-entities-in-the-template"></a>テンプレートの Data Factory エンティティ
### <a name="define-data-factory"></a>データ ファクトリの定義
次のサンプルに示すように、Resource Manager テンプレートでデータ ファクトリを定義します。  

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
このセクションで、Azure Storage アカウントの名前とキーを指定します。 Azure Storage のリンクされたサービスの定義に使用する JSON プロパティの詳細については、「[Azure Storage のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-linked-service)」をご覧ください。 

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

connectionString では、storageAccountName パラメーターと storageAccountKey パラメーターを使用しています。 これらのパラメーターの値は、構成ファイルを使用して渡されます。 この定義では、テンプレートで定義された azureStroageLinkedService、dataFactoryName の各変数も使用しています。 

#### <a name="azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービス
このセクションで、Azure SQL サーバー名、データベース名、ユーザー名、ユーザー パスワードを指定します。 Azure SQL のリンクされたサービスの定義に使用する JSON プロパティの詳細については、[Azure SQL のリンクされたサービス](data-factory-azure-sql-connector.md#linked-service-properties)に関するセクションをご覧ください。  

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
入力データを格納する BLOB コンテナー、フォルダー、ファイルの名前を指定します。 Azure BLOB データセットの定義に使用する JSON プロパティの詳細については、[Azure BLOB データセットのプロパティ](data-factory-azure-blob-connector.md#dataset-properties)に関するセクションをご覧ください。 

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
            "frequency": "Day",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL データセット
Azure Blob Storage からコピーしたデータを保持する、Azure SQL Database のテーブルの名前を指定します。 Azure SQL データセットの定義に使用する JSON プロパティの詳細については、[Azure SQL データセットのプロパティ](data-factory-azure-sql-connector.md#dataset-properties)に関するセクションをご覧ください。 

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
            "frequency": "Day",
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
          "start": "2016-10-02T00:00:00Z",
          "end": "2016-10-03T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>テンプレートの再利用
このチュートリアルでは、Data Factory エンティティを定義するためのテンプレートと、パラメーターの値を渡すためのテンプレートを作成しました。 パイプラインでは、パラメーターで指定された Azure Storage アカウントから Azure SQL Database にデータをコピーします。 同じテンプレートを使用して、Data Factory エンティティをさまざまな環境にデプロイするには、環境ごとにパラメーター ファイルを作成し、その環境にデプロイするときに使用します。     

例:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

最初のコマンドでは開発環境用、2 番目のコマンドではテスト環境用、3 番目のコマンドでは運用環境用のパラメーター ファイルをそれぞれ使用していることに注意してください。  

テンプレートを再利用して、繰り返されるタスクを実行することもできます。 たとえば、1 つ以上のパイプラインを持つ多数のデータ ファクトリを作成する必要があるとします。各データ ファクトリは同じロジックを実装しますが、それぞれ異なる Azure Storage アカウントと Azure SQL Database アカウントを使用します。 このシナリオでは、1 つの環境 (開発、テスト、または運用) で同じテンプレートと異なるパラメーター ファイルを使用してデータ ファクトリを作成します。   

## <a name="see-also"></a>関連項目
| トピック | 説明 |
|:--- |:--- |
| [パイプライン](data-factory-create-pipelines.md) |この記事では、Azure Data Factory のパイプラインとアクティビティについて説明します。 |
| [データセット](data-factory-create-datasets.md) |この記事では、Azure Data Factory のデータセットについて説明します。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) |この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
