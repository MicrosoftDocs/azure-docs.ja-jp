---
title: Azure Data Factory の Wait アクティビティ | Microsoft Docs
description: Wait アクティビティは、指定した期間にわたってパイプラインの実行を停止します。
services: data-factory
documentationcenter: ''
author: shlo
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/12/2018
ms.author: shlo
ms.openlocfilehash: 66d79bc1597cd8f3c7e01eb8227eb7c91ba04d1d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807815"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Azure Data Factory での wait アクティビティの実行
パイプラインで Wait アクティビティを使用すると、パイプラインは、指定した期間待った後、後続のアクティビティの実行を続行します。 

## <a name="syntax"></a>構文

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | `Wait` アクティビティの名前。 | String | はい
type | **Wait** に設定する必要があります。 | String | はい
waitTimeInSeconds | パイプラインが処理を続行するまでの待ち時間 (秒数)。 | 整数 | はい

## <a name="example"></a>例

> [!NOTE]
> このセクションでは、パイプラインを実行するための JSON の定義とサンプル PowerShell コマンドを紹介しています。 Azure PowerShell と JSON 定義を使用して Data Factory パイプラインを作成するための詳細な手順が記載されているチュートリアルについては、[Azure PowerShell を使用したデータ ファクトリの作成に関するチュートリアル](quickstart-create-data-factory-powershell.md)を参照してください。

### <a name="pipeline-with-wait-activity"></a>Wait アクティビティを含むパイプライン
この例では、パイプラインに 2 つのアクティビティ **Until** および **Wait** が含まれています。 Wait アクティビティは、1 秒間待つように構成されています。 パイプラインは、実行の間の待ち時間が 1 秒に設定されたループ内で Web アクティビティを実行します。 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
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
