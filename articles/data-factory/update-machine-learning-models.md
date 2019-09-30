---
title: Azure Data Factory を使って機械学習モデルを更新する | Microsoft Docs
description: Azure Data Factory と機械学習を使用して予測パイプラインを作成する方法について説明します
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 56d0ce6668c1077b99c980c2bc5b16998a3a41c1
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140541"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>更新リソース アクティビティを使って Azure Machine Learning モデルを更新する
この記事では、Azure Data Factory と Azure Machine Learning の統合に関するメインの記事「[Azure Machine Learning と Azure Data Factory を使って予測パイプラインを作成する](transform-data-using-machine-learning.md)」を補足します。 メインの記事をまだ呼んでいない場合は、この記事を読む前にお読みください。

## <a name="overview"></a>概要
Azure Machine Learning モデルの運用化プロセスの一環として、モデルのトレーニングと保存が行われます。 その後、このモデルを使用して、予測 Web サービスを作成します。 これによって、Web サイト、ダッシュボード、モバイル アプリでこの Web サービスを使用できます。

Machine Learning を使って作成するモデルは、通常、静的ではありません。 新しいデータが使用可能になるか、API のコンシューマーに独自のデータがある場合は、モデルを再トレーニングする必要があります。 Azure Machine Learning でモデルを再トレーニングする方法の詳細については、「[Machine Learning モデルの再トレーニング](../machine-learning/machine-learning-retrain-machine-learning-model.md)」を参照してください。

再トレーニングは頻繁に発生する可能性があります。 バッチ実行アクティビティと更新リソース アクティビティを使用すると、Data Factory を使って予測 Web サービスの再トレーニングと更新を行う Azure Machine Learning モデルを運用可能にできます。

次の図は、トレーニングと予測 Web サービスの関係を示しています。

