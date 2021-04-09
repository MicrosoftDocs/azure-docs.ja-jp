---
title: Azure Monitor ログを使用してロジック アプリを監視する
description: Azure Monitor ログを設定して Azure Logic Apps の診断データを収集することで、ロジック アプリのトラブルシューティングを行う
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: d1c095be1d0525cc843c04a3b83e14bb4211b175
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721261"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Azure Monitor ログを設定し、Azure Logic Apps の診断データを収集する

実行時にロジック アプリに関するより豊富なデバッグ情報を取得するには、[Azure Monitor ログ](../azure-monitor/logs/data-platform-logs.md)を設定して使用し、トリガー イベント、実行イベント、アクション イベントなどのランタイム データやイベントに関する情報を [Log Analytics ワークスペース](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)に記録して格納します。 [Azure Monitor](../azure-monitor/overview.md) を使用すると、クラウド環境とオンプレミス環境を監視して、可用性とパフォーマンスをより簡単に維持することができます。 Azure Monitor ログを使用することで、この情報を収集して確認するのに役立つ[ログ クエリ](../azure-monitor/logs/log-query-overview.md)を作成できます。 さらに、Azure Storage や Azure Event Hubs などの[他の Azure サービスでこの診断データを使用する](#extend-data)こともできます。

ロジック アプリのログを設定するには、[ロジック アプリを作成するときに Log Analytics を有効にする](#logging-for-new-logic-apps)か、既存のロジック アプリの Log Analytics ワークスペースに [Logic Apps 管理ソリューション をインストール](#install-management-solution)します。 このソリューションでは、ロジック アプリの実行に関する集約情報が提供され、状態、実行時間、再送信の状態、関連付け ID などの特定の詳細情報が含まれています。 次に、この情報に対してログの記録とクエリの作成を有効にするために、[Azure Monitor ログを設定します](#set-up-resource-logs)。

この記事では、新しいロジック アプリと既存のロジック アプリで Log Analytics を有効にする方法、Logic Apps 管理ソリューションをインストールして設定する方法、および Azure Monitor ログのクエリを設定して作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、[Log Analytics ワークスペース](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)が必要です。 ワークスペースがない場合は、[Log Analytics ワークスペースの作成方法](../azure-monitor/logs/quick-create-workspace.md)について確認してください。

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>新しいロジック アプリの Log Analytics を有効にする

ロジック アプリを作成するときに、Log Analytics を有効にすることができます。

1. [Azure portal](https://portal.azure.com) 内のロジック アプリを作成するための情報を指定するウィンドウで、次の手順を実行します。

   1. **[Log Analytics]** で **[オン]** を選択します。

   1. **Log Analytics ワークスペース** の一覧から、ロジック アプリの実行からデータを送信するワークスペースを選択します。

      ![ロジック アプリに関する情報の入力](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      この手順を完了すると、Azure によりロジック アプリが作成されます。このアプリは、Log Analytics ワークスペースに関連付けられています。 この手順では、ワークスペースに Logic Apps 管理ソリューションが自動的にインストールされます。

1. 完了したら **[作成]** を選択します。

1. ロジック アプリを実行した後、ロジック アプリの実行状態を表示するには、[次の手順を続行します](#view-logic-app-runs)。

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Logic Apps 管理ソリューションをインストールする

ロジック アプリの作成時にすでに Log Analytics をオンにしている場合は、この手順をスキップしてください。 Log Analytics ワークスペースには、Logic Apps 管理ソリューションが既にインストールされています。

1. [Azure portal](https://portal.azure.com) の検索ボックスに「`log analytics workspaces`」と入力し、 **[Log Analytics ワークスペース]** を選択します。

   ![[Log Analytics ワークスペース] を選択する](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. **[Log Analytics ワークスペース]** でワークスペースを選択します。

   ![Log Analytics ワークスペースを選択する](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. **[概要]** ウィンドウで **[Log Analytics の使用を開始する]**  >  **[監視ソリューションの構成]** の順に選択し、 **[ソリューションの表示]** を選択します。

   ![[概要] ウィンドウで [ソリューションの表示] を選択する](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. **[概要]** で **[追加]** を選択します。

   ![[概要] ウィンドウで新しいソリューションを追加する](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. **[Marketplace]** が開いたら、検索ボックスに「`logic apps management`」と入力し、 **[Logic Apps 管理]** を選択します。

   ![Marketplace から [Logic Apps 管理] を選択する](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. [ソリューションの説明] ウィンドウで **[作成]** を選択します。

   ![[作成] を選択して [Logic Apps 管理] ソリューションを追加する](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. ソリューションをインストールする Log Analytics ワークスペースを確認して確定し、もう一度 **[作成]** を選択します。

   ![[Logic Apps 管理] の [作成] を選択する](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Log Analytics ワークスペースが含まれる Azure リソース グループに Azure によってソリューションがデプロイされると、ワークスペースの [概要] ウィンドウにソリューションが表示されます。

   ![ワークスペースの [概要] ウィンドウ](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor ログを設定する

ランタイム イベントおよびデータに関する情報を [Azure Monitor ログ](../azure-monitor/logs/data-platform-logs.md)に格納するとき、この情報の検索と確認に役立つ[ログ クエリ](../azure-monitor/logs/log-query-overview.md)を作成できます。

> [!NOTE]
> 診断設定を有効にした後、診断データが指定された宛先 (Log Analytics、イベント ハブ、ストレージ アカウントなど) のログに最大 30 分間送信されない場合があります。 この遅延は、この期間の診断データが存在しないため確認できない可能性があることを意味します。 完了したイベントと[追跡対象プロパティ](#extend-data)は、10 ～ 15 分間 Log Analytics ワークスペースに表示されない場合があります。

1. [Azure Portal](https://portal.azure.com) で、ご利用のロジック アプリを探して選択します。

1. ロジック アプリ メニューの **[監視]** で、 **[診断設定]**  >  **[診断設定を追加する]** を追加します。

   ![[監視] で、[診断設定] > [診断設定を追加する] を選択する](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. 設定を作成するには、次の手順を実行します。

   1. 設定の名前を入力します。

   1. **[Log Analytics への送信]** を選択します。

   1. **[サブスクリプション]** では、Log Analytics ワークスペースに関連付けられている Azure サブスクリプションを選択します。

   1. **[Log Analytics ワークスペース]** では、使用するワークスペースを選択します。

   1. **[ログ]** にある **[WorkflowRuntime]** カテゴリを選択します。これは、記録するイベント カテゴリを指定します。

   1. すべてのメトリックを選択するには、 **[メトリック]** で **[AllMetrics]** を選択します。

   1. 終了したら、 **[保存]** を選択します。

   次に例を示します。

   ![Log Analytics ワークスペースとログ対象のデータを選択する](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>ロジック アプリの実行状態を表示する

ロジック アプリが実行されると、それらの実行に関するデータを Log Analytics ワークスペースに表示できます。

1. [Azure portal](https://portal.azure.com) で、Log Analytics ワークスペースを探して開きます。

1. ワークスペースのメニューで、 **[ワークスペースの概要]**  >  **[Logic Apps 管理]** を選択します。

   ![ロジック アプリの実行状態と実行件数](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > 実行後すぐに [Logic Apps 管理] タイルに結果が表示されない場合は、 **[最新の情報に更新]** を選択するか、しばらく待ってから再試行してください。

   ここでは、ロジック アプリの実行は名前または実行状態でグループ化されます。 このページには、ロジック アプリの実行のアクションまたはトリガーで発生したエラーに関する詳細も表示されます。

   ![ロジック アプリの実行状態の概要](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. 特定のロジック アプリまたは状態のすべての実行を表示するには、そのロジック アプリまたは状態の行を選択します。

   特定のロジック アプリのすべての実行を表示する例は次の通りです。

   ![ロジック アプリの実行と状態を表示する](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   [追跡対象プロパティを設定した](#extend-data)アクションでは、 **[追跡対象プロパティ]** 列で **[表示]** を選択してプロパティを表示することもできます。 追跡対象プロパティを検索するには、列フィルターを使用します。

   ![ロジック アプリの追跡対象プロパティを表示する](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. 結果をフィルター処理するには、クライアント側とサーバー側の両方でフィルター処理を実行します。

   * **クライアント側のフィルター**:列ごとに次のように必要なフィルターを選択します。

     ![列フィルターの例](./media/monitor-logic-apps-log-analytics/filters.png)

   * **サーバー側のフィルター**:特定の時間枠を選択するか、表示される実行の数を制限するには、ページの上部にあるスコープ コントロールを使用します。 既定では、一度に 1000 件のレコードのみ表示されます。

     ![時間枠を変更する](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. 特定の実行のすべてのアクションとその詳細を表示するには、ロジック アプリの実行の行を選択します。

   特定のロジック アプリの実行のすべてアクションとトリガーを表示する例は次のとおりです。

   ![ロジック アプリの実行のアクションを表示する](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>診断データを Azure Storage と Azure Event Hubs に送信する

Azure Monitor ログと併せて、ロジック アプリの診断データを他の Azure サービスで使用する方法を次のように拡張できます。

* [Azure リソース ログをストレージ アカウントにアーカイブする](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Azure プラットフォーム ログを Azure Event Hubs にストリーミングする](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

これにより、[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) や [Power BI](../azure-monitor/visualize/powerbi.md) などの他のサービスのテレメトリと分析を使用したリアルタイム監視が可能になります。 次に例を示します。

* [Event Hubs からStream Analytics にデータをストリーミングする](../stream-analytics/stream-analytics-define-inputs.md)
* [ストリーミング データを Stream Analytics で分析し、Power BI でリアルタイム分析ダッシュボードを作成する](../stream-analytics/stream-analytics-power-bi-dashboard.md)

診断データを送信する場所に応じて、まず [Azure ストレージ アカウントを作成する](../storage/common/storage-account-create.md)か、[Azure イベント ハブを作成する](../event-hubs/event-hubs-create.md)ことを確認してください。 その後、データの送信先を選択できます。 保有期間は、ストレージ アカウントを使用する場合にのみ適用されます。

![データを Azure ストレージ アカウントまたはイベント ハブに送信する](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure Monitor の診断イベント

各診断イベントには、ご利用のロジック アプリとそのイベントに関する詳細 (状態、開始時刻、終了時刻など) が含まれています。 監視、追跡、ログをプログラムで設定する際に、この情報を [Azure Logic Apps 用 REST API](/rest/api/logic) と [Azure Monitor 用 REST API](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows) で使用できます。 また、次に示す `clientTrackingId` および `trackedProperties` プロパティを使用することもできます。 

* `clientTrackingId`:指定しなかった場合、自動的にこの ID が生成され、ロジック アプリの実行でイベント (このロジック アプリから呼び出される入れ子になったワークフローなど) どうしが関連付けられます。 この ID は、カスタム ID 値を指定した `x-ms-client-tracking-id` ヘッダーをトリガー要求で渡すことで、トリガー内で手動で指定できます。 要求トリガー、HTTP トリガー、または webhook トリガーを使用できます。

* `trackedProperties`:診断データの入力または出力を追跡するには、ロジック アプリ デザイナーを使用するか、またはロジック アプリの JSON 定義から直接、アクションに `trackedProperties` セクションを追加します。 追跡対象プロパティで追跡できるのは、1 つのアクションの入出力のみです。ただし、イベントの `correlation` プロパティを使用すると、1 回の実行に含まれる複数のアクションにわたってそれらを相互に関連付けることができます。 1 つ以上のプロパティを追跡するには、`trackedProperties` セクションと必要なプロパティをアクションの定義に追加します。

  次に示す例では、アクションの入力からの追跡対象プロパティが **[変数を初期化する]** アクションの定義に含まれています。このプロパティには、レコードではなく配列を入力します。

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  次の例では、複数の追跡対象プロパティを示します。

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

次の例では、`ActionCompleted` イベントに `clientTrackingId` と `trackedProperties` 属性がどのように含まれるかを示します。

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
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
         "workflowId": "<logic-app-workflow-ID>",
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
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>次のステップ

* [監視および追跡クエリを作成する](../logic-apps/create-monitoring-tracking-queries.md)
* [Azure Monitor ログで B2B メッセージを監視する](../logic-apps/monitor-b2b-messages-log-analytics.md)
