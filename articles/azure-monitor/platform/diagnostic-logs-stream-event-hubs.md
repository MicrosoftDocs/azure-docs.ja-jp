---
title: Azure 診断ログをイベント ハブにストリーミングする
description: Azure 診断ログをイベント ハブにストリーミングする方法について説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: b5299af375646e7759d0770139df2cd6d7ce105c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314080"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Azure 診断ログをイベント ハブにストリーミングする
**[Azure 診断ログ](diagnostic-logs-overview.md)** は、ポータルに組み込まれた [Event Hubs にエクスポート] オプションを使用するか、Azure PowerShell コマンドレットまたは Azure CLI を使用して診断設定でイベント ハブ承認規則 ID を有効にすることによって、任意のアプリケーションにほぼリアルタイムでストリーミングできます。

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>診断ログと Event Hubs で実行できること
診断ログでストリーミング機能を使用する場合、次のような方法があります。

* **サードパーティ製のログ記録およびテレメトリ システムへのログのストリーミング** – すべての診断ログを単一のイベント ハブにストリーミングして、ログ データをサードパーティ製の SIEM またはログ分析ツールにパイプできます。
* **"ホットパス" データを Power BI にストリーミングしてサービスの正常性を表示する** - Event Hubs、Stream Analytics、Power BI を使用して、診断データを Azure サービスに関するほぼリアルタイムの洞察に簡単に変換できます。 Event Hubs をセットアップし、Stream Analytics でデータを処理して、Power BI を出力として使用する方法の概要については、[こちらの記事](../../stream-analytics/stream-analytics-power-bi-dashboard.md)をご覧ください。 診断ログを設定する際のヒントを次に示します。

  * オプションをポータルで有効にするか、PowerShell を使用して有効にすると、診断ログの特定のカテゴリのイベント ハブが自動的に作成されます。そのため、名前が **insights-** で始まる名前空間のイベント ハブを選択できます。
  * 次の SQL コードは、Power BI テーブルのすべてのログ データの解析に使用できる Stream Analytics クエリの一例です。

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **カスタムのテレメトリおよびログ プラットフォームを構築する** - カスタム構築されたテレメトリ プラットフォームが既にある場合や構築を検討している場合は、Event Hubs の非常にスケーラブルな発行/サブスクライブの特性により、診断ログを柔軟に取り込むことができます。 グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法については、[Dan Rosanova によるガイド](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)をご覧ください。

## <a name="enable-streaming-of-diagnostic-logs"></a>診断ログのストリーミングの有効化

診断ログのストリーミングは、プログラム、ポータル、または [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) を使用して有効にすることができます。 どの方法でも、Event Hubs 名前空間と、名前空間に送信するログ カテゴリおよびメトリックを指定する診断設定を作成します。 有効にしたログ カテゴリごとにイベント ハブがその名前空間に作成されます。 診断**ログ カテゴリ**とは、リソースで収集できるログの種類です。

> [!WARNING]
> コンピューティング リソース (VM や Service Fabric など) の診断ログを有効にし、ストリーミングするには、 [別の手順が必要](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)となります。

設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っており、さらに両方のサブスクリプションが同じ AAD テナントに含まれている限り、Event Hubs 名前空間は、ログを出力するリソースと同じサブスクリプションに属している必要はありません。

> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>ポータルを使用して診断ログをストリーミングする

1. ポータルで、Azure Monitor に移動し、**[診断設定]** をクリックします。

    ![Azure Monitor の [監視] セクション](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-blade.png)

2. 必要に応じて、リソース グループまたはリソースの種類で一覧をフィルタリングして、診断設定を行うリソースをクリックします。

3. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 [診断を有効にする] をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-none.png)

   リソースに既存の設定が存在する場合は、このリソースで構成済みの設定の一覧が表示されます。 [診断設定の追加] をクリックします。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-multiple.png)

3. 設定に名前をつけて **[イベント ハブへのストリーム]** のチェック ボックスをオンにしてから、Event Hubs 名前空間を選択します。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-configure.png)

   診断ログを初めてストリーミングする場合は、選択した名前空間にイベント ハブが作成されます。そのログ カテゴリを選択した名前空間にストリーミングするリソースが既に存在する場合は、その名前空間にイベント ハブがストリーミングされます。また、ストリーミング メカニズムで使用するアクセス許可をポリシーで定義します。 現在、イベント ハブにストリーミングするには、管理、送信、リッスンの各アクセス許可が必要です。 Event Hubs 名前空間の共有アクセス ポリシーは、ポータルの名前空間の [構成] タブで作成または変更できます。 これらの診断設定のいずれかを更新するには、クライアントに Event Hubs の承認規則に対する ListKey アクセス許可が必要です。 また、必要に応じて、イベント ハブの名前を指定できます。 イベント ハブの名前を指定した場合、ログは、新しく作成されたログ カテゴリごとのイベント ハブではなく、名前を指定したイベント ハブにルーティングされます。

4. **[Save]** をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されるとすぐに、診断ログがそのイベント ハブにストリーミングされます。

### <a name="via-powershell-cmdlets"></a>PowerShell コマンドレットの使用

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure PowerShell コマンドレット](../../azure-monitor/platform/powershell-quickstart-samples.md)を使用してストリーミングを有効にするには、次のパラメーターを指定して `Set-AzDiagnosticSetting` コマンドレットを使用します。

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

イベント ハブ承認規則 ID は、`{Event Hub namespace resource ID}/authorizationrules/{key name}` という形式の文字列です。たとえば、`/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey` のようになります。 現時点では、PowerShell を使って特定のイベント ハブ名を選択することはできません。

### <a name="via-azure-cli"></a>Azure CLI の使用

[Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) を使ってストリーミングを有効にするには、[az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) コマンドを使います。

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--logs` パラメーターとして渡された JSON 配列にディクショナリを追加することによって、診断ログに他のカテゴリを追加できます。

`--event-hub-rule` 引数は、PowerShell コマンドレットで説明したようにイベント ハブ承認規則 ID と同じ形式を使います。

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Event Hubs からログ データを使用する方法

Event Hubs からのサンプル出力データを次に示します。

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| 要素名 | 説明 |
| --- | --- |
| records |このペイロード内のすべてのログ イベントの配列。 |
| time |イベントが発生した時刻。 |
| category |このイベントのログ カテゴリ。 |
| resourceId |このイベントを生成したリソースのリソース ID。 |
| operationName |操作の名前。 |
| level |省略可能。 ログ イベントのレベル。 |
| properties |イベントのプロパティ。 |

Event Hubs へのストリーミングをサポートするすべてのリソース プロバイダーの一覧については、[こちら](diagnostic-logs-overview.md)をご覧ください。

## <a name="stream-data-from-compute-resources"></a>Compute リソースからのデータのストリーミング

診断ログは、Windows Azure Diagnostics エージェントを使用して Compute リソースからストリーミングすることもできます。 その設定方法については、[この記事を参照してください](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)。

## <a name="next-steps"></a>次の手順

* [Azure Monitor による Azure Active Directory ログのストリーム](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Azure 診断ログの詳細を確認する](diagnostic-logs-overview.md)
* [Event Hubs の使用](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

