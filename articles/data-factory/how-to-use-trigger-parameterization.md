---
title: パイプラインにトリガー情報を渡す
description: パイプラインでトリガー メタデータを参照する方法について説明します
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 50a9f9cd59ebeecae89580c878442eb20788f462
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593647"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>パイプライン実行でのトリガー メタデータの参照

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、トリガーの開始時刻などのトリガー メタデータをパイプラインの実行で使用する方法について説明します。

パイプラインでは、それを呼び出したトリガーからメタデータを認識し、読み取ることが必要になる場合があります。 たとえば、タンブリング ウィンドウ トリガーの実行では、期間の開始時刻と終了時刻に基づいて、パイプラインによって異なるデータ スライスまたはフォルダーが処理されます。 Azure Data Factory では、パラメーター化と[システム変数](control-flow-system-variables.md)を使用して、トリガーからパイプラインにメタデータを渡します。

このパターンは、トリガーがウィンドウの開始時刻と終了時刻を提供する [タンブリング ウィンドウ トリガー](how-to-create-tumbling-window-trigger.md)、トリガーが [カスタム定義 _データ_ フィールド](../event-grid/event-schema.md)の値を解析および処理する[カスタム イベント トリガー](how-to-create-custom-event-trigger.md)で特に便利です。

> [!NOTE]
> 提供されるメタデータ情報は、トリガーの種類ごとに異なります。 詳細については、[システム変数](control-flow-system-variables.md)に関するページを参照してください

## <a name="data-factory-ui"></a>Data Factory UI

このセクションでは、Azure Data Factory ユーザー インターフェイス内でトリガーからパイプラインにメタデータ情報を渡す方法について説明します。

1. **[作成キャンバス]** にアクセスしてパイプラインを編集します

1. 空のキャンバスをクリックして、パイプラインの設定を表示します。 アクティビティは選択しないでください。 設定パネルが折りたたまれている可能性があるため、その場合はキャンバスの下部から引き出す必要があります

1. **[パラメーター]** セクションを選択し、 **[+ 新規]** を選択してパラメーターを追加します

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="パイプラインでパラメーターを定義する方法を示すパイプライン設定のスクリーンショット。":::

1. **[+ トリガー]** をクリックして、トリガーをパイプラインに追加します。

1. パイプラインにトリガーを作成またはアタッチして、 **[OK]** をクリックします

1. 次のページで、各パラメーターのトリガー メタデータを入力します。 トリガー情報を取得するには、[システム変数](control-flow-system-variables.md)で定義した形式を使用します。 記入する必要があるのは、トリガー メタデータの値を想定しているパラメーターのみです。すべてのパラメーターには必要ありません。 たとえば、ここでは、トリガー実行の開始時刻を *parameter_1* に割り当てます。

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="トリガー情報をパイプライン パラメーターに渡す方法を示すトリガー定義ページのスクリーンショット。":::

1. パイプラインで値を使用するには、システム変数 __ではなく__、パイプライン定義のパラメーター _@pipeline().parameters.parameterName_ を使用します。 たとえば、この例でトリガーの開始時刻を読み取るには、@pipeline().parameters.parameter_1 を参照します。

## <a name="json-schema"></a>JSON スキーマ

パイプライン実行にトリガー情報を渡すには、トリガーとパイプライン json の両方を _parameters_ セクションで更新する必要があります。

### <a name="pipeline-definition"></a>パイプラインの定義

**properties** セクションで、**parameters** セクションにパラメーターの定義を追加します

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>トリガー定義

**pipelines** セクション内の **parameters** セクションで、パラメーター値を割り当てます。 記入する必要があるのは、トリガー メタデータの値を想定しているパラメーターのみです。すべてのパラメーターには必要ありません。

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>パイプラインでのトリガー情報の使用

パイプラインで値を使用するには、システム変数 __ではなく__、パイプライン定義のパラメーター _@pipeline().parameters.parameterName_ を使用します。

## <a name="next-steps"></a>次のステップ

トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#trigger-execution)」をご覧ください。
