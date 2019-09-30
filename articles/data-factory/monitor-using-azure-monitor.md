---
title: Azure Monitor を使用して、データ ファクトリを監視する |Microsoft Docs
description: Azure Data Factory からの情報を使用して診断ログを有効にし、Azure Monitor を使用して Data Factory パイプラインを監視する方法を説明します。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: c8d78473a1128dd4f96f2cfa0c14d2d3b1b2c1e9
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300559"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Azure Monitor を使用してデータ ファクトリのアラート送信および監視を行う

クラウド アプリケーションは複雑であり、動的なパーツが多数あります。 モニターでは、アプリケーションを正常な状態で確実に稼働させ続けるためのデータを提供します。 モニターは、潜在的な問題を回避し、過去の問題をトラブルシューティングするのにも役立ちます。

監視データを使用して、アプリケーションに関する詳細な分析情報を取得できます。 この知識は、アプリケーションのパフォーマンスと保守容易性の向上に役立ちます。 また、手動操作を必要とするアクションの自動化にも役立ちます。

Azure Monitor では、ほとんどの Azure サービスに対して、基礎レベルのインフラストラクチャのメトリックとログを提供します。 Azure 診断ログはリソースによって出力され、そのリソースの操作に関する豊富な頻出データを提供します。 そして、Azure Data Factory は、Monitor に診断ログを書き込みます。

詳細については、「[Azure Monitor の概要](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)」を参照してください。

## <a name="keeping-azure-data-factory-data"></a>Azure Data Factory データの保持

Data Factory では、パイプライン実行データを 45 日間だけ格納します。 データをより長期間保持する場合は、Monitor を使用してください。 Monitor を使用すると、診断ログを分析用にルーティングできます。 また、それらをストレージ アカウントに保持して、選択した期間中、ファクトリ情報を保持することもできます。

## <a name="diagnostic-logs"></a>診断ログ

* 監査や手動での検査のために、診断ログをストレージ アカウントに保存します。 診断設定を使用して、リテンション期間 (日数) を指定できます。
* ログを Azure Event Hubs にストリーミングします。 ログは、パートナー サービス、または Power BI などのカスタム分析ソリューションへの入力となります。
* ログを Log Analytics で分析します。

ログを出力するリソースのサブスクリプションにないストレージ アカウントまたはイベント ハブ名前空間を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な ロールベースのアクセス制御 (RBAC) アクセス権限を持っている必要があります。

## <a name="set-up-diagnostic-logs"></a>診断ログの設定

### <a name="diagnostic-settings"></a>診断設定

診断設定を使用して、非コンピューティング リソースの診断ログを構成します。 リソース コントロールの設定には、次の機能があります。

* 診断ログを送信する場所を指定します。 たとえば、Azure ストレージ アカウント、Azure イベント ハブ、Monitor ログなどがあります。
* 送信するログ カテゴリを指定します。
* ログの各カテゴリをストレージ アカウントに保持する期間を指定します。
* リテンション期間が 0 日の場合、ログは永続的に保持されます。 または、1 日から 2,147,483,647 日の間の任意の日数を値として指定できます。
* 保持ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合、保持ポリシーは無効になります。 たとえば、この状態は、Event Hubs または Monitor ログのオプションのみが選択されている場合に生じる可能性があります。
* 保持ポリシーは日単位で適用されます。 日と日の間の境界は、協定世界時 (UTC) の午前 0 時になります。 1 日の終わりに、保持ポリシーの期間を超えた日のログが削除されます。 たとえば、保持ポリシーが 1 日の場合、今日が始まった時点で、昨日より前のログは削除されます。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API を使用して診断ログを有効にする

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Monitor REST API の診断設定を作成または更新する

##### <a name="request"></a>Request

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* `{api-version}` を `2016-09-01` で置き換え
* `{resource-id}` を、診断設定を編集するリソースの ID に置き換えます。 詳細については、[リソース グループを使用した Azure リソースの管理](../azure-resource-manager/manage-resource-groups-portal.md)に関するページを参照してください。
* `Content-Type` ヘッダーを `application/json` に設定します。
* Azure Active Directory (Azure AD) から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、[要求の認証](../active-directory/develop/authentication-scenarios.md)に関するページを参照してください。

##### <a name="body"></a>Body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
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

