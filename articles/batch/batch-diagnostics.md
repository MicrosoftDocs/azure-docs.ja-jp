---
title: メトリック、アラート、診断ログ
description: プールやタスクなど Azure Batch アカウント リソースの診断ログ イベントを記録して分析します。
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: 83411d7018155955f5be71bd41803e510edbc9da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592678"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch の診断の評価と監視用のメトリック、アラート、およびログ

この記事では、[Azure Monitor](../azure-monitor/overview.md) の機能を使用して、Batch アカウントを監視する方法を説明します。 Azure Monitor は、Batch アカウント内のリソースの[メトリック](../azure-monitor/essentials/data-platform-metrics.md)と[診断ログ](../azure-monitor/essentials/platform-logs-overview.md)を収集します。 このデータを収集し、さまざまな方法で使用して、Batch アカウントの監視と問題の診断を行います。 [メトリック アラート](../azure-monitor/alerts/alerts-overview.md)を構成して、メトリックが指定した値に達したときに通知を受信するように構成することもできます。

## <a name="batch-metrics"></a>Batch メトリック

メトリックは、Azure リソースによって生成され、Azure Monitor サービスによって使用される Azure テレメトリ データです (パフォーマンス カウンターとも呼ばれます)。 Batch アカウントのメトリックスの例として、プール作成イベント、優先順位の低いノードの数、タスク完了イベントがあります。

[サポートされる Batch メトリックの一覧](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts)を参照してください。

メトリックは、

- 各 Batch アカウントで追加構成なしで既定で有効になります。
- 1 分ごとに生成されます。
- 自動的に保存されることはありませんが、30 日間のローリング履歴があります。 診断ログの一部として、アクティビティ メトリックを保存できます。

## <a name="view-batch-metrics"></a>Batch メトリックの表示

Azure portal のアカウントの **[概要]** ページには、既定でキー ノード、コア、およびタスク メトリックが表示されます。

Azure portal のすべての Batch アカウントのメトリックを表示するには:

1. Azure portal で、 **[すべてのサービス]**  >  **[Batch アカウント]** の順に選択し、Batch アカウントの名前を選択します。
2. **[監視]** で **[メトリック]** を選びます。
3. **[メトリックの追加]** を選択し、ドロップダウン リストからメトリックを選択します。
4. メトリックの **[集計方法]** オプションを選択します。 カウントベースのメトリック ([Dedicated Core Count]\(専用コア数\) や [Low-Priority Node Count]\(優先順位の低いノードの数\) など) の場合は、 **[平均]** 集計を使用します。 イベントベースのメトリック ([Pool Resize Complete Events]\(プール サイズの変更完了イベント\) など) の場合は、 **[カウント]** 集計を使用します。

   > [!WARNING]
   > [合計] 集計は使用しないでください。グラフの期間中に受信したすべてのデータ ポイントの値が合計されます。

5. その他のメトリックを追加するには、手順 3 と 4 を繰り返します。

Azure Monitor API を使用して、メトリックをプログラムで取得することもできます。 例については、「[.NET を使用した Azure Monitor メトリックの取得](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)」を参照してください。

### <a name="batch-metric-reliability"></a>Batch メトリックの信頼性

メトリックは傾向を特定するのに役立ち、データ分析に使用できます。 メトリックの配信は保証されておらず、順番どおりではない配信、データの損失、重複が発生する可能性があることに注意してください。 このため、単一のイベントを使用してアラートを生成したり関数をトリガーしたりすることはお勧めしません。 アラートのしきい値を設定する方法の詳細については、次のセクションを参照してください。

過去 3 分間に生成されたメトリックはまだ集計されている可能性があるため、この期間中はメトリック値が過小報告される可能性があります。

## <a name="batch-metric-alerts"></a>Batch メトリック アラート

