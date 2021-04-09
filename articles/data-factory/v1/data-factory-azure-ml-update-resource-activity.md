---
title: Azure Data Factory を使用して Machine Learning モデルを更新する
description: Azure Data Factory v1 と Azure Machine Learning スタジオ (クラシック) を使用して予測パイプラインを作成する方法について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 2d9156bfcc11817a647c33053a2d04c653c0a706
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785497"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>更新リソース アクティビティを使用して Azure Machine Learning スタジオ (クラシック) モデルを更新する

> [!div class="op_single_selector" title1="変換アクティビティ"]
> * [Hive アクティビティ](data-factory-hive-activity.md) 
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Azure Machine Learning スタジオ (クラシック) のバッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning スタジオ (クラシック) の更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)


> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory での機械学習モデルの更新](../update-machine-learning-models.md)に関するページをご覧ください。

これは、Azure Data Factory と Azure Machine Learning Studio (classic) の統合に関するメインの記事「[Azure Machine Learning Studio (classic) と Azure Data Factory を使用して予測パイプラインを作成する](data-factory-azure-ml-batch-execution-activity.md)」を補足する記事です。 メインの記事をまだ呼んでいない場合は、この記事を読む前にお読みください。 

## <a name="overview"></a>概要
時間の経過と共に、Azure Machine Learning Studio (クラシック) スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 再トレーニングが完了したら、再トレーニング済みの ML モデルでスコア付け Web サービスを更新する必要があります。 Web サービスを使用してスタジオ (クラシック) モデルの再トレーニングと更新を有効にするための標準的な手順を次に示します。

