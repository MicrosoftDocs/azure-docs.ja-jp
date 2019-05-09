---
title: Azure Data Factory でのパイプラインの実行アクティビティ | Microsoft Docs
description: パイプラインの実行アクティビティを使用して、ある Data Factory パイプラインを別の Data Factory パイプラインから呼び出す方法について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: a0ece499262464bc28f55c37188698a3313e2c04
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57998707"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Azure Data Factory でのパイプラインの実行アクティビティ
パイプラインの実行アクティビティを使用すると、Data Factory のパイプラインが別のパイプラインを呼び出すことができます。

## <a name="syntax"></a>構文

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | パイプラインの実行アクティビティの名前。 | String | はい
type | 次のように設定する必要があります。**ExecutePipeline**。 | String | はい
pipeline | このパイプラインが呼び出す依存パイプラインへのパイプライン参照。 パイプライン参照オブジェクトには、**referenceName** と **type** の 2 つのプロパティがあります。 ReferenceName プロパティは、参照パイプラインの名前を指定します。 type プロパティを PipelineReference に設定する必要があります。 | PipelineReference | はい
parameters | 呼び出されたパイプラインに渡されるパラメーター | パラメーター名を引数値にマップする JSON オブジェクト | いいえ 
waitOnCompletion | 依存パイプラインの実行が終了するまでアクティビティの実行を待機するかどうかを定義します。 既定値は false です。 | Boolean | いいえ 

## <a name="sample"></a>サンプル
このシナリオでは、次の 2 つのパイプラインがあります。

- **マスター パイプライン** - このパイプラインには、呼び出されたパイプラインを呼び出す 1 つのパイプラインの実行アクティビティが含まれます。 マスター パイプラインは、`masterSourceBlobContainer` と `masterSinkBlobContainer` の 2 つのパラメーターを使用します。
- **呼び出されたパイプライン** - このパイプラインには、Azure Blob ソースからデータを Azure Blob シンクにコピーする 1 つのコピー アクティビティが含まれます。 呼び出されたパイプラインは、`sourceBlobContainer` と `sinkBlobContainer` の 2 つのパラメーターを使用します。

### <a name="master-pipeline-definition"></a>マスター パイプラインの定義

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>呼び出されたパイプラインの定義

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**リンクされたサービス**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**ソース データセット**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**シンク データセット**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>パイプラインの実行

この例でマスター パイプラインを実行する場合、masterSourceBlobContainer パラメーターと masterSinkBlobContainer パラメーターに対して次の値が渡されます。 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

マスター パイプラインは、次の例で示すように、呼び出されたパイプラインにこれらの値を転送します。 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