指定したメトリックの値が割り当てられたしきい値に通過したときにトリガーされる、リアルタイムに近い "*メトリック アラート*" を構成することができます。 このアラートで通知が生成されるのは、アラートが "アクティブ化済み" になったとき (しきい値を超え、アラートの条件を満たしたとき) と、"解決済み" になったとき (しきい値を再び超え、条件を満たさなくなったとき) です。

メトリックに関して順番どおりではない配信、データの損失、および重複が発生する可能性があるため、単一のデータ ポイントに関してトリガーされるアラートはお勧めしません。 アラートを作成するときに、しきい値を使用して、これらの不整合を考慮することができます。

たとえば、優先順位の低いコアの数が特定のレベルに落ちた場合に生成されるメトリック アラートを構成して、プールの構成を調整できるようにします。 最良の結果を得るには、低優先度コア数の平均値が期間全体のしきい値を下回ったときにアラートをトリガーするための期間は 10 分以上に設定してください。 これにより、メトリックが集計する時間が増えるため、より正確な結果を得ることができます。

Azure portal でメトリック アラートを構成するには:

1. **[すべてのサービス]**  >  **[Batch アカウント]** の順に選択し、Batch アカウントの名前を選択します。
2. **[監視]** ページで、 **[アラート]** を選択し、 **[新しいアラート ルール]** を選択します。
3. **[条件の選択]** をクリックし、メトリックを選択します。 **[グラフの期間]** 、 **[しきい値の種類]** 、 **[演算子]** 、および **[集計の種類]** の値を確認し、 **[しきい値]** を入力します。 **[完了]** を選択します。
4. 既存のアクション グループを選択するか、新しいアクション グループを作成して、アラートにアクション グループを追加します。
5. **[アラート ルールの詳細]** セクションで、 **[アラート ルール名]** および **[説明]** を入力し、 **[重要度]** を選択します
6. **[アラート ルールの作成]** を選択します。

メトリック アラートの作成の詳細については、「[Azure Monitor でのメトリック アラートのしくみを理解する](../azure-monitor/alerts/alerts-metric-overview.md)」および「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-metric.md)」を参照してください。

リアルタイムに近い通知は、Azure Monitor [REST API](/rest/api/monitor/) を使用して構成することもできます。 詳細については、「[Microsoft Azure のアラートの概要](../azure-monitor/alerts/alerts-overview.md)」を参照してください。 ジョブ、タスク、またはプール固有の情報をアラートに含めるには、「[Azure Monitor のアラートを使用してイベントに応答する](../azure-monitor/alerts/tutorial-response.md)」の検索クエリに関する情報を参照してください。

## <a name="batch-diagnostics"></a>Batch 診断

診断ログには、Azure リソースによって生成された、各リソースの操作を記述する情報が含まれます。 Batch では、次のログを収集できます。

- プールやタスクなどの個々の Batch リソースの存続期間中に Azure Batch サービスによって生成された **サービス ログ** イベント。
- アカウント レベルの **メトリック** ログ。

診断ログの収集を有効にする設定は、既定では有効になりません。 監視する Batch アカウントごとに診断ログを明示的に有効にする必要があります。

### <a name="log-destinations"></a>ログの保存先

一般的なシナリオは、ログの保存先として Azure Storage アカウントを選択することです。 Azure Storage にログを格納するには、ログの収集を有効にする前にアカウントを作成します。 ストレージ アカウントを Batch アカウントに関連付けた場合は、ログの保存先としてそのアカウントを選択できます。

または、次のことも可能です。

- Batch 診断ログ イベントを [Azure Event Hub](../event-hubs/event-hubs-about.md) にストリーミングします。 Event Hubs は、毎秒数百万のイベントを取り込み、任意のリアルタイム分析プロバイダーを使用して変換および格納できます。
- 診断ログを [Azure Monitor ログ](../azure-monitor/logs/log-query-overview.md)に送信して分析したり、Power BI または Excel で分析するためにエクスポートしたりできます。

> [!NOTE]
> Azure サービスで診断ログ データの格納または処理を行うには、追加料金が発生することがあります。

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Batch 診断ログの収集を有効にする

