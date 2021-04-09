---
title: Azure Data Factory の ForEach アクティビティ
description: ForEach アクティビティは、パイプライン内の繰り返し制御フローを定義します。 これは、コレクションに対する反復処理に使用され、指定されたアクティビティを実行します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: a0c3a3cbaa71d627f54550cf92c067afbb1eb3f0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786211"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Azure Data Factory の ForEach アクティビティ
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ForEach アクティビティは、パイプライン内の繰り返し制御フローを定義します。 このアクティビティは、コレクションを反復処理するために使用され、指定されたアクティビティをループで実行します。 このアクティビティのループの実装は、プログラミング言語の Foreach ループ構造に似ています。

## <a name="syntax"></a>構文
プロパティは、この記事の後の方で説明します。 items プロパティはコレクションであり、次の構文に示すように、コレクション内の各項目は `@item()` を使用して参照されます。  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | ForEach アクティビティの名前。 | String | はい
type | **ForEach** に設定する必要があります | String | はい
isSequential | ループを順番に実行するか、または並行して実行するかを指定します。  一度に最大 20 のループ反復処理を並行して実行できます。 たとえば、**isSequential** が False に設定された状態で、10 個の異なるソースとシンク データセットがあるコピー アクティビティに対して ForEach アクティビティ反復処理を実行すると、一度にすべてのコピーが実行されます。 既定値は False です。 <br/><br/> "isSequential" が False に設定されている場合は、複数の実行可能ファイルを実行するための正しい構成が存在することを確認してください。 そうでない場合は、書き込みの競合が発生しないようにするために、このプロパティを慎重に使用する必要があります。 詳細については、「[Parallel execution (並列実行)](#parallel-execution)」セクションを参照してください。 | Boolean | いいえ。 既定値は False です。
batchCount | 並列実行の数を制御するために使用するバッチの数 (IsSequential が false に設定されている場合)。 これはコンカレンシーの上限ですが、For-Each アクティビティは常にこの数値で実行されるわけではありません | Integer (最大 50) | いいえ。 既定値は 20 です。
アイテム | 反復処理される JSON 配列を返す式。 | 式 (これは JSON 配列を返します) | はい
Activities | 実行されるアクティビティ。 | アクティビティの一覧 | はい

## <a name="parallel-execution"></a>並列実行
**isSequential** が False に設定されている場合、このアクティビティは最大 20 の同時実行反復処理と並行して反復処理します。 この設定は、慎重に使用する必要があります。 同時実行反復処理が同じフォルダーではあっても、異なるファイルへの書き込みである場合、このアプローチは適切です。 同時実行反復処理がまったく同じファイルへの同時書き込みである場合、このアプローチはエラーの原因になる可能性があります。 

## <a name="iteration-expression-language"></a>反復処理の式言語
ForEach アクティビティでは、反復処理される配列をプロパティ **items** として指定します。 ForEach アクティビティで 1 つの列挙を反復処理するには、`@item()` を使用します。 たとえば、**items** が配列: [1, 2, 3] である場合、`@item()` は最初の反復処理で 1 を、2 番目の反復処理で 2 を、3 番目の反復処理で 3 を返します。

## <a name="iterating-over-a-single-activity"></a>1 つのアクティビティを反復処理する
**シナリオ:** Azure BLOB 内の同じソース ファイルから Azure BLOB 内の複数の宛先ファイルにコピーします。

### <a name="pipeline-definition"></a>パイプラインの定義

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>BLOB データセットの定義

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>実行パラメーターの値

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>複数のアクティビティを反復処理する
ForEach アクティビティで複数のアクティビティ (例: コピー アクティビティと Web アクティビティ) を反復処理できます。 このシナリオでは、複数のアクティビティを個別のパイプラインに抽象化することをお勧めします。 次に、ForEach アクティビティを含むパイプラインの [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)を使用して、複数のアクティビティを含む個別のパイプラインを呼び出すことができます。 


### <a name="syntax"></a>構文

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>例
**シナリオ:** ExecutePipeline アクティビティを使用して ForEach アクティビティ内で InnerPipeline を反復処理します。 内部パイプラインは、パラメーター化されたスキーマ定義でコピーします。

#### <a name="master-pipeline-definition"></a>マスター パイプラインの定義

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>内部パイプラインの定義

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>ソース データセットの定義

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>シンク データセットの定義

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>マスター パイプラインのパラメーター
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>出力の集計

__foreach__ アクティビティの出力を集計するには、_Variables_ と _Append Variable_ アクティビティを使用してください。

最初に、パイプライン内で `array` "_変数_" を宣言します。 次に、各 __foreach__ ループ内で _Append Variable_ アクティビティを呼び出します。 その後、配列から集計を取得できます。

## <a name="limitations-and-workarounds"></a>制限事項と回避策

ForEach アクティビティと提案される回避策のいくつかの制限を次に示します。

| 制限事項 | 回避策 |
|---|---|
| 別の ForEach ループ (または Until ループ) 内に ForEach ループを入れ子にすることはできません。 | 入れ子にされたループが含まれる内側パイプライン上で外側の ForEach ループが含まれる外側パイプラインが反復される 2 段のパイプラインを設計します。 |
| ForEach アクティビティには並列処理のために最大 50 の `batchCount` と、最大 100,000 の項目が含まれています。 | 内側パイプライン上で ForEach アクティビティが含まれる外側パイプラインが反復される 2 段のパイプラインを設計します。 |
| SetVariable は、並列で実行される ForEach アクティビティ内では使用できません。この変数は、パイプライン全体に対してグローバルであるため、ForEach やその他のアクティビティは対象とされません。 | 順次 ForEach を使用するか、ForEach (子パイプラインで処理される変数/パラメーター) 内で Execute パイプラインを使用することを検討してください。|
| | |

## <a name="next-steps"></a>次のステップ
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
