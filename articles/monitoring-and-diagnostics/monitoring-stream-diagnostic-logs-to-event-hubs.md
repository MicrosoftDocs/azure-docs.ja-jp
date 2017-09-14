---
title: "Event Hubs 名前空間への Azure 診断ログのストリーミング | Microsoft Docs"
description: "Azure 診断ログを Event Hubs 名前空間にストリーミングする方法について説明します。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 01ba8ddfcf90e1368ac147296fd180f99420d96f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="stream-azure-diagnostic-logs-to-an-event-hubs-namespace"></a>Azure 診断ログを Event Hubs 名前空間にストリーミングする
**[Azure 診断ログ](monitoring-overview-of-diagnostic-logs.md)**は、ポータルに組み込まれた [Event Hubs にエクスポート] オプションを使用するか、Azure PowerShell コマンドレットまたは Azure CLI を使用して診断設定で Service Bus 規則 ID を有効にすることによって、任意のアプリケーションにほぼリアルタイムでストリーミングできます。

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>診断ログと Event Hubs で実行できること
診断ログでストリーミング機能を使用する場合、次のような方法があります。

* **サード パーティのログおよびテレメトリ システムにログをストリーミングする** - 将来的に、Event Hubs のストリーミングは、診断ログをサード パーティの SIEM やログ分析ソリューションにパイプ処理するためのメカニズムになります。
* **"ホットパス" データを PowerBI にストリーミングしてサービスの正常性を表示する** - Event Hubs、Stream Analytics、PowerBI を使用して、診断データを Azure サービスに関するほぼリアルタイムの洞察に簡単に変換できます。 Event Hubs をセットアップし、Stream Analytics でデータを処理して、PowerBI を出力として使用する方法の概要については、[こちらの記事](../stream-analytics/stream-analytics-power-bi-dashboard.md)をご覧ください。 診断ログを設定する際のヒントを次に示します。
  
  * オプションをポータルで有効にするか、PowerShell を使用して有効にすると、診断ログの特定のカテゴリのイベント ハブが自動的に作成されます。そのため、名前が **insights-** で始まる名前空間のイベント ハブを選択できます。
  * 次の SQL コードは、PowerBI テーブルのすべてのログ データの解析に使用できる Stream Analytics クエリの一例です。

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **カスタムのテレメトリおよびログ プラットフォームを構築する** - カスタム構築されたテレメトリ プラットフォームが既にある場合や構築を検討している場合は、Event Hubs の非常にスケーラブルな発行/サブスクライブの特性により、診断ログを柔軟に取り込むことができます。 グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法については、[Dan Rosanova によるガイド](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)をご覧ください。

## <a name="enable-streaming-of-diagnostic-logs"></a>診断ログのストリーミングの有効化
診断ログのストリーミングは、プログラム、ポータル、または [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings) を使用して有効にすることができます。 どの方法でも、Event Hubs 名前空間と、名前空間に送信するログ カテゴリおよびメトリックを指定する診断設定を作成します。 有効にしたログ カテゴリごとにイベント ハブがその名前空間に作成されます。 診断**ログ カテゴリ**とは、リソースで収集できるログの種類です。

> [!WARNING]
> コンピューティング リソース (VM や Service Fabric など) の診断ログを有効にし、ストリーミングするには、 [別の手順が必要](../event-hubs/event-hubs-streaming-azure-diags-data.md)となります。
> 
> 

設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、Service Bus または Event Hubs 名前空間は、ログを出力するリソースと同じサブスクリプションに属している必要はありません。

## <a name="stream-diagnostic-logs-using-the-portal"></a>ポータルを使用して診断ログをストリーミングする
1. ポータルで、Azure Monitor に移動し、**[診断設定]** をクリックします。

    ![Azure Monitor の [監視] セクション](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. 必要に応じて、リソース グループまたはリソースの種類で一覧をフィルタリングして、診断設定を行うリソースをクリックします。

3. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 [診断を有効にする] をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   リソースに既存の設定が存在する場合は、このリソースで構成済みの設定の一覧が表示されます。 [診断設定の追加] をクリックします。

   ![診断設定の追加 - 既存の設定が存在する](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. 設定に名前をつけて **[イベント ハブへのストリーム]** のチェック ボックスをオンにしてから、Event Hubs 名前空間を選択します。
   
   ![診断設定の追加 - 既存の設定が存在する](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)
    
   診断ログを初めてストリーミングする場合は、選択した名前空間にイベント ハブが作成されます。そのログ カテゴリを選択した名前空間にストリーミングするリソースが既に存在する場合は、その名前空間にイベント ハブがストリーミングされます。また、ストリーミング メカニズムで使用するアクセス許可をポリシーで定義します。 現在、イベント ハブにストリーミングするには、管理、送信、リッスンの各アクセス許可が必要です。 Event Hubs 名前空間の共有アクセス ポリシーは、ポータルの名前空間の [構成] タブで作成または変更できます。 これらの診断設定のいずれかを更新するには、クライアントに Event Hubs の承認規則に対する ListKey アクセス許可が必要です。

4. [ **Save**] をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されるとすぐに、診断ログがそのストレージ アカウントにストリーミングされます。

### <a name="via-powershell-cmdlets"></a>PowerShell コマンドレットの使用
[Azure PowerShell コマンドレット](insights-powershell-samples.md)を使用してストリーミングを有効にするには、次のパラメーターを指定して `Set-AzureRmDiagnosticSetting` コマンドレットを使用します。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -ServiceBusRuleId [your Service Bus rule ID] -Enabled $true
```

Service Bus 規則 ID は、`{Service Bus resource ID}/authorizationrules/{key name}` の形式の文字列です。たとえば、`/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{Service Bus namespace}/authorizationrules/RootManageSharedAccessKey` のようになります。

### <a name="via-azure-cli"></a>Azure CLI の使用
[Azure CLI](insights-cli-samples.md) を使用してストリーミングを有効にするには、次のように `insights diagnostic set` コマンドを使用します。

```azurecli
azure insights diagnostic set --resourceId <resourceID> --serviceBusRuleId <serviceBusRuleID> --enabled true
```

Service Bus 規則 ID には、PowerShell コマンドレットのセクションで説明したものと同じ形式を使用します。

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

| 要素名 | Description |
| --- | --- |
| records |このペイロード内のすべてのログ イベントの配列。 |
| time |イベントが発生した時刻。 |
| カテゴリ |このイベントのログ カテゴリ。 |
| resourceId |このイベントを生成したリソースのリソース ID。 |
| operationName |操作の名前。 |
| level |省略可能。 ログ イベントのレベル。 |
| プロパティ |イベントのプロパティ。 |

Event Hubs へのストリーミングをサポートするすべてのリソース プロバイダーの一覧については、[こちら](monitoring-overview-of-diagnostic-logs.md)をご覧ください。

## <a name="stream-data-from-compute-resources"></a>Compute リソースからのデータのストリーミング
診断ログは、Windows Azure 診断エージェントを使用して Compute リソースからストリーミングすることもできます。 その設定方法については、[この記事を参照してください](../event-hubs/event-hubs-streaming-azure-diags-data.md)。

## <a name="next-steps"></a>次のステップ
* [Azure 診断ログの詳細を確認する](monitoring-overview-of-diagnostic-logs.md)
* [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