| プロパティ | 種類 | 説明 |
| --- | --- | --- |
| **storageAccountId** |string | 診断ログを送信するストレージ アカウントのリソース ID。 |
| **serviceBusRuleId** |string | 診断ログのストリーミングのために Event Hubs を作成するサービス バス名前空間のサービス バス ルール ID。 ルール ID の形式は、`{service bus resource ID}/authorizationrules/{key name}` です。|
| **workspaceId** | 複合型 | メトリックの時間グレインと、その保持ポリシーの配列。 このプロパティの値は空です。 |
|**メトリック**| 呼び出されたパイプラインに渡されるパイプライン実行のパラメーター値| パラメーター名を引数値にマップする JSON オブジェクト。 |
| **logs**| 複合型| リソースの種類に対応する診断ログ カテゴリの名前。 リソースの診断ログ カテゴリの一覧を取得するには、診断設定の取得操作を実行します。 |
| **category**| string| ログ カテゴリとその保持ポリシーの配列。 |
| **timeGrain** | string | ISO 8601 期間形式でキャプチャされるメトリックの粒度。 プロパティ値は、1 分を指定する `PT1M` でなければなりません。 |
| **有効**| Boolean | このリソースに対して、メトリックまたはログ カテゴリの収集を有効にするどうかを指定します。 |
| **retentionPolicy**| 複合型| メトリックまたはログのカテゴリの保持ポリシーを示します。 このプロパティは、ストレージ アカウントのみに使用されます。 |
|**days**| int| メトリックまたはログを保持する日数。 プロパティ値が 0 の場合、ログは永続的に保持されます。 このプロパティは、ストレージ アカウントのみに使用されます。 |

##### <a name="response"></a>Response

200 OK。


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Monitor REST API の診断設定に関する情報を取得する

##### <a name="request"></a>Request

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* `{api-version}` を `2016-09-01` で置き換え
* `{resource-id}` を、診断設定を編集するリソースの ID に置き換えます。 詳細については、[リソース グループを使用した Azure リソースの管理](../azure-resource-manager/manage-resource-groups-portal.md)に関するページを参照してください。
* `Content-Type` ヘッダーを `application/json` に設定します。
* Azure AD から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、[要求の認証](../active-directory/develop/authentication-scenarios.md)に関するページを参照してください。

##### <a name="response"></a>Response

200 OK。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
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
詳細については、「[診断設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)」を参照してください。

## <a name="schema-of-logs-and-events"></a>ログとイベントのスキーマ

### <a name="monitor-schema"></a>スキーマの監視

#### <a name="activity-run-log-attributes"></a>アクティビティ実行ログの属性

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
   "properties":
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

| プロパティ | 種類 | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |string | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |string | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| string| アクティビティの実行の ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| string| パイプラインの実行の ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| string | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| string | 診断ログのカテゴリ。 プロパティ値は `ActivityRuns` に設定してください。 | `ActivityRuns` |
|**level**| string | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| string | 状態付きのアクティビティの名前。 アクティビティが開始ハートビートである場合、プロパティ値は `MyActivity -` です。 アクティビティが終了ハートビートである場合、プロパティ値は `MyActivity - Succeeded` です。 | `MyActivity - Succeeded` |
|**pipelineName**| string | パイプラインの名前。 | `MyPipeline` |
|**activityName**| string | アクティビティの名前。 | `MyActivity` |
|**start**| string | timespan でのアクティビティの実行の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z`|
|**end**| string | timespan でのアクティビティの実行の終了時刻 (UTC 形式)。 アクティビティが開始したがまだ終了していないことが診断ログに示されている場合、プロパティ値は `1601-01-01T00:00:00Z` です。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>パイプライン実行ログの属性

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

| プロパティ | 種類 | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |string | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |string | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| string| パイプラインの実行の ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| string | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| string | 診断ログのカテゴリ。 プロパティ値は `PipelineRuns` に設定してください。 | `PipelineRuns` |
|**level**| string | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| string | パイプラインの名前とその状態。 パイプラインの実行が完了すると、プロパティ値は `Pipeline - Succeeded` になります。 | `MyPipeline - Succeeded` |
|**pipelineName**| string | パイプラインの名前。 | `MyPipeline` |
|**start**| string | timespan でのアクティビティの実行の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z` |
|**end**| string | timespan でのアクティビティの実行の終了時刻 (UTC 形式)。 アクティビティが開始したがまだ終了していないことが診断ログに示されている場合、プロパティ値は `1601-01-01T00:00:00Z` です。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| string | パイプライン実行の最終的な状態。 プロパティ値は `Succeeded` または `Failed` のいずれかです。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>トリガー実行ログの属性

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

