---
title: "Azure Monitor を使用して、データ ファクトリを監視する |Microsoft Docs"
description: "Azure Data Factory からの情報を使用して診断ログを有効にし、Azure Monitor を使用して Data Factory パイプラインを監視する方法を説明します。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: a130907fe6cf2ae6498502644949290bc838f239
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Azure Monitor を使用して、データ ファクトリを監視する  
クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 監視では、アプリケーションを正常な状態で稼働させ続けるためのデータを取得できます。 また、潜在的な問題を防止したり、発生した問題をトラブルシューティングするのにも役立ちます。 さらに、監視データを使用して、アプリケーションに関する深い洞察を得ることもできます。 この知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。

Azure Monitor では、Microsoft Azure のほとんどのサービス向けにベース レベルのインフラストラクチャのメトリックおよびログを提供します。 詳細については、[監視の概要](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)を参照してください。 Azure 診断ログは、リソースによって出力されるログで、そのリソースの操作に関する豊富なデータを提供します。 データ ファクトリは、Azure Monitor で診断ログを出力します。 

## <a name="diagnostic-logs"></a>診断ログ

* 監査や手動での検査に使用するために診断ログを **ストレージ アカウント** に保存する。 診断設定を使用して、リテンション期間 (日数) を指定できます。
* サード パーティのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、診断ログを **Event Hubs** にストリーミングします。
* **Operations Management Suite (OMS) Log Analytics** を使用して解析する

ログを出力するリソースのサブスクリプションとは別のサブスクリプションにある、ストレージ アカウントまたはイベント ハブ名前空間を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な ロールベースのアクセス制御 (RBAC) アクセス権限を持っている必要があります。

## <a name="set-up-diagnostic-logs"></a>診断ログの設定

### <a name="diagnostic-settings"></a>診断設定
非コンピューティング リソースの診断ログは、診断設定を使用して構成します。 リソースの診断設定では、以下を制御します。

* 診断ログの送信先 (ストレージ アカウント、Event Hubs、OMS Log Analytics)。
* 送信するログ カテゴリ。
* ログの各カテゴリをストレージ アカウントに保持する期間。
* リテンション期間が 0 日の場合、ログは永続的に保持されます。 リテンション期間が 0 日の場合、ログは永続的に保持されます。
* リテンション期間ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合 (たとえば、Event Hubs または OMS オプションだけが選択されている場合)、保持ポリシーは無効になります。
* 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。

### <a name="enable-diagnostic-logs-via-rest-apis"></a>REST API を使用した診断ログの有効化

Azure Monitor REST API の診断設定を作成または更新する

**要求**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**ヘッダー**
* `{api-version}` を `2016-09-01` で置き換え
* `{resource-id}` を、診断設定を編集するリソースの リソース ID で置き換えます。 詳細については、[リソース グループを使用した Azure リソースの管理](../azure-resource-manager/resource-group-portal.md)に関するページを参照してください。
* `Content-Type` ヘッダーを `application/json` に設定します。
* Azure Active Directory から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、[要求の認証](../active-directory/develop/active-directory-authentication-scenarios.md)に関するページを参照してください。

