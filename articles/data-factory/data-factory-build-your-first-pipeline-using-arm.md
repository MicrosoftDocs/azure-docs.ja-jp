---
title: "初めてのデータ ファクトリの作成 (Resource Manager テンプレート) | Microsoft Docs"
description: "このチュートリアルでは、Azure Resource Manager テンプレートを使用して、サンプルの Azure Data Factory パイプラインを作成します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eb9e70b9-a13a-4a27-8256-2759496be470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 38e8320c09c5aa870018081a29911611f09a3e3a
ms.lasthandoff: 03/24/2017


---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>チュートリアル: Azure Resource Manager テンプレートを使用した初めての Azure Data Factory の作成
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-build-your-first-pipeline.md)
> * [Azure ポータル](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
> 

この記事では、Azure Resource Manager テンプレートを使用して最初の Azure データ ファクトリを作成します。 その他のツールや SDK を使用してチュートリアルを行うには、ドロップダウン リストでいずれかのオプションを選択します。

> [!NOTE]
> このチュートリアルのデータ パイプラインでは、入力データを変換して出力データを生成します。 データをソース データ ストアからターゲット データ ストアにコピーするのではありません。 Azure Data Factory を使用してデータをコピーする方法のチュートリアルについては、[Blob Storage から SQL Database へのデータのコピーのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するページを参照してください。
> 
> 2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) には、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 詳細については、[Data Factory でのスケジュールと実行](data-factory-scheduling-and-execution.md)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件
* 「 [チュートリアルの概要](data-factory-build-your-first-pipeline.md) 」に目を通し、 **前提条件** の手順を完了する必要があります。
* 「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」に記載されている手順に従って、コンピューターに Azure PowerShell の最新バージョンをインストールします。
* Azure Resource Manager テンプレートについては、「 [Azure Resource Manager テンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md) 」を参照してください。 

## <a name="in-this-tutorial"></a>このチュートリアルの内容
| エンティティ | Description |
| --- | --- |
| Azure Storage のリンクされたサービス |Azure Storage アカウントをデータ ファクトリにリンクします。 Azure ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。 |
| HDInsight のオンデマンドのリンクされたサービス |オンデマンド HDInsight クラスターをデータ ファクトリにリンクします。 クラスターは、データ処理のために自動的に作成され、処理が終了すると削除されます。 |
| Azure BLOB の入力データセット |Azure Storage のリンクされたサービスを参照します。 リンクされたサービスは、Azure Storage アカウントを参照します。Azure BLOB データセットは、入力データを保持するストレージ内のコンテナー、フォルダー、ファイルの名前を指定します。 |
| Azure BLOB の出力データセット |Azure Storage のリンクされたサービスを参照します。 リンクされたサービスは、Azure Storage アカウントを参照します。Azure BLOB データセットは、出力データを保持するストレージ内のコンテナー、フォルダー、ファイルの名前を指定します。 |
| データ パイプライン |パイプラインには、HDInsightHive 型のアクティビティが 1 つ含まれています。このアクティビティは、入力データセットを使用し、出力データセットを生成します。 |

データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインには、1 つまたは複数のアクティビティを含めることができます。 アクティビティには、[データ移動アクティビティ](data-factory-data-movement-activities.md)と[データ変換アクティビティ](data-factory-data-transformation-activities.md)の 2 種類があります。 このチュートリアルでは、アクティビティ (コピー アクティビティ) を 1 つ含むパイプラインを作成します。

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
以下の内容を含む **ADFTutorialARM.json** という名前の JSON ファイルを **C:\ADFGetStarted** フォルダーに作成します。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
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
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "HDInsightOnDemand",
                  "typeProperties": {
                    "clusterSize": 1,
                    "version": "3.2",
                    "timeToLive": "00:05:00",
                    "osType": "windows",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
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
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
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
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2016-10-01T00:00:00Z",
                  "end": "2016-10-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> Azure Data Factory を作成するための Resource Manager テンプレートのその他の例については、「[Tutorial: Create a pipeline with Copy Activity using an Azure Resource Manager template (チュートリアル: Azure Resource Manager テンプレートを使用してコピー アクティビティでパイプラインを作成する)](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)」を参照してください。  
> 
> 

## <a name="parameters-json"></a>Parameters JSON
Azure Resource Manager テンプレートのパラメーターを含む **ADFTutorialARM-Parameters.json** という名前の JSON ファイルを作成します。  

> [!IMPORTANT]
> このパラメーター ファイルの **storageAccountName** パラメーターと **storageAccountKey** パラメーターに、Azure Storage アカウントの名前とキーを指定します。 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
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
   * 次のコマンドを実行して、使用するサブスクリプションを選択します。 このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。
    ```
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```   
2. 次のコマンドを実行し、手順 1. で作成した Resource Manager テンプレートを使用して Data Factory エンティティをデプロイします。 

    ```PowerShell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>パイプラインを監視する
1. [Azure Portal](https://portal.azure.com/) にログインした後、**[参照]** をクリックして **[データ ファクトリ]** を選択します。
     ![[参照] > [データ ファクトリ]](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. **[データ ファクトリ]** ブレードで、作成したデータ ファクトリ (**TutorialFactoryARM**) をクリックします。    
3. 該当するデータ ファクトリの **[Data Factory]** ブレードで **[ダイアグラム]** をクリックします。

     ![Diagram Tile](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. **ダイアグラム ビュー**に、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。
   
   ![ダイアグラム ビュー](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. ダイアグラム ビューで、 **AzureBlobOutput**データセットをダブルクリックします。 現在処理中のスライスが表示されます。
   
    ![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. 処理が完了すると、スライスの状態に **[準備完了]** が表示されます。 オンデマンド HDInsight クラスターの作成には通常しばらく時間がかかります (約 20 分)。 そのため、パイプラインによるスライスの処理に **約 30 分** かかると想定してください。
   
    ![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. スライスが**準備完了**状態になったら、Blob Storage の **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。  

Azure ポータル ブレードを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については、 [データセットとパイプラインの監視](data-factory-monitor-manage-pipelines.md) に関するページを参照してください。

データ パイプラインは、監視と管理アプリを使用して監視することもできます。 このアプリケーションの使い方の詳細については、 [監視アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md) に関する記事を参照してください。 

> [!IMPORTANT]
> 入力ファイルは、スライスが正常に処理された時点で削除されます。 そのためスライスを取得したり、このチュートリアルをもう一度行ったりする場合は、adfgetstarted コンテナーの inputdata フォルダーに入力ファイル (input.log) をアップロードしてください。
> 
> 

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
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
これは、リソース グループ ID に基づく一意の文字列です。  

### <a name="defining-data-factory-entities"></a>Data Factory エンティティの定義
JSON テンプレートには、次の Data Factory エンティティが定義されています。 

* [Azure Storage のリンクされたサービス](#azure-storage-linked-service)
* [HDInsight のオンデマンドのリンクされたサービス](#hdinsight-on-demand-linked-service)
* [Azure BLOB の入力データセット](#azure-blob-input-dataset)
* [Azure BLOB の出力データセット](#azure-blob-output-dataset)
* [コピー アクティビティを含むデータ パイプライン](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
このセクションで、Azure Storage アカウントの名前とキーを指定します。 Azure Storage のリンクされたサービスの定義に使用する JSON プロパティの詳細については、「[Azure Storage のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-linked-service)」を参照してください。 

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
**connectionString** では、storageAccountName パラメーターと storageAccountKey パラメーターを使用しています。 これらのパラメーターの値は、構成ファイルを使用して渡されます。 この定義では、テンプレートで定義された azureStroageLinkedService、dataFactoryName の各変数も使用しています。 

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight のオンデマンドのリンクされたサービス
HDInsight のオンデマンドのリンクされたサービスを定義するときに使用する JSON プロパティの詳細については、「[コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)」を参照してください。  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
          "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "HDInsightOnDemand",
          "typeProperties": {
            "clusterSize": 1,
            "version": "3.2",
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
          }
    }
}
```
以下の点に注意してください。 

* Data Factory は、上記の JSON で **Windows ベース** の HDInsight クラスターを自動的に作成します。 **Linux ベース** の HDInsight クラスターを作成させることもできます。 詳細については、 [オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) に関するセクションを参照してください。 
* オンデマンド HDInsight クラスターの代わりに、 **独自の HDInsight クラスター** を使用できます。 詳細については、 [HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) に関するセクションを参照してください。
* HDInsight クラスターは、JSON (**linkedServiceName**) で指定した BLOB ストレージに**既定のコンテナー**を作成します。 クラスターを削除しても、HDInsight はこのコンテナーを削除しません。 この動作は仕様です。 オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスを処理する必要があるたびに HDInsight クラスターが作成され、処理が終了すると削除されます。
  
    処理されるスライスが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、"adf**データ ファクトリ名**-**リンクされたサービス名**-日時スタンプ" というパターンに従います。 Azure BLOB ストレージ内のコンテナーを削除するには、 [Microsoft ストレージ エクスプローラー](http://storageexplorer.com/) などのツールを使用します。

詳細については、 [オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) に関するセクションを参照してください。

#### <a name="azure-blob-input-dataset"></a>Azure BLOB の入力データセット
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
          "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          },
          "external": true
    }
}
```
この定義では、パラメーター テンプレートで定義されているパラメーター blobContainer、inputBlobFolder、および inputBlobName が使用されます。 

#### <a name="azure-blob-output-dataset"></a>Azure BLOB の出力データセット
出力データを格納する BLOB コンテナーとフォルダーの名前を指定します。 Azure BLOB データセットの定義に使用する JSON プロパティの詳細については、[Azure BLOB データセットのプロパティ](data-factory-azure-blob-connector.md#dataset-properties)に関するセクションをご覧ください。  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
          "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          }
    }
}
```

この定義では、パラメーター テンプレートで定義されているパラメーター blobContainer および outputBlobFolder が使用されます。 

#### <a name="data-pipeline"></a>データ パイプライン
オンデマンドの Azure HDInsight クラスターで Hive スクリプトを実行して、データを変換するパイプラインを定義します。 この例のパイプラインの定義に使用されている JSON 要素については、「[パイプライン JSON](data-factory-create-pipelines.md#pipeline-json)」を参照してください。 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('hdInsightOnDemandLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "description": "Pipeline that transforms data using Hive script.",
          "activities": [
        {
              "type": "HDInsightHive",
              "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                      "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                      "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
              },
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('blobOutputDatasetName')]"
            }
              ],
              "policy": {
                "concurrency": 1,
                "retry": 3
              },
              "scheduler": {
                "frequency": "Month",
                "interval": 1
              },
              "name": "RunSampleHiveActivity",
              "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
        }
          ],
          "start": "2016-10-01T00:00:00Z",
          "end": "2016-10-02T00:00:00Z",
          "isPaused": false
    }
}
```

## <a name="reuse-the-template"></a>テンプレートの再利用
このチュートリアルでは、Data Factory エンティティを定義するためのテンプレートと、パラメーターの値を渡すためのテンプレートを作成しました。 同じテンプレートを使用して、Data Factory エンティティをさまざまな環境にデプロイするには、環境ごとにパラメーター ファイルを作成し、その環境にデプロイするときに使用します。     

例:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
最初のコマンドでは開発環境用、2 番目のコマンドではテスト環境用、3 番目のコマンドでは運用環境用のパラメーター ファイルをそれぞれ使用していることに注意してください。  

テンプレートを再利用して、繰り返されるタスクを実行することもできます。 たとえば、1 つ以上のパイプラインを持つ多数のデータ ファクトリを作成する必要があるとします。各データ ファクトリは同じロジックを実装しますが、それぞれ異なる Azure Storage アカウントと Azure SQL Database アカウントを使用します。 このシナリオでは、1 つの環境 (開発、テスト、または運用) で同じテンプレートと異なるパラメーター ファイルを使用してデータ ファクトリを作成します。 

## <a name="resource-manager-template-for-creating-a-gateway"></a>ゲートウェイを作成するための Resource Manager テンプレート
背後で論理ゲートウェイを作成するためのサンプル Resource Manager テンプレートを次に示します。 オンプレミス コンピューターまたは Azure IaaS VM にゲートウェイをインストールし、キーを使用して Data Factory サービスにゲートウェイを登録します。 詳細については、 [オンプレミスとクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事を参照してください。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
このテンプレートでは、GatewayUsingARM という名前のゲートウェイで GatewayUsingArmDF という名前のデータ ファクトリを作成しています。 

## <a name="see-also"></a>関連項目
| トピック | 説明 |
|:--- |:--- |
| [パイプライン](data-factory-create-pipelines.md) |この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法を説明します。 |
| [データセット](data-factory-create-datasets.md) |この記事では、Azure Data Factory のデータセットについて説明します。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) |この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [監視アプリを使用したパイプラインの監視と管理に関する記事](data-factory-monitor-manage-app.md) |この記事では、監視と管理アプリを使用してパイプラインを監視、管理、デバッグする方法について説明します。 |