| プロパティ | 種類 | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |string | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |string | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| string| トリガー実行の ID。 | `08587023010602533858661257311` |
|**resourceId**| string | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| string | 診断ログのカテゴリ。 プロパティ値は `PipelineRuns` に設定してください。 | `PipelineRuns` |
|**level**| string | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| string | トリガーの名前と、トリガーが正常に起動されたかどうかを示す最終的な状態。 ハートビートが成功した場合、プロパティ値は `MyTrigger - Succeeded` になります。 | `MyTrigger - Succeeded` |
|**triggerName**| string | トリガーの名前。 | `MyTrigger` |
|**triggerType**| string | トリガーの種類。 プロパティ値は `Manual Trigger` か、`Schedule Trigger` のいずれかです。 | `ScheduleTrigger` |
|**triggerEvent**| string | トリガーのイベント。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| string | timespan でのトリガーの起動の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| string | トリガーが正常に起動されたかどうかを示す最終的な状態。 プロパティ値は `Succeeded` または `Failed` のいずれかです。 | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics のスキーマ

Log Analytics は、Monitor からスキーマを継承します。ただし、次の例外があります。

* 各列名の最初の文字は、大文字になります。 たとえば、Monitor の列名 "correlationId" は、Log Analytics では "CorrelationId" です。
* "Level" 列はありません。
* 動的な "properties" 列は、次の動的 JSON BLOB の種類として維持されます。

    | Azure Monitor の列 | Log Analytics の列 | 種類 |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 動的 |
    | $.properties.Annotations | 注釈 | 動的 |
    | $.properties.Input | 入力 | 動的 |
    | $.properties.Output | Output | 動的 |
    | $.properties.Error.errorCode | ErrorCode | int |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | Error | 動的 |
    | $.properties.Predecessors | Predecessors | 動的 |
    | $.properties.Parameters | parameters | 動的 |
    | $.properties.SystemParameters | SystemParameters | 動的 |
    | $.properties.Tags | Tags | 動的 |
    
## <a name="metrics"></a>メトリック

Monitor を使用すると、Azure ワークロードのパフォーマンスと正常性を可視化できます。 最も重要な種類の Monitor データは、パフォーマンス カウンターとも呼ばれるメトリックです。 メトリックは、ほとんどの Azure リソースによって出力されます。 Monitor には、監視およびトラブルシューティングのために、これらのメトリックを構成して使用する方法が複数用意されています。

Azure Data Factory バージョン 2 では、次のメトリックが出力されます。

| **メトリック**           | **メトリックの表示名**         | **単位** | **集計の種類** | **説明**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | 成功したパイプライン実行回数のメトリック | Count    | 合計                | 1 分の枠内で成功したパイプライン実行の合計数。 |
| PipelineFailedRuns   | 失敗したパイプライン実行回数のメトリック    | Count    | 合計                | 1 分の枠内で失敗したパイプライン実行の合計数。    |
| ActivitySucceededRuns | 成功したアクティビティ実行回数のメトリック | Count    | 合計                | 1 分の枠内で成功したアクティビティの実行の合計数。  |
| ActivityFailedRuns   | 失敗したアクティビティ実行回数のメトリック    | Count    | 合計                | 1 分の枠内で失敗したアクティビティの実行の合計数。     |
| TriggerSucceededRuns | 成功したトリガー実行の回数メトリック  | Count    | 合計                | 1 分の枠内で成功したトリガー実行の合計数。   |
| TriggerFailedRuns    | 失敗したトリガー実行の回数メトリック     | Count    | 合計                | 1 分の枠内で失敗したトリガー実行の合計数。      |

メトリックにアクセスするには、「[Azure Monitor データ プラットフォーム](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)」に記載された手順に従います。

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Azure Monitor で Data Factory メトリックを監視する

Monitor と Data Factory の統合を使用して、データを Monitor にルーティングできます。 この統合は次のシナリオで役立ちます。

* Data Factory によって Monitor に発行された充実したメトリックのセットに複雑なクエリを記述する必要があります。 Monitor を使用して、これらのクエリにカスタム アラートを作成できます。

* データ ファクトリ全体を監視する必要があります。 複数のデータ ファクトリから 1 つの Monitor ワークスペースにデータをルーティングできます。

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>診断設定とワークスペースを構成する

データ ファクトリに診断設定を作成または追加します。

1. ポータルで Monitor に移動します。 **[設定]**  >  **[診断設定]** の順に選択します。

1. 診断設定の対象となるデータ ファクトリを選択します。