**本文**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
} 
```

| プロパティ | 型 | Description |
| --- | --- | --- |
| storageAccountId |String | 診断ログを送信するストレージ アカウントのリソース ID。 |
| serviceBusRuleId |String | 診断ログのストリーミングのために Event Hubs を作成するサービス バス名前空間のサービス バス ルール ID。 ルール ID の形式は、"{サービス バス リソース ID}/authorizationrules/{キー名}"　です。|
| workspaceId | 複合型 | メトリックの時間グレインと、その保有ポリシーの配列。 現時点では、このプロパティは空です。 |
|metrics| 呼び出されたパイプラインに渡されるパイプライン実行のパラメーター値| 引数値に JSON オブジェクト マッピングするパラメーター名 | 
| ログ| 複合型| リソースの種類に対応する診断ログ カテゴリの名前。 リソースの診断ログ カテゴリの一覧を取得するには、まず診断設定の取得操作を実行します。 |
| カテゴリ| String| ログ カテゴリの配列とその保有ポリシー |
| timeGrain | String | ISO 8601 期間形式でキャプチャされるメトリックの粒度。 PT1M (1 分) にする必要があります。|
| 有効| Boolean | このリソースに対して、そのメトリックまたはログ カテゴリの収集を有効にするどうかを指定します|
| retentionPolicy| 複合型| メトリックまたはログのカテゴリの保持ポリシーを示します。 ストレージ アカウントのオプションのみに使用されます。|
| 日数| int| メトリックまたはログを保持する日数。 値が 0 の場合、ログは無期限に保存されます。 ストレージ アカウントのオプションのみに使用されます。 |

**応答**

200 OK


```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Azure Monitor REST API の診断設定に関する情報を取得する

**要求**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**ヘッダー**
* `{api-version}` を `2016-09-01` で置き換え
* `{resource-id}` を、診断設定を編集するリソースの リソース ID で置き換えます。 詳細については、「リソース グループを使用した Azure リソースの管理」に関するページを参照してください。
* `Content-Type` ヘッダーを `application/json` に設定します。
* Azure Active Directory から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、要求の認証に関するページを参照してください。

**応答**

200 OK

```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/0ee78edb-a0ad-456c-a0a2-901bf542c102/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
詳細については、ここ](https://msdn.microsoft.com/en-us/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>ログとイベントのスキーマ

### <a name="activity-run-logs-attributes"></a>アクティビティ実行ログ属性

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties:" 
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| プロパティ | 型 | Description | 例 |
| --- | --- | --- | --- |
| Level |String | 診断ログのレベルです。 アクティビティの実行ログの場合、レベル 4 常時です。 | `4`  |
| correlationId |String | 特定の要求をエンド ツー エンドで追跡する一意の ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Timespan 内のイベントの時刻、UTC 形式 | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| String| アクティビティ実行の ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| String| パイプライン実行の ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | データ ファクトリのリソースに関連付けられているリソース ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|カテゴリ| String | 診断ログのカテゴリ このプロパティは "ActivityRuns" に設定します | `ActivityRuns` |
|level| String | 診断ログのレベルです。 このプロパティは "Informational" に設定します | `Informational` |
|operationName| String |状態付きのアクティビティの名前。 状態がハートビート開始の場合は、`MyActivity -` です。 状態がハートビート終了の場合は、最終的な状態の `MyActivity - Succeeded` です | `MyActivity - Succeeded` |
|pipelineName| String | パイプラインの名前 | `MyPipeline` |
|activityName| String | アクティビティの名前 | `MyActivity` |
|start| String | Timespan でのアクティビティ実行の開始、UTC 形式 | `2017-06-26T20:55:29.5007959Z`|
|end| String | Timespan でのアクティビティ実行の終了、UTC 形式 アクティビティがまだ終了していない場合 (アクティビティ開始の診断ログ)、既定値 `1601-01-01T00:00:00Z` が設定されます。  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>パイプライン実行ログ属性

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties": 
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| プロパティ | 型 | Description | 例 |
| --- | --- | --- | --- |
| Level |String | 診断ログのレベルです。 アクティビティ実行ログの場合、レベル 4 です。 | `4`  |
| correlationId |String | 特定の要求をエンド ツー エンドで追跡する一意の ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Timespan 内のイベントの時刻、UTC 形式 | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| String| パイプライン実行の ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | データ ファクトリのリソースに関連付けられているリソース ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|カテゴリ| String | 診断ログのカテゴリ このプロパティは "PipelineRuns" に設定します | `PipelineRuns` |
|level| String | 診断ログのレベルです。 このプロパティは "Informational" に設定します | `Informational` |
|operationName| String |状態付きパイプラインの名前。 パイプラインの実行が完了したとき、最終的な状態で "パイプライン - 成功"| `MyPipeline - Succeeded` |
|pipelineName| String | パイプラインの名前 | `MyPipeline` |
|start| String | Timespan でのアクティビティ実行の開始、UTC 形式 | `2017-06-26T20:55:29.5007959Z`|
|end| String | Timespan でのアクティビティ実行の終了、UTC 形式 アクティビティがまだ終了していない場合 (アクティビティ開始の診断ログ)、既定値 `1601-01-01T00:00:00Z` が設定されます。  | `2017-06-26T20:55:29.5007959Z` |
|status| String | パイプライン実行の最終的な状態 (成功または失敗) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>トリガー実行ログ属性

```json
{ 
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
} 

