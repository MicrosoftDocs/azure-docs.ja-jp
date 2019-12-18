---
title: Azure Data Factory を使用して Machine Learning モデルを更新する
description: Azure Data Factory と Azure Machine Learning を使用して予測パイプラインを作成する方法について説明します。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: afc79badd19fa180e631f1f8fa9735567a0b1e33
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978715"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>更新リソース アクティビティを使って Azure Machine Learning モデルを更新する

> [!div class="op_single_selector" title1="変換アクティビティ"]
> * [Hive アクティビティ](data-factory-hive-activity.md) 
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Machine Learning バッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)


> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory での機械学習モデルの更新](../update-machine-learning-models.md)に関するページをご覧ください。

この記事では、Azure Data Factory と Azure Machine Learning の統合に関するメインの記事「[Azure Machine Learning と Azure Data Factory を使って予測パイプラインを作成する](data-factory-azure-ml-batch-execution-activity.md)」を補足します。 メインの記事をまだ呼んでいない場合は、この記事を読む前にお読みください。 

## <a name="overview"></a>概要
時間の経過と共に、Azure ML スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 再トレーニングが完了したら、再トレーニング済みの ML モデルでスコア付け Web サービスを更新する必要があります。 Web サービスを使用して Azure ML モデルの再トレーニングと更新を有効にするための標準的な手順を次に示します。

