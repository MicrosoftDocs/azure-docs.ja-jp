---
title: "Azure Data Factory を使って予測データ パイプラインを作成する | Microsoft Docs"
description: "Azure Data Factory で Azure Machine Learning バッチ実行アクティビティを使用して、予測パイプラインを作成する方法について説明します。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.openlocfilehash: 413f12d301a0e2c47048d23b2d4fb7de6423256d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Azure Machine Learning と Azure Data Factory を使って予測パイプラインを作成する
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [バージョン 2 - プレビュー](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) では、予測分析ソリューションをビルド、テスト、およびデプロイできます。 大まかに次の 3 つの手順で行われます。

1. **トレーニング実験を作成する**。 この手順を実行するには、Azure ML Studio を使用します。 ML Studio は、トレーニング データを活用した予測分析モデルのトレーニングとテストに使用できる、コラボレーションと視覚化に対応した開発環境です。
2. **トレーニング実験を予測実験に変換する**。 既存のデータでモデルがトレーニングされ、それを使用して新しいデータをスコア付けする準備ができると、スコア付け用に実験を用意し、合理化します。
3. **Web サービスとしてデプロイする**。 Azure Web サービスとしてスコア付け実験を発行できます。 この Web サービスのエンドポイントを使用して、モデルにデータを送信し、モデルの予測を受信できます。  

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Machine Learning バッチ実行アクティビティ](v1/data-factory-azure-ml-batch-execution-activity.md)を参照してください。


### <a name="data-factory-and-machine-learning-together"></a>Data Factory と Machine Learning
Azure Data Factory を使用すると、公開済みの [Azure Machine Learning][azure-machine-learning] Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 Azure Data Factory パイプラインで **バッチ実行アクティビティ** を使用すると、Azure ML Web サービスを呼び出して、データの予測を一括で行うことができます。 

時間の経過と共に、Azure ML スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 次の手順を実行することで、Data Factory パイプラインから Azure ML モデルを再トレーニングできます。

1. 予測実験ではなく、トレーニング実験を Web サービスとして発行します。 前のシナリオで予測実験を Web サービスとして公開したのと同様にこの手順を Azure ML Studio で行います。
2. Azure ML バッチ実行アクティビティを使用して、トレーニング実験用 Web サービスを呼び出します。 基本的には、Azure ML バッチ実行アクティビティを使用して、トレーニング Web サービスとスコア付け Web サービスの両方を呼び出すことができます。

再トレーニングを実行したら、**Azure ML 更新リソース アクティビティ**を使用して、スコア付け Web サービス (Web サービスとして公開した予測実験) を、新しくトレーニングを行ったモデルで更新します。 詳しくは、「[更新リソース アクティビティを使用してモデルを更新する](update-machine-learning-models.md)」をご覧ください。

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning のリンクされたサービス

**Azure Machine Learning** のリンクされたサービスを作成して、Azure Machine Learning Web サービスを Azure Data Factory にリンクします。 このリンクされたサービスは、Azure Machine Learning のバッチ実行アクティビティと[更新リソース アクティビティ](update-machine-learning-models.md)で使用されます。 


```JSON
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

JSON 定義のプロパティについては、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 

Azure Machine Learning では、予測実験用にクラシック Web サービスと新しい Web サービスの両方をサポートします。 Data Factory から使用する適切なサービスを選択できます。 Azure Machine Learning のリンクされたサービスを作成するために必要な情報を取得するには、https://services.azureml.net に移動します。ここには、(新しい) Web サービスとクラシック Web サービスがすべて表示されます。 アクセスする Web サービスをクリックし、**[使用]** ページをクリックします。 **apiKey** プロパティの**主キー**と、**mlEndpoint** プロパティの**バッチ要求**をコピーします。 

![Azure Machine Learning Web サービス](./media/transform-data-using-machine-learning/web-services.png)

##<a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning バッチ実行アクティビティ

次の JSON スニペットでは、Azure Machine Learning バッチ実行アクティビティを定義しています。 このアクティビティ定義には、先ほど作成した Azure Machine Learning のリンクされたサービスへの参照が含まれています。 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"path1"
            }, 
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"path2"
            }        
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path3"
            }, 
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path4"
            }         
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```



| プロパティ          | 説明                              | 必須 |
| :---------------- | :--------------------------------------- | :------- |
| name              | パイプラインのアクティビティの名前     | あり      |
| 説明       | アクティビティの動作を説明するテキスト。  | いいえ       |
| type              | Data Lake Analytics U-SQL アクティビティの場合、アクティビティの種類は **AzureMLBatchExecution** です。 | あり      |
| 既定のコンテナー | Azure Machine Learning のリンクされたサービスにリンクされたサービス。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | あり      |
| webServiceInputs  | Azure Machine Learning Web サービスの入力名をマップするキーと値のペア。 キーは、公開済みの Azure Machine Learning Web サービスで定義されている入力パラメーターと一致する必要があります。 値は、Azure Storage のリンクされたサービスと、入力 BLOB の場所を指定する FilePath プロパティのペアです。 | いいえ       |
| webServiceOutputs | Azure Machine Learning Web サービスの出力名をマップするキーと値のペア。 キーは、公開済みの Azure Machine Learning Web サービスで定義されている出力パラメーターと一致する必要があります。 値は、Azure Storage のリンクされたサービスと、出力 BLOB の場所を指定する FilePath プロパティのペアです。 | いいえ       |
| globalParameters  | Azure ML バッチ実行サービス エンドポイントに渡されるキーと値のペア。 キーは、公開済みの Azure ML Web サービスで定義されている、Web サービスのパラメーターの名前と一致する必要があります。 値は、Azure ML バッチ実行要求の GlobalParameters プロパティに渡されます。 | いいえ       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>シナリオ1: Azure Blob Storage のデータを参照する Web サービスの入力/出力を使用する