![[Web サービス]](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning 更新リソース アクティビティ

次の JSON スニペットは、Azure Machine Learning バッチ実行アクティビティを定義しています。

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| プロパティ                      | 説明                              | 必須 |
| :---------------------------- | :--------------------------------------- | :------- |
| 名前                          | パイプラインのアクティビティの名前。     | はい      |
| description                   | アクティビティの動作を説明するテキスト。  | いいえ       |
| type                          | Azure Machine Learning 更新リソース アクティビティの場合、アクティビティの種類は **AzureMLUpdateResource** です。 | はい      |
| linkedServiceName             | updateResourceEndpoint プロパティが含まれる Azure Machine Learning のリンクされたサービス。 | はい      |
| trainedModelName              | Web サービスの実験で更新されるようにトレーニング済みのモデル モジュールの名前 | はい      |
| trainedModelLinkedServiceName | 更新操作によってアップロードされる iLearner ファイルを保持する Azure Storage のリンクされたサービスの名前 | はい      |
| trainedModelFilePath          | 更新操作によってアップロードされる iLearner ファイルを表す trainedModelLinkedService 内の相対ファイル パス | はい      |

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー

モデルの再トレーニングと予測 Web サービスの更新を運用可能にするプロセス全体では、次の手順を実行する必要があります。

- **バッチ実行アクティビティ**を使用して、**トレーニング Web サービス**を呼び出します。 トレーニング Web サービスの呼び出しは、[Azure Machine Learning および Data Factory のバッチ実行のアクティビティを使用した予測パイプラインの作成](transform-data-using-machine-learning.md)に関するページで説明されている予測 Web サービスの呼び出しと同じです。 トレーニング Web サービスの出力は、予測 Web サービスの更新に使用できる iLearner ファイルです。
- **更新リソース アクティビティ**を使用して**予測 Web サービス**の**更新リソース エンドポイント**を呼び出し、新しくトレーニングを行ったモデルでこの Web サービスを更新します。

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning のリンクされたサービス

作業対象となる上記のエンド ツー エンドのワークフローでは、次の 2 つの Azure Machine Learning のリンクされたサービスを作成する必要があります。

1. トレーニング Web サービスへの Azure Machine Learning のリンクされたサービス。このリンクされたサービスは、[Azure Machine Learning および Data Factory のバッチ実行のアクティビティを使用した予測パイプラインの作成](transform-data-using-machine-learning.md)に関するページで説明されているのと同じ方法で、バッチ実行アクティビティによって使用されます。 相違点は、トレーニング Web サービスの出力が、予測 Web サービスを更新する更新リソース アクティビティでその後に使用される iLearner ファイルであることです。
2. 予測 Web サービスの更新リソース エンドポイントへの Azure Machine Learning のリンクされたサービス。 このリンクされたサービスは、上記の手順から返された iLearner ファイルを使用して予測 Web サービスを更新する更新リソース アクティビティで使用されます。

2 番目の Azure Machine Learning のリンクされたサービスの場合、構成は、Azure Machine Learning Web サービスが従来の Web サービスであるか、新しい Web サービスであるかによって異なります。 相違点については、以降のセクションで個別に説明します。

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Web サービスが新しい Azure Resource Manager Web サービスである

Web サービスが、Azure Resource Manager エンドポイントを公開する新しい種類の Web サービスである場合は、2 番目の**既定以外**のエンドポイントを追加する必要はありません。 リンクされたサービスの **updateResourceEndpoint** の形式は次のとおりです。

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

[Azure Machine Learning Web サービスのポータル](https://services.azureml.net/)で Web サービスにクエリを実行するときに、URL 内のプレースホルダーの値を取得できます。

新しい種類の更新リソース エンドポイントでは、サービス プリンシパルの認証が必要です。 サービス プリンシパルの認証を使用するには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録し、Web サービスが属するサブスクリプションまたはリソース グループの**共同作成者**または**所有者**のロールを付与します。 [サービス プリンシパルを作成し、Azure リソースを管理するためのアクセス許可を割り当てる方法](../active-directory/develop/howto-create-service-principal-portal.md)に関するページをご覧ください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

- アプリケーション ID
- アプリケーション キー
- テナント ID

リンクされたサービス定義のサンプルを次に示します。

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

次のシナリオで詳細を説明します。 このシナリオでは、Azure Data Factory パイプラインから Azure Machine Learning Studio モデルの再トレーニングと更新を行う例を示します。


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>サンプル:Azure Machine Learning モデルの再トレーニングと更新

このセクションでは、**Azure Machine Learning Studio バッチ実行アクティビティ**を使用してモデルの再トレーニングを行うサンプル パイプラインを示します。 このパイプラインでは、**Azure Machine Learning Studio 更新リソース アクティビティ**を使用したスコア付け Web サービスのモデルの更新も行います。 このセクションでは、すべてのリンクされたサービス、データ セット、およびパイプラインの JSON スニペットも提供されます。

### <a name="azure-blob-storage-linked-service"></a>Azure BLOB ストレージのリンクされたサービス:
Azure Storage には次のデータが格納されています。

* トレーニング データ。 Azure Machine Learning Studio トレーニング Web サービス用の入力データです。
* iLearner ファイル。 Azure Machine Learning Studio トレーニング Web サービスの出力です。 このファイルは更新リソース アクティビティへの入力としても使用します。

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Azure Machine Learning Studio トレーニング エンドポイント用のリンクされたサービス
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

**Azure Machine Learning Studio** で、次の操作を行い、**mlEndpoint** と **apiKey** の値を取得します。

1. 左側のメニューで **[Web サービス]** をクリックします。
2. Web サービスの一覧で、 **[トレーニング Web サービス]** をクリックします。
3. **[API キー]** ボックスの隣にあるコピー ボタンをクリックします。 クリップボードにコピーされた API キーを Data Factory JSON エディターに貼り付けます。
4. **Azure Machine Learning Studio** で **[バッチ実行]** リンクをクリックします。
5. **[要求]** セクションの**要求 URI** をコピーして、Data Factory JSON エディターに貼り付けます。

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Azure Machine Learning Studio の更新可能なスコア付けエンドポイント用のリンクされたサービス:
次の JSON スニペットは、スコア付け Web サービスの更新可能なエンドポイントを参照する Azure Machine Learning のリンクされたサービスを定義します。

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

### <a name="pipeline"></a>パイプライン
パイプラインには、**AzureMLBatchExecution** と **AzureMLUpdateResource** の 2 つのアクティビティが含まれています。 バッチ実行アクティビティはトレーニング データを入力として使用し、iLearner ファイルを出力として作成します。 更新リソース アクティビティは次に、この iLearner ファイルを取得し、予測 Web サービスの更新に使用します。

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>次の手順
別の手段でデータを変換する方法を説明している次の記事を参照してください。

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