1. [Azure Machine Learning Studio (クラシック)](https://studio.azureml.net) で実験を作成します。
2. モデルが準備できたら、Azure Machine Learning Studio (クラシック) を使用して、**トレーニング実験**とスコア付け/**予測実験**の両方の Web サービスを発行します。

次の表で、この例で使用する Web サービスについて説明します。  詳細については、「 [プログラムによる Machine Learning のモデルの再トレーニング](../../machine-learning/machine-learning-retrain-models-programmatically.md) 」を参照してください。

- **トレーニング Web サービス** - トレーニング データを受信し、トレーニング済みのモデルを作成します。 再トレーニングの出力は、Azure Blob Storage 内の .ilearner ファイルになります。 **既定のエンドポイント** が、トレーニング実験を Web サービスとして発行するときに自動的に作成されます。 エンドポイントは複数作成することができますが、この例では、既定のエンドポイントのみを使用します。
- **スコア付け Web サービス** - ラベルの付いていないデータの例を受信し、予測を作成します。 予測の出力は、実験の構成に応じてさまざまな形式 (.csv ファイル、Azure SQL データベースの行など) をとります。 既定のエンドポイントが、予測実験を Web サービスとして発行するときに自動的に作成されます。 

次の図は、Azure ML でのトレーニングとスコア付けのエンドポイントの関係を示しています。

![[Web サービス]](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**training web service** を使用して、2 つ目の **トレーニング Web サービス**。 トレーニング Web サービスの呼び出す方法は、データのスコア付け用 Azure ML Web サービス (スコア付け Web サービス) を呼び出す場合と同じです。 前のセクションで、Azure Data Factory パイプラインから Azure ML Web サービスを呼び出す方法について詳しく説明しています。 

**scoring web service** を使用して、2 つ目の **Azure ML 更新リソース アクティビティ** を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。 次の例では、リンクされているサービスの定義を示します。 

## <a name="scoring-web-service-is-a-classic-web-service"></a>スコア付け Web サービスが従来の Web サービスである
スコア付け Web サービスが**従来の Web サービス**の場合は、Azure Portal を使用して、2 つ目の**更新可能な既定以外のエンドポイント**を作成します。 手順については、「[エンドポイントを作成する](../../machine-learning/machine-learning-create-endpoint.md)」を参照してください。 更新可能な既定以外のエンドポイントを作成したら、次の手順を行います。

* **[バッチ実行]** をクリックして、**mlEndpoint** JSON プロパティの URI の値を取得します。
* **[リソースの更新]** リンクをクリックして、**updateResourceEndpoint** JSON プロパティの URI の値を取得します。 API キーは、エンドポイントのページにあります (右下隅)。

![updatable endpoint](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

次の例は、Azure ML のリンクされたサービスに対する JSON 定義のサンプルを示しています。 リンクされたサービスでは、認証に apiKey が使用されます。  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>スコア付け Web サービスが Azure Resource Manager Web サービスである 
Web サービスが、Azure Resource Manager エンドポイントを公開する新しい種類の Web サービスである場合は、2 番目の**既定以外**のエンドポイントを追加する必要はありません。 リンクされたサービスの **updateResourceEndpoint** の形式は次のとおりです。 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

[Azure Machine Learning Web サービスのポータル](https://services.azureml.net/)で Web サービスにクエリを実行するときに、URL 内のプレースホルダーの値を取得できます。 新しい種類の更新リソース エンドポイントには、AAD (Azure Active Directory) トークンが必要です。 Azure Machine Learning のリンクされたサービスで **servicePrincipalId** と **servicePrincipalKey** を指定します。 [サービス プリンシパルを作成し、Azure リソースを管理するためのアクセス許可を割り当てる方法](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページをご覧ください。 AzureML のリンクされたサービス定義のサンプルを次に示します。 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

次のシナリオで詳細を説明します。 このシナリオでは、Azure Data Factory パイプラインから Azure ML モデルの再トレーニングと更新を行う例を示します。

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>シナリオ: Azure ML モデルの再トレーニングと更新
このセクションでは、 **Azure ML バッチ実行アクティビティ** を使用してモデルの再トレーニングを行うサンプル パイプラインを示します。 このパイプラインでは、 **Azure ML 更新リソース アクティビティ** を使用したスコア付け Web サービスのモデルの更新も行います。 このセクションでは、すべてのリンクされたサービス、データ セット、およびパイプラインの JSON スニペットも提供されます。

サンプル パイプラインのダイアグラム ビューを次に示します。 ご覧のように、Azure ML バッチ実行アクティビティはトレーニングの入力を受け取り、トレーニングの出力 (iLearner ファイル) を作成します。 Azure ML 更新リソース アクティビティはトレーニングの出力を受け取り、スコア付け Web サービスのエンドポイントでモデルを更新します。 更新リソース アクティビティは出力を作成しません。 placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure BLOB ストレージのリンクされたサービス:
Azure Storage には次のデータが格納されています。

* トレーニング データ。 Azure ML トレーニング Web サービス用の入力データです。  
* iLearner ファイル。 Azure ML トレーニング Web サービスの出力です。 このファイルは更新リソース アクティビティへの入力としても使用します。  

リンクされたサービスのサンプルの JSON 定義を次に示します。

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>トレーニングの入力データセット:
次のデータセットは、Azure Machine Learning トレーニング Web サービスの入力トレーニング データを表しています。 Azure Machine Learning バッチ実行アクティビティは、このデータセットを入力として取得します。

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>トレーニングの出力データセット:
次のデータセットは、Azure ML トレーニング Web サービスの出力 iLearner ファイルを示しています。 Azure ML バッチ実行アクティビティがこのデータセットを作成します。 このデータセットは、Azure ML 更新リソース アクティビティへの入力としても使用されます。

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Azure Machine Learning トレーニング エンドポイント用のリンクされたサービス
次の JSON スニペットは、トレーニング Web サービスの既定のエンドポイントを示す Azure Machine Learning のリンクされたサービスを定義します。

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

**Azure Machine Learning Studio (クラシック)** で、次の操作を実行して **mlEndpoint** と **apiKey** の値を取得します。

1. 左側のメニューで **[Web サービス]** をクリックします。
2. Web サービスの一覧で、 **[トレーニング Web サービス]** をクリックします。
3. **[API キー]** ボックスの隣にあるコピー ボタンをクリックします。 クリップボードにコピーされた API キーを Data Factory JSON エディターに貼り付けます。
4. **Azure Machine Learning Studio (クラシック)** で、 **[バッチ実行]** リンクをクリックします。
5. **[要求]** セクションの**要求 URI** をコピーして、Data Factory JSON エディターに貼り付けます。   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure ML の更新可能なスコア付けエンドポイント用のリンクされたサービス:
次の JSON スニペットは、スコア付け Web サービスの更新可能な既定以外のエンドポイントを参照する Azure Machine Learning のリンクされたサービスを定義します。  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>プレースホルダーの出力データセット:
Azure ML 更新リソース アクティビティでは出力は作成されません。 ただし、Azure Data Factory でパイプラインのスケジュールを制御するには出力データセットが必要です。 このため、この例ではダミー/プレースホルダーのデータセットを使用します。  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>パイプライン
パイプラインには、**AzureMLBatchExecution** と **AzureMLUpdateResource** の 2 つのアクティビティが含まれています。 Azure ML バッチ実行アクティビティはトレーニング データを入力として使用し、.iLearner ファイルを出力として作成します。 このアクティビティは、トレーニング Web サービス (Web サービスとして公開されたトレーニング実験) と入力トレーニング データを呼び出し、Web サービスから ilearner ファイルを受け取ります。 placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
