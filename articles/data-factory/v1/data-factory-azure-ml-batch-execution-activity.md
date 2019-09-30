---
title: Azure Data Factory を使って予測データ パイプラインを作成する | Microsoft Docs
description: Azure Data Factory と Azure Machine Learning を使用して予測パイプラインを作成する方法について説明します
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
ms.openlocfilehash: e7c48c1d91ae08be29531f4a99ea75ab7a928f34
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140480"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Azure Machine Learning と Azure Data Factory を使って予測パイプラインを作成する

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

## <a name="introduction"></a>はじめに
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory での Machine Learning を使用したデータ変換](../transform-data-using-machine-learning.md)に関するページを参照してください。


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) では、予測分析ソリューションをビルド、テスト、およびデプロイできます。 大まかに次の 3 つの手順で行われます。

1. **トレーニング実験を作成する**。 この手順を実行するには、Azure Machine Learning Studio を使用します。 Azure Machine Learning Studio は、トレーニング データを活用した予測分析モデルのトレーニングとテストに使用できる、コラボレーションと視覚化に対応した開発環境です。
2. **トレーニング実験を予測実験に変換する**。 既存のデータでモデルがトレーニングされ、それを使用して新しいデータをスコア付けする準備ができると、スコア付け用に実験を用意し、合理化します。
3. **Web サービスとしてデプロイする**。 Azure Web サービスとしてスコア付け実験を発行できます。 この Web サービスのエンドポイントを使用して、モデルにデータを送信し、モデルの予測を受信できます。

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory は、データの**移動**や**変換**を調整し自動化するクラウドベースのデータ統合サービスです。 さまざまなデータ ストアからデータを取り込み、データを変換/処理して結果データをデータ ストアに発行できる Azure Data Factory を使うことで、データ統合ソリューションを作成できます。

Data Factory サービスでは、データを移動して変換するデータ パイプラインを作成し、指定したスケジュール (毎時、毎日、毎週など) でパイプラインを実行できます。 また、このサービスには視覚化機能が豊富に用意されています。このため、データ パイプライン間の系列と依存関係を表示できるほか、統一された 1 つのビューからすべてのデータ パイプラインを監視して、容易に問題を特定し、監視アラートを設定できます。

Azure Data Factory サービスについては、[Azure Data Factory の概要](data-factory-introduction.md)と [Azure Data Factory を使用した初めてのパイプラインの作成](data-factory-build-your-first-pipeline.md)に関するページを参照してください。

### <a name="data-factory-and-machine-learning-together"></a>Data Factory と Machine Learning
Azure Data Factory を使用すると、公開された [Azure Machine Learning][azure-machine-learning] Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 Azure Data Factory パイプラインで**バッチ実行アクティビティ**を使用すると、Azure Machine Learning Studio Web サービスを呼び出して、データの予測をバッチで行うことができます。 詳細については、「バッチ実行アクティビティを使用して、Azure Machine Learning Studio Web サービスを呼び出す」を参照してください。

時間の経過と共に、Azure Machine Learning Studio スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 次の手順を実行することで、Data Factory パイプラインから Azure Machine Learning Studio モデルを再トレーニングできます。

1. 予測実験ではなく、トレーニング実験を Web サービスとして発行します。 前のシナリオで予測実験を Web サービスとして公開したのと同様にこの手順を Azure Machine Learning Studio で行います。
2. Azure Machine Learning Studio バッチ実行アクティビティを使用して、トレーニング実験用 Web サービスを呼び出します。 基本的には、Azure Machine Learning Studio バッチ実行アクティビティを使用して、トレーニング Web サービスとスコア付け Web サービスの両方を呼び出すことができます。

再トレーニングを実行したら、**Azure Machine Learning Studio 更新リソース アクティビティ**を使用して、スコア付け Web サービス (Web サービスとして公開した予測実験) を、新しくトレーニングを行ったモデルで更新します。 詳しくは、「[更新リソース アクティビティを使用してモデルを更新する](data-factory-azure-ml-update-resource-activity.md)」をご覧ください。

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>バッチ実行アクティビティを使用して Web サービスを呼び出す
Azure Data Factory を使用してデータの移動と処理を調整した後、Azure Machine Learning を使用してバッチを実行します。 大まかな手順を以下に示します。