Azure portal に新しい診断設定を作成するには、次の手順に従います。

1. Azure portal で、 **[すべてのサービス]**  >  **[Batch アカウント]** の順に選択し、Batch アカウントの名前を選択します。
2. **[監視]** で **[診断設定]** を選択します。
3. **[診断設定]** ページで、 **[診断設定を追加する]** を選択します。
4. 設定の名前を入力します。
5. 宛先を選択します。 **[Log Analytics への送信]** 、 **[ストレージ アカウントへのアーカイブ]** 、 **[イベント ハブへのストリーム]** から指定します。 ストレージ アカウントを選択する場合、必要に応じて保持ポリシーを設定できます。 リテンション期間の日数を指定しない場合は、ストレージ アカウントが有効である間、データは保持されます。
6. **[ServiceLog]** 、 **[AllMetrics]** 、またはその両方を選択します。
7. **[保存]** を選択して診断設定を作成します。

[Azure portal で Azure Monitor を使用して収集を有効にする](../azure-monitor/essentials/diagnostic-settings.md)ことで、診断設定を構成することもでき、これを行うには、[Resource Manager テンプレート](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)を使用するか、Azure PowerShell または Azure CLI を使用します。 詳細については、「[Azure プラットフォーム ログの概要](../azure-monitor/essentials/platform-logs-overview.md)」を参照してください。

### <a name="access-diagnostics-logs-in-storage"></a>ストレージ内の診断ログにアクセスする

Batch 診断ログをストレージ アカウント内にアーカイブする場合は、関連するイベントが発生するとすぐに、ストレージ アカウント内にストレージ コンテナーが作成されます。 次の名前付けパターンに従って、BLOB が作成されます。

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

次に例を示します。

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

各 `PT1H.json` BLOB ファイルには、BLOB の URL で指定された時間 (たとえば `h=12`) 内に発生した JSON 形式のイベントが含まれます。 現在の時間内にイベントが発生すると、`PT1H.json` ファイルにイベントが追加されます。 分の値 (`m=00`) は常に `00` です。診断ログ イベントが個々の BLOB に 1 時間ごとに分類されるためです。 (時刻はすべて UTC 形式です)。

`PT1H.json` ログ ファイル内の `PoolResizeCompleteEvent` エントリの例を次に示します。 これには、専用ノードと優先順位の低いノードの現在の数と目標の数に関する情報に加え、操作の開始時刻と終了時刻が含まれます。

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

ストレージ アカウント内の診断ログのスキーマの詳細については、「[Azure リソース ログをストレージ アカウントにアーカイブする](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)」を参照してください。 ストレージ アカウント内のログにプログラムでアクセスするには、Storage API を使用します。

### <a name="service-log-events"></a>サービス ログ イベント

Azure Batch サービス ログが収集される場合、そのログには、プールやタスクなどの個々の Batch リソースの存続期間中に Azure Batch サービスによって生成されたイベントが含まれます。 Batch によって生成された各イベントが、JSON 形式で記録されます。 たとえば、次に示すのはサンプルの **プール作成イベント** の本文です。

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch サービスによって生成されるサービス ログ イベントには、次のものが含まれます。

- [プール作成](batch-pool-create-event.md)
- [プール削除の開始](batch-pool-delete-start-event.md)
- [プール削除の完了](batch-pool-delete-complete-event.md)
- [プールのサイズ変更の開始](batch-pool-resize-start-event.md)
- [プールのサイズ変更の完了](batch-pool-resize-complete-event.md)
- [プールの自動スケーリング](batch-pool-autoscale-event.md)
- [タスク開始](batch-task-start-event.md)
- [タスク完了](batch-task-complete-event.md)
- [タスク失敗](batch-task-fail-event.md)
- [タスク スケジュール失敗](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>次のステップ

- Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。
- [Batch ソリューション](monitoring-overview.md)の詳細を確認します。