---
title: Azure Data Factory の変数アクティビティの設定
description: 変数アクティビティの設定を使用して、Data Factory パイプラインで定義されている既存の変数の値を設定する方法について説明します
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/07/2020
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.openlocfilehash: 113829dd35c14b5efae39c55a8085dcd2c1ecef4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786160"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure Data Factory の変数アクティビティの設定
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

変数アクティビティの設定を使用して、Data Factory パイプラインで定義されている文字列型、ブール型、配列型の既存の変数の値を設定します。

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | パイプラインのアクティビティの名前 | はい
description | アクティビティの動作を説明するテキスト | no
type | **SetVariable** に設定する必要があります | はい
value | 変数が割り当てられる文字列リテラルまたは式オブジェクトの値 | はい
variableName | このアクティビティによって設定される変数の名前 | はい

## <a name="incrementing-a-variable"></a>変数のインクリメント

Azure Data Factory の変数に関するよくあるシナリオの 1 つに、until または foreach アクティビティ内で変数を反復子として使用するというものがあります。 変数の設定アクティビティでは、`value` フィールドに設定されている変数を参照することはできません。 この制限を回避するには、一時変数を設定してから、2 番目の変数の設定アクティビティを作成します。 2 番目の変数の設定アクティビティでは、反復子の値が一時変数に設定されます。 

以下に、このパターンの例を示します。

![変数のインクリメント](media/control-flow-set-variable-activity/increment-variable.png "変数の値を増やす")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>次のステップ
Data Factory でサポートされている関連制御フロー アクティビティについては、以下を参照してください。 

- [変数アクティビティの追加](control-flow-append-variable-activity.md)