1. Azure Machine Learning のリンクされたサービスを作成します。 以下の値が必要になります。

   1. **要求 URI** 。 要求 URI は、Web サービス ページで **[Batch 実行]** リンクをクリックするとわかります。
   2. **API キー** 。 API キーは、発行した Web サービスをクリックするとわかります。
   3. **AzureMLBatchExecution** アクティビティを使用します。

      ![Machine Learning Dashboard](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>シナリオ: Azure Blob Storage のデータを参照する Web サービスの入力/出力の使用を実験する
このシナリオの Azure Machine Learning Web サービスは、Azure BLOB ストレージ内のファイルのデータを使用して予測を作成し、BLOB ストレージに予測結果を保存します。 次の JSON では、AzureMLBatchExecution アクティビティを使用する Data Factory パイプラインが定義されています。 このアクティビティは、入力としてデータセット **DecisionTreeInputBlob** を使用し、出力として **DecisionTreeResultBlob** を使用します。 **DecisionTreeInputBlob** は、**webServiceInput** JSON プロパティを使用して Web サービスの入力として渡します。 **DecisionTreeResultBlob** は、**webServiceOutputs** JSON プロパティを使用して Web サービスの出力として渡します。

> [!IMPORTANT]
> Web サービスで複数の入力を受け取る場合は、**webServiceInput** プロパティを使用せずに、**webServiceInputs** プロパティを使用します。 webServiceInputs プロパティの使用例については、「 [Web サービスで複数の入力が必要である](#web-service-requires-multiple-inputs) 」のセクションを参照してください。
>
> **webServiceInput**/**webServiceInputs** と **webServiceOutputs** の各プロパティ (**typeProperties** 内) から参照されているデータセットもアクティビティの **inputs** と **outputs** に含める必要があります。
>
> Azure Machine Learning Studio の実験では、Web サービスの入力ポートおよび出力ポートとグローバル パラメーターには既定の名前 ("input1"、"input2") がありますが、これらはカスタマイズすることができます。 webServiceInputs、webServiceOutputs、および globalParameters の設定に使用する名前は、実験での名前と厳密に一致する必要があります。 バッチ実行のヘルプ ページでサンプルの要求のペイロードを表示して、Azure Machine Learning Studio エンドポイントで必要なマッピングを確認することができます。
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> AzureMLBatchExecution アクティビティの入力および出力だけを、パラメーターとして Web サービスに渡すことができます。 たとえば、上の JSON スニペットでは、DecisionTreeInputBlob は AzureMLBatchExecution アクティビティへの入力であり、webServiceInput パラメーターを使用して Web サービスに入力として渡されます。
>
>

### <a name="example"></a>例
この例では、Azure Storage を使用して、入力データと出力データの両方を保持します。

この例を実行する前に、[Data Factory を使用して最初のパイプラインを作成する][adf-build-1st-pipeline]チュートリアルを完了することをお勧めします。 この例では、Data Factory エディターを使用して Data Factory アーティファクト (リンクされたサービス、データセット、パイプライン) を作成します。

1. **Azure Storage** 用の**リンクされたサービス**を作成します。 入力ファイルと出力ファイルが異なるストレージ アカウントにある場合は、リンクされたサービスが 2 つ必要です。 JSON の例を次に示します。

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. **入力**用の Azure Data Factory **データセット**を作成します。 他の Data Factory データセットとは異なり、これらのデータセットは **folderPath** 値と **fileName** 値を含む必要があります。 パーティション分割を使用すると、各バッ チ実行 (各データ スライス) で一意の入力ファイルと出力ファイルを処理または生成できます。 入力を CSV ファイル形式に変換し、これを各スライスのストレージ アカウントに配置するため、いくつかのアップストリーム アクティビティを含める場合があります。 その場合は、次の例に示す **external** および **externalData** の設定を含めないと、DecisionTreeInputBlob は別のアクティビティの出力データセットになります。

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
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

    入力 csv ファイルには列ヘッダー行がある必要があります。 **コピー アクティビティ**を使用している csv を BLOB ストレージに作成または移動する場合、シンクの **blobWriterAddHeader** プロパティを **true** に設定する必要があります。 例:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    csv ファイルにヘッダー行がない場合、次のエラーが表示される場合があります:**アクティビティ エラー:文字列の読み取りエラー。予期しないトークン:StartObject。パス ''、行 1、位置 1**。
3. **出力**用の Azure Data Factory **データセット**を作成します。 この例では、パーティション分割を使用して各スライスの実行ごとに一意の出力パスを作成します。 パーティションがない場合、アクティビティはファイルを上書きします。

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. 次の型の**リンクされたサービス**を作成します:**AzureMLLinkedService**。API キーとモデルのバッチ実行 URL を入力します。

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. 最後に、 **AzureMLBatchExecution** アクティビティを含むパイプラインを作成します。 実行時には、パイプラインは次の手順を実行します。

   1. 入力データセットから入力ファイルの場所を取得する。
   2. Azure Machine Learning のバッチ実行 API を呼び出す。
   3. バッチの実行出力を出力データセットで指定された BLOB にコピーする。

      > [!NOTE]
      > AzureMLBatchExecution アクティビティは、0 個以上の入力と 1 個以上の出力を使用できます。
      >
      >

      ```JSON
      {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      **start** と [end](https://en.wikipedia.org/wiki/ISO_8601) の日時は、いずれも **ISO 形式**である必要があります。 例: 2014-10-14T16:32:41Z。 **start + 48 時間** " になります。 **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。 JSON のプロパティの詳細については、 [JSON スクリプティング リファレンス](https://msdn.microsoft.com/library/dn835050.aspx) を参照してください。

      > [!NOTE]
      > AzureMLBatchExecution アクティビティへの入力の指定は省略可能です。
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>シナリオ: リーダー/ライター モジュールを使用したさまざまなストレージのデータの参照を実験する
Azure Machine Learning Studio を実験するときのもう 1 つの一般的なシナリオは、リーダー モジュールとライター モジュールを使用することです。 リーダー モジュールは実験にデータを読み込むために使用し、ライター モジュールは実験からデータを保存するために使用します。 リーダーとライター モジュールの詳細については、MSDN ライブラリの[リーダー](https://msdn.microsoft.com/library/azure/dn905997.aspx)と[ライター](https://msdn.microsoft.com/library/azure/dn905984.aspx)に関するページを参照してください。

リーダー/ライター モジュールの各プロパティには Web サービスのパラメーターを使用するのがよい方法です。 これらの Web パラメーターを使用すると、実行時に値を構成できます。 たとえば、次の Azure SQL データベースを使用するリーダー モジュールで実験を作成できます:XXX.database.windows.net。 Web サービスをデプロイした後、Web サービスのコンシューマーを有効にして、YYY.database.windows.net という名前の別の Azure SQL Server を指定できます。 Web サービスのパラメーターを使用して、この値を構成できます。

> [!NOTE]
> Web サービスの入力と出力は、Web サービスのパラメーターとは異なるものです。 最初のシナリオでは、Azure Machine Learning Studio Web サービスに対して入力と出力を指定する方法を説明しました。 このシナリオでは、リーダー/ライター モジュールのプロパティに対応するパラメーターを Web サービスに渡します。
>
>

Web サービス パラメーターを使用するシナリオを見てみましょう。 Azure Machine Learning でサポートされるいずれかのデータ ソース (例: Azure SQL Database) のデータをリーダー モジュールで読み取る Azure Machine Learning Web サービスをデプロイしました。 バッチ実行が実行された後、ライター モジュールを使用して結果が書き込まれます (Azure SQL Database)。  Web サービスの入力と出力は実験では定義されていません。 この場合は、リーダー/ライター モジュールに関連する Web サービス パラメーターを設定することをお勧めします。 この設定により、AzureMLBatchExecution アクティビティを使用するときにリーダー/ライター モジュールを構成できます。 Web サービスのパラメーターは、次に示すように、アクティビティの JSON の **globalParameters** セクションで指定します。

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

また、次の例に示すように、 [Data Factory 関数](data-factory-functions-variables.md) を使用して、Web サービス パラメーターの値を渡すこともできます。

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web サービス パラメーターでは大文字と小文字が区別されるため、アクティビティ JSON に指定した名前が Web サービスによって公開されている名前と一致していることを確認してください。
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>リーダー モジュールを使用して Azure BLOB の複数のファイルからデータを読み取る
ビッグ データ パイプラインにアクティビティ (Pig、Hive など) を設定すると、拡張子が付いていない出力ファイルを 1 つ以上生成できます。 たとえば、外部 Hive テーブルを指定するとき、外部 Hive テーブルのデータを 000000_0 という名前で Azure BLOB ストレージに格納できます。 実験では、リーダー モジュールを使用して複数のファイルを読み取り、予測に使用できます。

Azure Machine Learning の実験でリーダー モジュールを使用する場合は、入力として Azure BLOB を指定できます。 Azure Blob Storage 内のファイルは、HDInsight 上で実行する Pig および Hive スクリプトによって生成される出力ファイル (例:000000_0) でもかまいません。 リーダー モジュールでは、 **[Path to container, directory/blob (コンテナーへのパス、ディレクトリ/BLOB)]** を構成して (拡張子がない) ファイルを読み取ることができます。 **コンテナーへのパス**でコンテナーをポイントし、**ディレクトリ/BLOB** では、次の画像のようにファイルが含まれるフォルダーをポイントします。 アスタリスク (\*) **は、実験の一環としてコンテナー/フォルダー内のすべてのファイル (つまり、data/aggregateddata/year=2014/month-6/\*)** を読み取るように指定します。

![Azure BLOB のプロパティ](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>例
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Web サービス パラメーターを使用した AzureMLBatchExecution のパイプライン

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning studio model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

上の JSON の例に関する説明:

* デプロイされた Azure Machine Learning Web サービスは、リーダー モジュールとライター モジュールを使用して、Azure SQL Database のデータを読み書きします。 この Web サービスでは、Database server name、Database name、Server user account name、Server user account password という 4 つのパラメーターが公開されています。
* **start** と [end](https://en.wikipedia.org/wiki/ISO_8601) の日時は、いずれも **ISO 形式**である必要があります。 例: 2014-10-14T16:32:41Z。 **start + 48 時間** " になります。 **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。 JSON のプロパティの詳細については、 [JSON スクリプティング リファレンス](https://msdn.microsoft.com/library/dn835050.aspx) を参照してください。

### <a name="other-scenarios"></a>その他のシナリオ
#### <a name="web-service-requires-multiple-inputs"></a>Web サービスで複数の入力が必要である
Web サービスで複数の入力を受け取る場合は、**webServiceInput** プロパティを使用せずに、**webServiceInputs** プロパティを使用します。 **webServiceInputs** から参照されているデータセットもアクティビティの **inputs** に含める必要があります。

Azure Machine Learning Studio の実験では、Web サービスの入力ポートおよび出力ポートとグローバル パラメーターには既定の名前 ("input1"、"input2") がありますが、これらはカスタマイズすることができます。 webServiceInputs、webServiceOutputs、および globalParameters の設定に使用する名前は、実験での名前と厳密に一致する必要があります。 バッチ実行のヘルプ ページでサンプルの要求のペイロードを表示して、Azure Machine Learning Studio エンドポイントで必要なマッピングを確認することができます。

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Web サービスに入力が必要ではない
Azure Machine Learning Studio バッチ実行 Web サービスを使用すると、R や Python のスクリプトなど、入力が必要ではない任意のワークフローを実行できます。 また、GlobalParameters を公開しない Reader モジュールを使用して構成する方法を実験することもできます。 この場合、AzureMLBatchExecution アクティビティは次のように構成されます。

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Web サービスに入力/出力が必要ではない
Azure Machine Learning Studio バッチ実行 Web サービスに Web サービスの出力を構成しない場合があります。 この例では、Web サービスの入力も出力ありません。また、GlobalParameters も構成されていません。 アクティビティ自体に構成されている出力があっても、webServiceOutput として出力されません。

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web サービスはリーダーとライターを使用し、他のアクティビティが成功した場合にのみアクティビティを実行する
Azure Machine Learning Studio Web サービスのリーダーとライター モジュールは、GlobalParameters あり、またはなしで構成できます。 ただし、いくつかのアップストリーム処理が完了した場合のみデータセットの依存関係を使用してサービスを呼び出すパイプラインに、サービス呼び出しを埋め込むことができます。 また、この方法を使用して、バッチ実行が完了した後に別のアクションをトリガーすることもできます。 この場合、Web サービスの入力または出力として指定せずに、アクティビティの入力と出力を使用する依存関係を表現することができます。

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

**ここまでのポイント** は次のとおりです。

* 実験用エンドポイントが webServiceInput を使用している場合、そのエンドポイントは BLOB データセットで表され、アクティビティの入力と webServiceInput プロパティに含まれます。 使用していない場合、webServiceInput プロパティは省略されます。
* 実験用エンドポイントが webServiceOutput(s) を使用している場合、そのエンドポイントは BLOB データセットで表され、アクティビティの出力と webServiceOutputs プロパティに含まれます。 アクティビティの出力と webServiceOutputs は、実験の各出力名でマッピングされます。 使用していない場合、webServiceOutputs プロパティは省略されます。
* 実験用エンドポイントが globalParameter を公開している場合、アクティビティの globalParameters プロパティでキーと値のペアとして指定されます。 公開していない場合、globalParameters プロパティは省略されます。 キーは大文字と小文字が区別されます。 [Azure Data Factory 関数](data-factory-functions-variables.md) を値に使用することができます。
* アクティビティの入力と出力のプロパティには、アクティビティの typeProperties から参照せずに含めることができるその他のデータセットがあります。 このようなデータセットは実行の制御にスライスの依存関係を使用しますが、それ以外の場合は、AzureMLBatchExecution アクティビティから無視されます。


## <a name="updating-models-using-update-resource-activity"></a>更新リソース アクティビティを使用してモデルを更新する
再トレーニングを実行したら、**Azure Machine Learning Studio 更新リソース アクティビティ**を使用して、スコア付け Web サービス (Web サービスとして公開した予測実験) を、新しくトレーニングを行ったモデルで更新します。 詳しくは、「[更新リソース アクティビティを使用してモデルを更新する](data-factory-azure-ml-update-resource-activity.md)」をご覧ください。

### <a name="reader-and-writer-modules"></a>リーダーとライター モジュール
Web サービス パラメーターの使用を伴う一般的なシナリオとして、Azure SQL のリーダーとライターを使用するシナリオがあります。 リーダー モジュールは、Azure Machine Learning Studio 外部のデータ管理サービスからデータを実験にロードするために使用します。 ライター モジュールは、実験のデータを Azure Machine Learning Studio 外部のデータ管理サービスに保存するために使用します。

Azure BLOB と Azure SQL のリーダー/ライターの詳細については、MSDN ライブラリの[リーダー](https://msdn.microsoft.com/library/azure/dn905997.aspx)と[ライター](https://msdn.microsoft.com/library/azure/dn905984.aspx)に関するページを参照してください。 前のセクションの例では、Azure BLOB リーダーと Azure BLOB ライターを使用しています。 ここでは、Azure SQL リーダーと Azure SQL ライターの使用について説明します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
**質問:** ビッグ データ パイプラインによって生成される複数のファイルがあります。 AzureMLBatchExecution アクティビティを使用して、すべてのファイルで処理できますか。

**A:** はい。 詳しくは、「 **リーダー モジュールを使用して Azure BLOB の複数のファイルからデータを読み取る** 」をご覧ください。

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Azure Machine Learning Studio バッチ スコアリング アクティビティ
**AzureMLBatchScoring** アクティビティを使用して Azure Machine Learning と統合している場合は、最新の **AzureMLBatchExecution** アクティビティを使用することをお勧めします。

AzureMLBatchExecution アクティビティは、Azure SDK および Azure PowerShell の 2015 年 8 月のリリースで導入されました。

AzureMLBatchScoring アクティビティを引き続き使用する場合は、このセクションを読んでください。

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>入力/出力に Azure Storage を使用する Azure Machine Learning Studio バッチ スコアリング アクティビティ

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Web サービス パラメーター
Web サービス パラメーターの値を指定するには、パイプライン JSON の **AzureMLBatchScoringActivty** セクションに **typeProperties** セクションを追加します。次に例を示します。

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
また、次の例に示すように、 [Data Factory 関数](data-factory-functions-variables.md) を使用して、Web サービス パラメーターの値を渡すこともできます。

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web サービス パラメーターでは大文字と小文字が区別されるため、アクティビティ JSON に指定した名前が Web サービスによって公開されている名前と一致していることを確認してください。
>
>

## <a name="see-also"></a>関連項目
* [Azure ブログの投稿:Azure Data Factory と Azure Machine Learning の概要](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
