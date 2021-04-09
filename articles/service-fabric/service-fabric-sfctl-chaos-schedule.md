---
title: Azure Service Fabric CLI - sfctl chaos schedule
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 chaos をスケジュール設定するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: dd911574cd37c18e3043cfc2482bd1fcd5f3857b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260851"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
chaos のスケジュールを取得および設定します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| get | Chaos をいつどのようにして実行するかを定義する Chaos Schedule を取得します。 |
| set | Chaos によって使用されるスケジュールを設定します。 |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Chaos をいつどのようにして実行するかを定義する Chaos Schedule を取得します。

使用中の Chaos Schedule のバージョンと、Chaos をいつどのようにして実行するかを定義する Chaos Schedule を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Chaos によって使用されるスケジュールを設定します。

Chaos は、Chaos Schedule に基づいて実行を自動的にスケジュールします。 提供されたバージョンがサーバー上のバージョンと一致する場合、Chaos Schedule が更新されます。 Chaos Schedule を更新すると、サーバー上のバージョンが 1 つ増加します。 サーバー上のバージョンは、大きい番号に到達した後、0 に戻ります。 この呼び出しが行われたときに Chaos が実行されていると、呼び出しは失敗します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --chaos-parameters-dictionary | ジョブによって使用される ChaosParameters への文字列名のマッピングを表す JSON でエンコードされたリスト。 |
| --expiry-date-utc | Schedule を使用した Chaos のスケジュール設定を停止する日付と時刻。  既定値\: 9999-12-31T23\:59\:59.999Z。 |
| --jobs | Chaos を実行する日時と Chaos の実行で使用するパラメーターを表す ChaosScheduleJobs の JSON でエンコードされたリスト。 |
| --start-date-utc | Schedule を使用した Chaos のスケジュール設定を開始する日付と時刻。  既定値\: 1601-01-01T00\:00\:00.000Z。 |
| --timeout -t | 既定値\: 60。 |
| --version | Schedule のバージョン番号。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

### <a name="examples"></a>例

次のコマンドは、2016-01-01 から 2038-01-01 まで無停止で Chaos を実行するスケジュールを設定します (現在のスケジュールのバージョンは 0 であるものとします)。
Chaos は、その時刻にクラスター上でスケジュールされます。
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](./scripts/sfctl-upgrade-application.md)を使用して、Service Fabric CLI の使用方法を学習します。
