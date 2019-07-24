---
title: 状態の確認、ログの設定、アラートの取得 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps の状態の監視、診断データのログ記録、およびアラートの設定を行います
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 80776f9284752e8554486cb458096ccc9319949e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112315"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Azure Logic Apps の状態の監視、診断ログの設定、アラートの有効化

[ロジック アプリを作成して実行する](../logic-apps/quickstart-create-first-logic-app-workflow.md)と、その実行の履歴、トリガーの履歴、状態、パフォーマンスを確認できます。 リアルタイムでのイベントの監視と高度なデバッグについては、ご利用のロジック アプリの[診断ログ](#azure-diagnostics)を設定します。 このようにして、トリガー イベント、実行イベント、アクション イベントなど、[イベントを検索して表示する](#find-events)ことができます。 また、Azure Storage や Azure Event Hubs などの[他のサービスでこの診断データ](#extend-diagnostic-data)を使用することもできます。 

エラーやその他考えられる問題に関する通知を受け取るには、[アラート](#add-azure-alerts)を設定します。 たとえば、"1 時間に 5 件を超える実行が失敗したとき" を検出するアラートを作成できます。 また、[Azure Diagnostics イベントの設定とプロパティ](#diagnostic-event-properties)を使用すると、監視、追跡、ログ記録をプログラムで設定することもできます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>ロジック アプリの実行の履歴とトリガーの履歴を表示する

1. [Azure Portal](https://portal.azure.com) でご利用のロジック アプリを探すには、Azure のメイン メニューの **[すべてのサービス]** を選択します。 検索ボックスに「ロジック アプリ」と入力し、**[ロジック アプリ]** を選択します。

   ![ロジック アプリを検索する](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure Portal には、所有する Azure サブスクリプションに関連付けられているロジック アプリがすべて表示されます。 

2. ご利用のロジック アプリを選択し、**[概要]** を選択します。

   Azure Portal では、そのロジック アプリの実行の履歴とトリガーの履歴が表示されます。 例: 

   ![ロジック アプリの実行の履歴とトリガーの履歴](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **[実行の履歴]** には、ご利用のロジック アプリの実行がすべて表示されます。 
   * **[トリガーの履歴]** には、ご利用のロジック アプリのトリガー アクティビティがすべて表示されます。

   状態の説明については、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

   > [!TIP]
   > 必要なデータが見つからない場合は、ツール バーの **[更新]** を選択してください。

3. 特定の実行からステップを表示するには、**[実行の履歴]** でその実行を選択します。 

   モニター ビューには、その実行に含まれる各ステップが表示されます。 例: 

   ![特定の実行のアクション](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. その実行について詳細を確認するには、**[実行の詳細]** を選択します。 この情報は、その実行のステップ、状態、入出力をまとめたものです。 

   ![[実行の詳細] を選択する](media/logic-apps-monitor-your-logic-apps/run-details.png)

   たとえば、その実行の**関連付け ID** を取得できます。これは、[Logic Apps 用の REST API](https://docs.microsoft.com/rest/api/logic) を使用する際に必要になる場合があります。

5. 特定のステップの詳細を取得するには、そのステップを選択します。 これで、そのステップに対して発生した入出力やエラー (ある場合) などの詳細を確認できるようになりました。 例: 

   ![ステップの詳細](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Logic Apps サービス内では、実行時の詳細情報とイベントはすべて暗号化されます。 これらの暗号化が解除されるのは、ユーザーがそのデータの表示を要求したときのみです。 また、これらのイベントへのアクセスは、[Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) で制御することができます。

6. 特定のトリガー イベントの詳細を取得するために、**[概要]** ウィンドウに戻ります。 **[トリガーの履歴]** で、トリガー イベントを選択します。 これで、次のように、入出力などの詳細を確認できます。

   ![トリガー イベントの出力の詳細](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>ロジック アプリの診断ログを有効にする

実行時の詳細情報やイベントを使用した高度なデバッグの場合は、[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)を使用して診断ログを設定できます。 Azure Monitor は Azure のサービスであり、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持するうえで役立ちます。 

開始する前に、Log Analytics ワークスペースを用意しておく必要があります。 [Log Analytics ワークスペースの作成方法](../azure-monitor/learn/quick-create-workspace.md)に関するページを参照してください。

1. [Azure Portal](https://portal.azure.com) で、ご利用のロジック アプリを探して選択します。 

2. ロジック アプリ ブレードのメニューの **[監視]** で、**[診断]** > **[診断設定]** の順に選択します。

   ![[監視]、[診断]、[診断設定] の順に移動する](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. **[診断設定]** で **[オン]** を選択します。

   ![診断ログを有効にする](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. ここで、次のように、ログ用に Log Analytics ワークスペースとイベント カテゴリを選択します。

   1. **[Log Analytics への送信]** を選択します。 
   2. **[Log Analytics]** で、**[構成]** を選択します。 
   3. **[OMS ワークスペース]** で、ログに使用するワークスペースを選択します。
      > [!NOTE]
      > OMS ワークスペースは、Log Analytics ワークスペースと呼ばれるようになりました。
   4. **[ログ]** で、**[WorkflowRuntime]** カテゴリを選択します。
   5. メトリックの間隔を選択します。
   6. 完了したら、**[保存]** を選択します。

   ![Log Analytics ワークスペースとログ対象のデータを選択する](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

これで、トリガー イベント、実行イベント、アクション イベントに関して、イベントとその他のデータを検索できるようになりました。

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>ロジック アプリのイベントとデータを検索する

トリガー イベント、実行イベント、アクション イベントなど、ご利用のロジック アプリのイベントを検索して表示するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、**[すべてのサービス]** を選択します。 次に示すように、"ログ分析" を検索し、**[Log Analytics]** を選択します。

   ![[Log Analytics] を選択する](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. **[Log Analytics]** で、ご利用の Log Analytics ワークスペースを見つけて選択します。 

   ![Log Analytics ワークスペースを選択する](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. **[管理]** で、**[ログ検索]** を選択します。

   ![[ログ検索] を選択する](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. 検索ボックスに、検索するフィールドを指定し、**Enter** キーを押します。 入力を開始すると、一致候補と使用できる操作が表示されます。 

   たとえば、発生した上位 10 件のイベントを検索するには、**search Category == "WorkflowRuntime" | limit 10** という検索クエリを入力して選択します。

   ![検索文字列を入力する](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   詳細については、[Azure Monitor ログでのデータの検索方法](../log-analytics/log-analytics-log-searches.md)に関する記事を参照してください。

5. 結果ページの左側のバーで、表示する期間を選択します。
フィルターを追加してクエリを絞り込むには、**[+ 追加]** を選択します。

   ![クエリ結果の期間を選択する](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. **[フィルターの追加]** で、フィルター名を入力すると、目的のフィルターを見つけることができます。 そのフィルターを選択し、**[+追加]** を選択します。

   この例では、"status" という単語を使用して、**AzureDiagnostics** で失敗したイベントを検索します。
   ここでは、**status_s** のフィルターが既に選択されています。

   ![フィルターを選択する](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. 左側のバーで、使用するフィルター値を選択し、**[適用]** を選択します。

   ![フィルター値を選択して [適用] を選択する](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. ここで、作成しているクエリに戻ります。 このクエリは、選択したフィルターと値によって更新されます。 以前の結果もフィルター処理されています。

   ![フィルター処理された結果が表示されているクエリに戻る](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. クエリを将来使用するために保存するには、**[保存]** を選択します。 [クエリの保存方法](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query)を確認してください。

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>診断データを他のサービスで使用する方法と場所を拡張する

Azure Monitor ログと併せて、ロジック アプリの診断データを他の Azure サービスで使用する方法を次のように拡張できます。 

* [Azure 診断ログを Azure Storage にアーカイブする](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Azure 診断ログを Azure Event Hubs にストリーミングする](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

これにより、[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) や [Power BI](../azure-monitor/platform/powerbi.md) などの他のサービスのテレメトリと分析を使用したリアルタイム監視が可能になります。 例: 

* [Event Hubs からStream Analytics にデータをストリーミングする](../stream-analytics/stream-analytics-define-inputs.md)
* [ストリーミング データを Stream Analytics で分析し、Power BI でリアルタイム分析ダッシュボードを作成する](../stream-analytics/stream-analytics-power-bi-dashboard.md)

設定するオプションに基づいて、[Azure ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md)または[Azure イベント ハブの作成](../event-hubs/event-hubs-create.md)を最初に行うようにしてください。 その後、診断データの送信先のオプションを選択してください。

![データを Azure ストレージ アカウントまたはイベント ハブに送信する](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> 保有期間は、ストレージ アカウントの使用を選択した場合にのみ適用されます。

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>ロジック アプリのアラートを設定する

ご利用のロジック アプリの特定のメトリックまたは超過したしきい値を監視するには、[Azure のアラート](../azure-monitor/platform/alerts-overview.md)を設定します。 [Azure のメトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)について確認してください。 

[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)を使用せずにアラートを設定するには、次の手順を実行します。 高度なアラートの条件とアクションの場合は、[Azure Monitor ログも設定してください](#azure-diagnostics)。

1. ロジック アプリ ブレードのメニューの **[監視]** で、次に示すように、**[診断]** > **[アラート ルール]** > **[アラートの追加]** の順に選択します。

   ![ロジック アプリのアラートを追加する](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. **[アラート ルールの追加]** ブレードで、次のようにアラートを作成します。

   1. **[リソース]** で、ご利用のロジック アプリを選択します (まだ選択されていない場合)。 
   2. アラートの名前と説明を指定します。
   3. 追跡する**メトリック**またはイベントを選択します。
   4. **条件**を選択し、メトリックの**しきい値**を指定して、このメトリックを監視する**期間**を選択します。
   5. このアラートに関するメールを送信するかどうかを選択します。 
   6. アラートを送信する場合は、その他のメール アドレスを指定します。 
   また、アラートの送信先の webhook URL も指定できます。

   たとえば、次のルールでは、1 時間以内に 5 件以上の実行が失敗したときにアラートを送信します。

   ![メトリックのアラート ルールを作成する](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> アラートからロジック アプリを実行するために、[要求トリガー](../connectors/connectors-native-reqres.md)をワークフローに含めることができます。これにより、次の例のようなタスクを実行できます。
> 
> * [Slack に投稿する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [テキストを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [メッセージをキューに追加する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Azure Diagnostics イベントの設定と詳細

各診断イベントには、ご利用のロジック アプリとそのイベントに関する詳細 (状態、開始時刻、終了時刻など) が含まれています。 監視、追跡、ログをプログラムで設定する際に、これらの詳細を [Azure Logic Apps 用 REST API](https://docs.microsoft.com/rest/api/logic) と [Azure Diagnostics 用 REST API](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) で使用できます。

たとえば、`ActionCompleted` イベントには、追跡と監視に使用できる `clientTrackingId` プロパティと `trackedProperties` プロパティがあります。

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`:指定しなかった場合、自動的にこの ID が生成され、ロジック アプリの実行でイベント (このロジック アプリから呼び出される入れ子になったワークフローなど) どうしが関連付けられます。 この ID は、カスタム ID 値を指定した `x-ms-client-tracking-id` ヘッダーをトリガー要求で渡すことで、トリガーから手動で指定できます。 要求トリガー、HTTP トリガー、または webhook トリガーを使用できます。

* `trackedProperties`:診断データで入力または出力を追跡するために、ロジック アプリの JSON 定義で追跡対象プロパティをアクションに追加することができます。 追跡対象プロパティで追跡できるのは、1 つのアクションの入出力のみです。ただし、イベントの `correlation` プロパティを使用すると、1 回の実行に含まれる複数のアクションにわたってそれらを相互に関連付けることができます。

  1 つ以上のプロパティを追跡するには、`trackedProperties` セクションと必要なプロパティをアクションの定義に追加します。 たとえば、"オーダー ID" などテレメトリ内のデータを追跡する場合は次のとおりです。

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>次の手順

* [ロジック アプリのデプロイとリリースの管理用にテンプレートを作成する](../logic-apps/logic-apps-create-deploy-template.md)
* [Enterprise Integration Pack を使用した B2B シナリオ](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [B2B メッセージを監視する](../logic-apps/logic-apps-monitor-b2b-message.md)
