---
title: "Azure Data Factory パイプラインでカスタム アクティビティを使用する"
description: "カスタム アクティビティを作成して Azure Data Factory パイプラインで使用する方法について説明します。"
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
ms.date: 08/10/2017
ms.author: shengc
ms.openlocfilehash: 24f15168fd716cf317087b8a2ad19b66574ce569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Azure Data Factory パイプラインでカスタム アクティビティを使用する
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-use-custom-activities.md)
> * [バージョン 2 - プレビュー](transform-data-using-dotnet-custom-activity.md)

Azure Data Factory パイプラインでは、2 種類のアクティビティを使用できます。

- [サポートされるソース データ ストアとシンク データ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の間でデータを移動する[データ移動アクティビティ](copy-activity-overview.md)。
- Azure HDInsight、Azure Batch、Azure Machine Learning などのコンピューティング サービスを使用してデータを変換する[データ変換アクティビティ](transform-data.md)。 

Data Factory でサポートされていないデータ ストアとの間でデータを移動する場合や、Data Factory でサポートされていない方法でデータを変換/処理する場合は、独自のデータ移動ロジックまたはデータ変換ロジックで**カスタム アクティビティ**を作成し、パイプラインでそのアクティビティを使用します。 カスタム アクティビティでは、仮想マシンの **Azure Batch** プールでカスタマイズされたコード ロジックを実行します。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 のカスタム アクティビティ](v1/data-factory-use-custom-activities.md)を参照してください。
 

Azure Batch サービスを初めて利用する場合は、次のトピックをご覧ください。

* [Azure Batch の基本](../batch/batch-technical-overview.md) 」をご覧ください。
* Azure Batch アカウントは、[New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) コマンドレットまたは [Azure Portal](../batch/batch-account-create-portal.md) を使用して作成します。 このコマンドレットの使用方法の詳細については、 [PowerShell を使用した Azure Batch アカウントの管理](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) に関するトピックをご覧ください。
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) コマンドレット。

## <a name="azure-batch-linked-service"></a>Azure Batch のリンクされたサービス 
次の JSON では、サンプルの Azure Batch 回線サービスを定義しています。 詳細については、「[Azure Data Factory でサポートされるコンピューティング環境](compute-linked-services.md)」をご覧ください。

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Azure Batch のリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 

## <a name="custom-activity"></a>カスタム アクティビティ

次の JSON スニペットでは、単純なカスタム アクティビティを使用するパイプラインを定義しています。 このアクティビティ定義には、Azure Batch のリンクされたサービスへの参照が含まれています。 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

このサンプルでは、helloworld.exe は、resourceLinkedService で使用される Azure ストレージ アカウントの customactv2/helloworld フォルダーに格納されているカスタム アプリケーションです。 カスタム アクティビティでは、Azure Batch で実行されるこのカスタム アプリケーションを送信します。 コマンドは、Azure Batch プール ノードのターゲット オペレーティング システムで実行できる任意の優先アプリケーションに置き換えることができます。 

次の表は、このアクティビティに固有のプロパティの名前と説明です。 

| プロパティ              | 説明                              | 必須 |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | パイプラインのアクティビティの名前     | あり      |
| 説明           | アクティビティの動作を説明するテキスト。  | いいえ       |
| type                  | カスタム アクティビティの場合、アクティビティの種類は **Custom** です。 | あり      |
| 既定のコンテナー     | Azure Batch にリンクされたサービス。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。  | あり      |
| コマンド               | 実行されるカスタム アプリケーションのコマンド。 アプリケーションが Azure Batch プール ノードで既に使用可能な場合は、resourceLinkedService と folderPath を省略できます。 たとえば、Windows バッチ プール ノードでネイティブでサポートされている `cmd /c dir` をコマンドとして指定できます。 | あり      |
| resourceLinkedService | カスタム アプリケーションが格納されているストレージ アカウントへの Azure Storage のリンクされたサービス。 | いいえ       |
| folderPath            | カスタム アプリケーションとそのすべての依存関係のフォルダーのパス。 | いいえ       |
| referenceObjects      | 既存のリンクされたサービスとデータセットの配列。 カスタム コードが Data Factory のリソースを参照できるように、参照されているリンクされたサービスとデータセットが JSON 形式でカスタム アプリケーションに渡されます。 | いいえ       |
| extendedProperties    | カスタム コードが追加のプロパティを参照できるように、JSON 形式でカスタム アプリケーションに渡すことができるユーザー定義プロパティ。 | いいえ       |

