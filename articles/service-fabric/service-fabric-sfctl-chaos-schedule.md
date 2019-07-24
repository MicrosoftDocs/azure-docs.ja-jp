---
title: Azure Service Fabric CLI- sfctl chaos schedule | Microsoft Docs
description: Service Fabric CLI sfctl chaos schedule のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668315"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
chaos のスケジュールを取得および設定します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| get | Chaos をいつどのようにして実行するかを定義する Chaos Schedule を取得します。 |
| set | Chaos によって使用されるスケジュールを設定します。 |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Chaos をいつどのようにして実行するかを定義する Chaos Schedule を取得します。

使用中の Chaos Schedule のバージョンと、Chaos をいつどのようにして実行するかを定義する Chaos Schedule を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Chaos によって使用されるスケジュールを設定します。

Chaos は、Chaos Schedule に基づいて実行を自動的にスケジュールします。 指定された入力スケジュールのバージョンは、サーバー上の Chaos Schedule のバージョンと一致している必要があります。 指定されたバージョンがサーバー上のバージョンと一致しない場合、Chaos Schedule は更新されません。 指定されたバージョンがサーバー上のバージョンと一致する場合、Chaos Schedule は更新され、サーバー上の Chaos Schedule のバージョンは 1 だけインクリメントされて、2,147,483,647 を超えると 0 に戻ります。 この呼び出しが行われたときに Chaos が実行されていると、呼び出しは失敗します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --chaos-parameters-dictionary | ジョブによって使用される ChaosParameters への文字列名のマッピングを表す JSON でエンコードされたリスト。 |
| --expiry-date-utc | Schedule を使用した Chaos のスケジュール設定を停止する日付と時刻。  既定値\: 9999-12-31T23\:59\:59.999Z。 |
| --jobs | Chaos を実行する日時と Chaos の実行で使用するパラメーターを表す ChaosScheduleJobs の JSON でエンコードされたリスト。 |
| --start-date-utc | Schedule を使用した Chaos のスケジュール設定を開始する日付と時刻。  既定値\: 1601-01-01T00\:00\:00.000Z。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |
| --version | Schedule のバージョン番号。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

### <a name="examples"></a>例

次のコマンドは、2016-01-01 から 2038-01-01 まで無停止で Chaos を実行するスケジュールを設定します (現在のスケジュールのバージョンは 0 であるものとします)。 Chaos は、その時刻にクラスター上でスケジュールされます。

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


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。