1. [Azure Machine Learning Studio (クラシック)](https://studio.azureml.net) で実験を作成します。
2. モデルが準備できたら、Azure Machine Learning Studio (クラシック) を使用して、**トレーニング実験** とスコア付け/**予測実験** の両方の Web サービスを発行します。

次の表で、この例で使用する Web サービスについて説明します。  詳細については、[プログラムによる Azure Machine Learning スタジオ (クラシック) モデルの再トレーニング](../../machine-learning/classic/retrain-machine-learning-model.md)に関するページを参照してください。

- **トレーニング Web サービス** - トレーニング データを受信し、トレーニング済みのモデルを作成します。 再トレーニングの出力は、Azure Blob Storage 内の .ilearner ファイルになります。 **既定のエンドポイント** が、トレーニング実験を Web サービスとして発行するときに自動的に作成されます。 エンドポイントは複数作成することができますが、この例では、既定のエンドポイントのみを使用します。
- **スコア付け Web サービス** - ラベルの付いていないデータの例を受信し、予測を作成します。 予測の出力は、実験の構成に応じてさまざまな形式 (.csv ファイル、Azure SQL Database の行など) をとります。 既定のエンドポイントが、予測実験を Web サービスとして発行するときに自動的に作成されます。 

次の図は、Azure Machine Learning スタジオ (クラシック) でのトレーニングとスコア付けのエンドポイントの関係を示したものです。

![[Web サービス]](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**Azure Machine Learning スタジオ (クラシック) バッチ実行アクティビティ** を使用して、**トレーニング Web サービス** を呼び出すことができます。 トレーニング Web サービスの呼び出す方法は、データのスコア付け用 Azure Machine Learning スタジオ (クラシック) Web サービス (スコア付け Web サービス) を呼び出す場合と同じです。 前のセクションで、Azure Data Factory パイプラインから Azure Machine Learning スタジオ (クラシック) Web サービスを呼び出す方法について詳しく説明しています。 

**scoring web service** を使用して、2 つ目の **Azure Machine Learning スタジオ (クラシック) 更新リソース アクティビティ** を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。 次の例では、リンクされているサービスの定義を示します。 

## <a name="scoring-web-service-is-a-classic-web-service"></a>スコア付け Web サービスが従来の Web サービスである
スコア付け Web サービスが **従来の Web サービス** の場合は、Azure Portal を使用して、2 つ目の **更新可能な既定以外のエンドポイント** を作成します。 手順については、「[エンドポイントを作成する](../../machine-learning/classic/create-endpoint.md)」を参照してください。 更新可能な既定以外のエンドポイントを作成したら、次の手順を行います。

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
Web サービスが、Azure Resource Manager エンドポイントを公開する新しい種類の Web サービスである場合は、2 番目の **既定以外** のエンドポイントを追加する必要はありません。 リンクされたサービスの **updateResourceEndpoint** の形式は次のとおりです。 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

[Azure Machine Learning Studio (classic) Web サービスのポータル](https://services.azureml.net/)で Web サービスにクエリを実行するときに、URL 内のプレースホルダーの値を取得できます。 新しい種類の更新リソース エンドポイントには、AAD (Azure Active Directory) トークンが必要です。 スタジオ (クラシック) のリンクされたサービスで **servicePrincipalId** と **servicePrincipalKey** を指定します。 [サービス プリンシパルを作成し、Azure リソースを管理するためのアクセス許可を割り当てる方法](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページをご覧ください。 AzureML のリンクされたサービス定義のサンプルを次に示します。 

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

次のシナリオで詳細を説明します。 このシナリオでは、Azure Data Factory パイプラインからスタジオ (クラシック) モデルの再トレーニングと更新を行う例を示します。

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>シナリオ: スタジオ (クラシック) モデルの再トレーニングと更新
このセクションでは、**Azure Machine Learning Studio (classic) バッチ実行アクティビティ** を使用してモデルの再トレーニングを行うサンプル パイプラインを示します。 このパイプラインでは、**Azure Machine Learning Studio (classic) 更新リソース アクティビティ** を使用して、スコア付け Web サービスのモデルの更新も行います。 このセクションでは、すべてのリンクされたサービス、データ セット、およびパイプラインの JSON スニペットも提供されます。

サンプル パイプラインのダイアグラム ビューを次に示します。 ご覧のように、スタジオ (クラシック) バッチ実行アクティビティを使用して、トレーニングの入力を受け取り、トレーニングの出力 (iLearner ファイル) を作成します。 スタジオ (クラシック) 更新リソース アクティビティを使用して、トレーニングの出力を受け取り、スコア付け Web サービスのエンドポイントでモデルを更新します。 更新リソース アクティビティは出力を作成しません。 placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure BLOB ストレージのリンクされたサービス:
Azure Storage には次のデータが格納されています。

* トレーニング データ。 スタジオ (クラシック) トレーニング Web サービスへの入力データです。  
* iLearner ファイル。 スタジオ (クラシック) トレーニング Web サービスからの出力です。 このファイルは更新リソース アクティビティへの入力としても使用します。  

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
次のデータセットは、スタジオ (クラシック) トレーニング Web サービス用の入力トレーニング データを表しています。 このデータセットは、スタジオ (クラシック) バッチ実行アクティビティによって入力として使用されます。

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
次のデータセットは、Azure Machine Learning スタジオ (クラシック) トレーニング Web サービスの出力 iLearner ファイルを表しています。 このデータセットは、Azure Machine Learning スタジオ (クラシック) バッチ実行アクティビティによって生成されます。 このデータセットは、Azure Machine Learning スタジオ (クラシック) 更新リソース アクティビティへの入力としても使用されます。

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

### <a name="linked-service-for-studio-classic-training-endpoint"></a>スタジオ (クラシック) トレーニング エンドポイント用のリンクされたサービス
次の JSON スニペットを使用すると、トレーニング Web サービスの既定のエンドポイントを示すスタジオ (クラシック) のリンクされたサービスが定義されます。

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
5. **[要求]** セクションの **要求 URI** をコピーして、Data Factory JSON エディターに貼り付けます。   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>スタジオ (クラシック) の更新可能なスコア付けエンドポイント用のリンクされたサービス:
次の JSON スニペットを使用して、スコア付け Web サービスの更新可能な既定以外のエンドポイントを参照するスタジオ (クラシック) のリンクされたサービスを定義します。  

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
スタジオ (クラシック) 更新リソース アクティビティを使用すると、出力は作成されません。 ただし、Azure Data Factory でパイプラインのスケジュールを制御するには出力データセットが必要です。 このため、この例ではダミー/プレースホルダーのデータセットを使用します。  

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
パイプラインには、**AzureMLBatchExecution** と **AzureMLUpdateResource** の 2 つのアクティビティが含まれています。 Azure Machine Learning スタジオ (クラシック) バッチ実行アクティビティによって、トレーニング データが入力として使用され、iLearner ファイルが出力として作成されます。 このアクティビティは、トレーニング Web サービス (Web サービスとして公開されたトレーニング実験) と入力トレーニング データを呼び出し、Web サービスから ilearner ファイルを受け取ります。 placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。

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