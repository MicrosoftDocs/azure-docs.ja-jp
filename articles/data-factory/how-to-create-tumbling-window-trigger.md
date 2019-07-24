---
title: Azure Data Factory でのタンブリング ウィンドウ トリガーの作成 | Microsoft Docs
description: タンブリング ウィンドウでパイプラインを実行するトリガーを Azure Data Factory で作成する方法について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: shlo
ms.openlocfilehash: 6fbdee71ab1123c258a5191a78e38f51eb41cbab
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433231"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>タンブリング ウィンドウでパイプラインを実行するトリガーの作成
この記事では、タンブリング ウィンドウ トリガーを作成、起動、および監視する手順について説明します。 トリガーとサポートされる種類の全般的な情報については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関する記事をご覧ください。

タンブリング ウィンドウ トリガーは、状態を維持しながら、指定した開始時刻から定期的に実行される種類のトリガーです。 タンブリング ウィンドウとは、固定サイズで重複しない一連の連続する時間間隔です。 タンブリング ウィンドウ トリガーはパイプラインと 1 対 1 の関係を持ち、単一のパイプラインのみを参照できます。

## <a name="data-factory-ui"></a>Data Factory UI

タンブリング ウィンドウ トリガーを Azure Portal で作成するには、**[トリガー] > [タンブリング ウィンドウ] > [次へ]** を選択し、タンブリング ウィンドウを定義するプロパティを構成します。