このシナリオの Azure Machine Learning Web サービスは、Azure BLOB ストレージ内のファイルのデータを使用して予測を作成し、BLOB ストレージに予測結果を保存します。 次の JSON では、AzureMLBatchExecution アクティビティを使用する Data Factory パイプラインが定義されています。 Azure Blog Storage の入出力データは、LinkedName と FilePath のペアを使用して参照されます。 サンプルのリンクされたサービスが入力と出力で異なる場合は、Data Factory が適切なファイルを選択して Azure ML Web サービスに送信できるように、入力と出力にそれぞれ異なるリンクされたサービスをご利用いただけます。 

> [!IMPORTANT]
> Azure ML の実験では、Web サービスの入力ポートおよび出力ポートとグローバル パラメーターには既定の名前 ("input1"、"input2") がありますが、これらはカスタマイズすることができます。 webServiceInputs、webServiceOutputs、および globalParameters の設定に使用する名前は、実験での名前と厳密に一致する必要があります。 バッチ実行のヘルプ ページでサンプルの要求のペイロードを表示して、Azure ML エンドポイントで必要なマッピングを確認することができます。
>
> 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"amltest/input/in1.csv"
            }, 
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"amltest/input/in2.csv"
            }        
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out1.csv"
            }, 
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out2.csv"
            }         
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>シナリオ2: リーダー/ライター モジュールを使用してさまざまなストレージのデータを参照する
Azure ML の実験を作成するときのもう 1 つの一般的なシナリオは、データ インポート モジュールとデータ出力モジュールを使用することです。 データ インポート モジュールは実験にデータを読み込むために使用し、データ出力モジュールは実験からデータを保存するために使用します。 データ インポート モジュールとデータ出力モジュールの詳細については、MSDN ライブラリの[データのインポート](https://msdn.microsoft.com/library/azure/dn905997.aspx)に関するトピックおよび[データの出力](https://msdn.microsoft.com/library/azure/dn905984.aspx)に関するトピックをご覧ください。     

データ インポート モジュールとデータ出力モジュールを使用するときは、これらのモジュールの各プロパティに Web サービスのパラメーターを使用することをお勧めします。 これらの Web パラメーターを使用すると、実行時に値を構成できます。 たとえば、Azure SQL Database: XXX.database.windows.net を使用するデータ インポート モジュールで実験を作成できます。 Web サービスをデプロイしたら、Web サービスのコンシューマーが `YYY.database.windows.net` という別の Azure SQL Server を指定できるようにします。 Web サービスのパラメーターを使用して、この値を構成できます。

> [!NOTE]
> Web サービスの入力と出力は、Web サービスのパラメーターとは異なるものです。 最初のシナリオでは、Azure ML Web サービスに対して入力と出力を指定する方法を説明しました。 このシナリオでは、データ インポート/データ出力モジュールのプロパティに対応する Web サービスのパラメーターを渡します。
>
> 

Web サービス パラメーターを使用するシナリオを見てみましょう。 Azure Machine Learning でサポートされるいずれかのデータ ソース (例: Azure SQL Database) のデータをリーダー モジュールで読み取る Azure Machine Learning Web サービスをデプロイしました。 バッチ実行が実行された後、ライター モジュールを使用して結果が書き込まれます (Azure SQL Database)。  Web サービスの入力と出力は実験では定義されていません。 この場合は、リーダー/ライター モジュールに関連する Web サービス パラメーターを設定することをお勧めします。 この設定により、AzureMLBatchExecution アクティビティを使用するときにリーダー/ライター モジュールを構成できます。 Web サービスのパラメーターは、次に示すように、アクティビティの JSON の **globalParameters** セクションで指定します。

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```


> [!NOTE]
> Web サービス パラメーターでは大文字と小文字が区別されるため、アクティビティ JSON に指定した名前が Web サービスによって公開されている名前と一致していることを確認してください。
>

再トレーニングを実行したら、**Azure ML 更新リソース アクティビティ**を使用して、スコア付け Web サービス (Web サービスとして公開した予測実験) を、新しくトレーニングを行ったモデルで更新します。 詳しくは、「[更新リソース アクティビティを使用してモデルを更新する](update-machine-learning-models.md)」をご覧ください。



## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
