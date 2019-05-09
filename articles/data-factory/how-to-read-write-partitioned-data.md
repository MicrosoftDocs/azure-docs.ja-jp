---
title: Azure Data Factory に対してパーティション分割されたデーの読み取りまたは書き込みを行う方法 | Microsoft Docs
description: Azure Data Factory に対してパーティション分割されたデーの読み取りまたは書き込みを行う方法について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: f2d780900a0cd24f2d70499573a4055dc836ae0b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013572"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Azure Data Factory に対してパーティション分割されたデーの読み取りまたは書き込みを行う方法

Azure Data Factory バージョン 1 では、**SliceStart**、**SliceEnd**、**WindowStart**、および **WindowEnd** システム変数を使用して、パーティション分割されたデータの読み取りまたは書き込みが可能でした。 現在のバージョンの Data Factory では、パイプライン パラメーターと、そのパラメーターの値としてのトリガーの開始時刻またはスケジュールされた時刻を使用してこの動作を実現できます。 

## <a name="use-a-pipeline-parameter"></a>パイプライン パラメーターを使用する 

Data Factory バージョン 1 では、次の例に示すように、**partitionedBy** プロパティと **SliceStart** システム変数を使用できました。 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

**partitonedBy** プロパティの詳細については、「[Azure Data Factory を使用した Azure Blob Storage との間でのデータのコピー](v1/data-factory-azure-blob-connector.md#dataset-properties)」を参照してください。 

現在のバージョンの Data Factory でこの動作を実現するには: 

1. **文字列**型の*パイプライン パラメーター*を定義します。 次の例では、パイプライン パラメーターの名前は **windowStartTime** です。 
2. パイプライン パラメーターの値を参照するために、データセット定義内に **folderPath** を設定します。 
3. パイプラインをオンデマンドで起動するときに、パラメーターの実際の値を渡します。 また、実行時にトリガーの開始時刻またはスケジュールされた時刻を動的に渡すこともできます。 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-a-value-from-a-trigger"></a>トリガーから値を渡す

次のタンブリング ウィンドウ トリガー定義では、トリガーのウィンドウの開始時刻がパイプライン パラメーター **windowStartTime** の値として渡されます。 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>例

データセット定義の例を次に示します。

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

パイプラインの定義: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順

パイプラインを含むデータ ファクトリを作成する方法の完全なチュートリアルについては、「[クイック スタート:データ ファクトリを作成する](quickstart-create-data-factory-powershell.md)」を参照してください。 