## <a name="passing-objects-and-properties"></a>オブジェクトとプロパティを渡す

次のサンプルは、referenceObjects と extendedProperties を使用して、Data Factory オブジェクトとユーザー定義プロパティをカスタム アプリケーションに渡す方法を示しています。 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            }           
        }
      }
    }]
  }
}
```

アクティビティを実行すると、referenceObjects と extendedProperties が、SampleApp.exe の同じ実行フォルダーに配置されている次のファイルに格納されます。 

- activity.json

  extendedProperties とカスタム アクティビティのプロパティが格納されます。 

- linkedServices.json

  referenceObjects プロパティで定義されているリンクされたサービスの配列が格納されます。 

- datasets.json

  referenceObjects プロパティで定義されているデータセットの配列が格納されます。 

次のサンプル コードは、SampleApp.exe が JSON ファイルの必要な情報にアクセスする方法を示しています。 

```C#
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

####<a name="retrieve-execution-outputs"></a>実行の出力を取得する

次の PowerShell コマンドを使用して、サンプル パイプラインのパイプライン実行を開始し、実行結果を監視できます。 

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -dataFactoryName "factoryName" -PipelineName "pipelineName" 
$result = Get-AzureRmDataFactoryV2ActivityRun -dataFactoryName "factoryName" -PipelineRunId $runId -RunStartedAfter "2017-09-06" -RunStartedBefore "2017-12-31"
$result.output -join "`r`n" 
$result.Error -join "`r`n" 
```

カスタム アプリケーションの **stdout** と **stderr** は、タスクの GUID を使用して Azure Batch のリンクされたサービスを作成したときに定義した、Azure Storage のリンクされたサービスの **adfjobs** コンテナーに保存されます。 次のスニペットに示すように、アクティビティの実行の出力から詳細なパスを取得できます。 

```shell
"exitcode": 0
"outputs": [
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stdout.txt",
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stderr.txt"
]
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

> [!IMPORTANT]
> - activity.json、linkedServices.json、datasets.json は、Bath タスクのランタイム フォルダーに格納されます。 この例では、activity.json、linkedServices.json、datasets.json は、https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/runtime/ パスに格納されます。 必要に応じて、パスを個別にクリーンアップしてください。 
> - リンクされたサービスでセルフホステッド統合ランタイムを使用している場合、顧客が定義したプライベート ネットワーク環境内に資格情報を保持できるように、セルフホステッド統合ランタイムによってキーやパスワードなどの機密情報が暗号化されます。 この場合、カスタム アプリケーション コードから一部の機密フィールドを参照したときにフィールドが見つからない可能性があります。 必要に応じて、リンクされたサービスの参照を使用するのではなく、extendedProperties で SecureString を使用してください。 



## <a name="auto-scaling-of-azure-batch"></a>Azure Batch の自動スケール
**自動スケール** 機能で、Azure Batch プールを作成することもできます。 たとえば、専用 VM 数が 0 の Azure Batch プールと、保留中のタスクの数に基づく自動スケールの数式を作成できます。 

ここでのサンプル数式では次の動作が行われます。プールが最初に作成されるとき、1 つの VM から始まります。 $PendingTasks メトリックにより、実行中 + アクティブ (キューに登録済み) 状態のタスク数が定義されます。  この数式により、最後の 180 秒間の保留タスク平均数が判明し、TargetDedicated が適宜設定されます。 それにより、TargetDedicated が 25 台の仮想マシンを超えることはありません。 そのため、新しいタスクが送信されると、プールが自動的に増加します。タスクが完了すると、VM は 1 台ずつ解放され、自動スケールにより VM が減らされます。 startingNumberOfVMs と maxNumberofVMs はニーズに合わせて調整できます。

自動スケールの数式:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

詳細については、「 [Azure Batch プール内のコンピューティング ノードの自動スケール](../batch/batch-automatic-scaling.md) 」をご覧ください。

プールで既定の [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)を使用する場合、Batch サービスがカスタム アクティビティを実行する前に VM を準備するのに 15 ～ 30 分かかることがあります。  プールが異なる 　autoScaleEvaluationInterval を使用する場合、Batch サービスは autoScaleEvaluationInterval + 10 分を要することがあります。


## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [Machine Learning バッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