![タンブリング ウィンドウ トリガーを Azure Portal で作成する](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>タンブリング ウィンドウのトリガーの種類のプロパティ
タンブリング ウィンドウには、次のトリガーの種類のプロパティがあります。

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

次の表に、タンブリング ウィンドウ トリガーの繰り返しとスケジュール設定に関連する主な JSON 要素の概要を示します。

| JSON 要素 | 説明 | type | 使用できる値 | 必須 |
|:--- |:--- |:--- |:--- |:--- |
| **type** | トリガーの種類。 種類は固定値の "TumblingWindowTrigger" です。 | String | "TumblingWindowTrigger" | はい |
| **runtimeState** | トリガー実行時の現在の状態。<br/>**メモ**:この要素は \<readOnly> です。 | String | "Started"、"Stopped"、"Disabled" | はい |
| **frequency** | トリガーが繰り返される頻度の単位 (分または時間) を表す文字列。 **startTime** の日付値が **frequency** 値よりも細かい場合、ウィンドウの境界を計算するときに **startTime** の日付が考慮されます。 たとえば、**frequency** 値が時間単位で、**startTime** 値が 2017-09-01T10:10:10Z の場合、最初のウィンドウは (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z) になります。 | String | "minute"、"hour"  | はい |
| **interval** | トリガーの実行頻度を決定する、**frequency** 値の間隔を示す正の整数。 たとえば、**interval** が 3 で **frequency** が "hour" の場合、トリガーは 3 時間ごとに繰り返されます。 | 整数 | 正の整数。 | はい |
| **startTime**| 最初の発生。これは過去の場合があります。 最初のトリガー間隔は、(**startTime**、**startTime** + **interval**) になります。 | DateTime | DateTime 値。 | はい |
| **endTime**| 最後の発生。これは過去の場合があります。 | DateTime | DateTime 値。 | はい |
| **delay** | ウィンドウのデータ処理の開始の遅延時間。 パイプライン実行は、予想される実行時間 + **delay** の時間が経過してから開始されます。 **delay** は、トリガーが期限を過ぎてから新しい実行をトリガーするまでの待機時間を定義します。 **delay** によってウィンドウの **startTime** が変更されるわけではありません。 たとえば、**delay** 値が 00:10:00 の場合、10 分の遅延を意味します。 | Timespan<br/>(hh:mm:ss)  | 期間の値。既定値は 00:00:00 です。 | いいえ  |
| **maxConcurrency** | 準備ができているウィンドウに対して発生する同時トリガー実行の数。 たとえば、前日の実行を 1 時間ごとにバックフィルすると、24 ウィンドウになります。 **maxConcurrency** = 10 の場合、トリガー イベントは最初の 10 ウィンドウ (00:00-01:00 - 09:00-10:00) に対してのみ発生します。 最初の 10 回がトリガーされたパイプライン実行が完了すると、次の 10 ウィンドウ (10:00-11:00 - 19:00-20:00) に対してトリガー実行が発生します。 **maxConcurrency** = 10 のこの例を続けると、準備ができているウィンドウが 10 個ある場合、パイプライン実行は合計 10 回になります。 準備ができているウィンドウが 1 つしかない場合、パイプライン実行は 1 回だけになります。 | 整数 | 1 ～ 50 の整数。 | はい |
| **retryPolicy:Count** | パイプライン実行前の再試行回数は "Failed" とマークされます。  | 整数 | 整数。既定値は 0 (再試行なし) です。 | いいえ  |
| **retryPolicy: intervalInSeconds** | 秒単位で指定された再試行の間の遅延。 | 整数 | 秒数。既定値は 30 です。 | いいえ  |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart および WindowEnd システム変数

**パイプライン**定義 (つまり、クエリの一部) で、タンブリング ウィンドウ トリガーの **WindowStart** および **WindowEnd** システム変数を使用できます。 システム変数は、**トリガー**定義でパイプラインにパラメーターとして渡します。 次の例は、これらの変数をパラメーターとして渡す方法を示しています。

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

パイプライン定義で **WindowStart** および **WindowEnd** システム変数値を使用するには、"MyWindowStart" パラメーターと "MyWindowEnd" パラメーターを適宜使用します。

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>バックフィル シナリオでのウィンドウの実行順序
(特にバックフィル シナリオで) 実行するウィンドウが複数ある場合、ウィンドウの実行順序は決定論的であり、最も古い間隔から最も新しい間隔の順になります。 現時点では、この動作を変更することはできません。

### <a name="existing-triggerresource-elements"></a>既存の TriggerResource 要素
既存の **TriggerResource** 要素には、次の点が適用されます。

* トリガーの **frequency** 要素の値 (またはウィンドウ サイズ) が変更されても、既に処理されたウィンドウの状態はリセット*されません*。 トリガーは、新しいウィンドウ サイズを使用して実行された最後のウィンドウから、ウィンドウに対して引き続き発生します。
* トリガーの **endTime** 要素の値が変更 (追加または更新) されても、既に処理されたウィンドウの状態はリセット*されません*。 トリガーは新しい **endTime** 値に従います。 新しい **endTime** 値が既に実行されたウィンドウよりも前の場合、トリガーは停止します。 それ以外の場合は、新しい **endTime** 値に達すると、トリガーは停止します。

## <a name="sample-for-azure-powershell"></a>Azure PowerShell のサンプル

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

ここでは、Azure PowerShell を使用してトリガーを作成、起動、および監視する方法について説明します。

1. 次の内容が含まれた **MyTrigger.json** という名前の JSON ファイルを C:\ADFv2QuickStartPSH\ フォルダーに作成します。

    > [!IMPORTANT]
    > JSON ファイルを保存する前に、**startTime** 要素の値を現在の UTC 時間に設定します。 **endTime** 要素の値を現在の UTC 時間の 1 時間後に設定します。

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. **Set-AzDataFactoryV2Trigger** コマンドレットを使用してトリガーを作成します。

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. **Get-AzDataFactoryV2Trigger** コマンドレットを使用して、トリガーの状態が **Stopped** であることを確認します。

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. **Start-AzDataFactoryV2Trigger** コマンドレットを使用してトリガーを起動します。

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. **Get-AzDataFactoryV2Trigger** コマンドレットを使用して、トリガーの状態が **Started** であることを確認します。

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. **Get-AzDataFactoryV2TriggerRun** コマンドレットを使用して、Azure PowerShell でトリガー実行を取得します。 トリガー実行に関する情報を取得するには、次のコマンドを定期的に実行します。 トリガー定義の値に合わせて、**TriggerRunStartedAfter** と **TriggerRunStartedBefore** の値を更新します。

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Azure Portal でトリガー実行とパイプライン実行を監視するには、[パイプライン実行の監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)に関するセクションをご覧ください。

## <a name="next-steps"></a>次の手順
トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#triggers)」をご覧ください。