1. 選択したデータ ファクトリの設定が存在しない場合は、設定を作成するように求められます。 **[診断を有効にする]** を選択します。

   ![設定が存在しない場合に診断設定を作成する](media/data-factory-monitor-oms/monitor-oms-image1.png)

   データ ファクトリに既存の設定が存在する場合は、データ ファクトリで構成済みの設定の一覧が表示されます。 **[診断設定の追加]** を選択します。

   ![設定が存在する場合に診断設定を追加する](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 設定に名前を付け、 **[Log Analytics への送信]** を選択して、 **[Log Analytics ワークスペース]** からワークスペースを選択します。

    ![設定に名前を付けてログ分析ワークスペースを選択する](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. **[保存]** を選択します。

しばらくすると、このデータ ファクトリに対する設定の一覧に新しい設定が表示されます。 新しいイベント データが生成されるとすぐに、診断ログがそのワークスペースにストリーミングされます。 イベントが生成されてから、Log Analytics に表示されるまでに最大 15 分かかかる場合があります。

> [!NOTE]
> Azure ログ テーブルには 500 個を超える列を含めることができないため、リソース固有モードを選択することを強くお勧めします。 詳細については、[Log Analytics の既知の制限](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics)に関するセクションを参照してください。

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Marketplace から Azure Data Factory Analytics をインストールする

!["Azure Marketplace" にアクセスして、「Analytics filter」と入力し、[Azure Data Factory Analytics (プレビュー)] を選択する](media/data-factory-monitor-oms/monitor-oms-image3.png)

![[Azure Data Factory Analytics (プレビュー)] の詳細](media/data-factory-monitor-oms/monitor-oms-image4.png)

**[作成]** を選択して、 **[OMS ワークスペース]** と **[OMS ワークスペースの設定]** を選択します。

![新しいソリューションの作成](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory メトリックを監視する

Azure Data Factory Analytics をインストールすると、既定のセットのビューが作成され、次のメトリックが有効になります。

- ADF の実行 - 1) Data Factory によるパイプラインの実行
 
- ADF の実行 - 2) Data Factory によるアクティビティの実行

- ADF の実行 - 3) Data Factory によるトリガーの実行

- ADF のエラー - 1) Data Factory によるパイプラインのエラーの上位 10 件

- ADF のエラー - 2) Data Factory によるアクティビティの実行エラーの上位 10 件

- ADF のエラー - 3) Data Factory によるトリガーのエラーの上位 10 件

- ADF の統計 - 1) 種類別のアクティビティの実行

- ADF の統計 - 2) 種類別のトリガーの実行

- ADF の統計 - 3) パイプラインの実行の最長期間

!["Workbooks (プレビュー)" と "AzureDataFactoryAnalytics" が強調表示されているウィンドウ](media/data-factory-monitor-oms/monitor-oms-image6.png)

前述のメトリックの視覚化、これらのメトリックの背後にあるクエリの確認、クエリの編集、アラートの作成、およびその他のアクションを実行できます。

![データ ファクトリによるパイプライン実行のグラフィック表示](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>アラート

Azure portal にサインインし、 **[モニター]**  >  **[アラート]** の順に選択してアラートを作成します。

![ポータル メニューのアラート](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>アラートを作成する

1. **[+ 新しいアラート ルール]** を選択して新しいアラートを作成します。

    ![新しいアラート ルール](media/monitor-using-azure-monitor/alerts_image4.png)

1. アラートの条件を定義します。

    > [!NOTE]
    > **[リソースの種類でフィルター]** ドロップダウン リストでは **[すべて]** を選択するようにしてください。

    ![[アラートの条件を定義します] > [ターゲットの選択] で [リソースの選択] ウィンドウが開きます ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![[アラートの条件を定義します] > [条件の追加] で [シグナル ロジックの構成] ウィンドウを開きます](media/monitor-using-azure-monitor/alerts_image6.png)

    ![[シグナル ロジックの構成] ウィンドウ](media/monitor-using-azure-monitor/alerts_image7.png)

1. アラートの詳細を定義します。

    ![[アラートの詳細]](media/monitor-using-azure-monitor/alerts_image8.png)

1. アクション グループを定義します。

    ![ルールの作成 ([新しいアクショングループ] が強調表示されている)](media/monitor-using-azure-monitor/alerts_image9.png)

    ![新しいアクション グループを作成する](media/monitor-using-azure-monitor/alerts_image10.png)

    ![メール、SMS、プッシュ、音声の構成](media/monitor-using-azure-monitor/alerts_image11.png)

    ![アクション グループの定義](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>次の手順
[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)
