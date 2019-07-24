---
title: Azure Data Factory のフィルター アクティビティ | Microsoft Docs
description: フィルター アクティビティは、入力をフィルター処理します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: shlo
ms.openlocfilehash: 787c9393e2700bd7ed349b501e70abc4a0687b9c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021834"
---
# <a name="filter-activity-in-azure-data-factory"></a>Azure Data Factory のフィルター アクティビティ
パイプラインでフィルター アクティビティを使用して、入力配列にフィルター式を適用することができます。 

## <a name="syntax"></a>構文

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | `Filter` アクティビティの名前。 | String | はい
type | **filter** に設定する必要があります。 | String | はい
condition | 入力のフィルター処理に使用する条件。 | 式 | はい
items | フィルターを適用する必要がある入力配列。 | 式 | はい

## <a name="example"></a>例

この例では、パイプラインに 2 つのアクティビティ **Filter** および **ForEach** が含まれています。 フィルター アクティビティは、3 より大きい値を持つ項目の入力配列をフィルター処理するように構成されています。 ForEach アクティビティは、フィルター処理された値を反復処理し、現在の値によって指定された秒数だけ待機します。

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
                "name": "MyForEach",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