```

| プロパティ | 型 | Description | 例 |
| --- | --- | --- | --- |
| Level |String | 診断ログのレベルです。 アクティビティ実行ログの場合、レベル 4 に設定します。 | `4`  |
| correlationId |String | 特定の要求をエンド ツー エンドで追跡する一意の ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Timespan 内のイベントの時刻、UTC 形式 | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| String| トリガー実行の ID | `08587023010602533858661257311` |
|resourceId| String | データ ファクトリのリソースに関連付けられているリソース ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|カテゴリ| String | 診断ログのカテゴリ このプロパティは "PipelineRuns" に設定します | `PipelineRuns` |
|level| String | 診断ログのレベルです。 このプロパティは "Informational" に設定します | `Informational` |
|operationName| String |トリガーが正常に起動されたかを示す最終的な状態付きのトリガーの名前。 ハートビートが成功した場合、"MyTrigger - 成功"| `MyTrigger - Succeeded` |
|triggerName| String | トリガーの名前 | `MyTrigger` |
|triggerType| String | トリガーの種類 (手動トリガーまたはスケジュール トリガー) | `ScheduleTrigger` |
|triggerEvent| String | トリガーのイベント | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| String | Timespan でのトリガーの開始、UTC 形式 | `2017-06-26T20:55:29.5007959Z`|
|status| String | トリガーが正常に起動したかどうかを示す最終的な状態 (成功または失敗) | `Succeeded`|

### <a name="metrics"></a>メトリック

Azure Monitor では、テレメトリを使用して、Azure のワークロードのパフォーマンスと正常性を視覚的に確認できます。 Azure テレメトリ データの種類の中でも最も重要なのは、Azure リソースのほとんどから出力されるメトリックであり、これはパフォーマンス カウンターとも呼ばれます。 Azure Monitor では、このメトリックを複数の方法で構成して使用し、監視やトラブルシューティングを行うことができます。

ADFV2 は、次のメトリックを出力します

| **メトリック**           | **メトリックの表示名**         | **単位** | **集計の種類** | **説明**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | 成功したパイプライン実行回数のメトリック | カウント    | 合計                | 分の枠内で成功したパイプライン実行の合計回数 |
| PipelineFailedRuns   | 失敗したパイプライン実行回数のメトリック    | カウント    | 合計                | 分の枠内で失敗したパイプライン実行の合計回数    |
| ActiviySucceededRuns | 成功したアクティビティ実行回数のメトリック | カウント    | 合計                | 分の枠内で成功したアクティビティ実行の合計回数  |
| ActivityFailedRuns   | 失敗したアクティビティ実行回数のメトリック    | カウント    | 合計                | 分の枠内で失敗したアクティビティ実行の合計回数     |
| TriggerSucceededRuns | 成功したトリガー実行の回数メトリック  | カウント    | 合計                | 分の枠内で成功したトリガー実行の合計回数   |
| TriggerFailedRuns    | 失敗したトリガー実行の回数メトリック     | カウント    | 合計                | 分の枠内で失敗したトリガー実行の合計回数      |

メトリックにアクセスするには、記事 - https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics の説明に従ってください。 

## <a name="next-steps"></a>次のステップ
実行によるパイプラインの監視と管理の詳細については、[パイプラインをプログラムで監視および管理する](monitor-programmatically.md)に関する記事を参照してください。 