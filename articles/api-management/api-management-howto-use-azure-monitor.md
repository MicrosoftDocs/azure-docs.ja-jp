---
title: チュートリアル - Azure API Management で発行された API を監視する | Microsoft Docs
description: このチュートリアルの手順に従い、メトリック、アラート、アクティビティ ログ、リソース ログを使用して Azure API Management で API を監視する方法を学びます。
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 1cb902c4b59193c46dbeca47bb355f0695a0f2c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572792"
---
# <a name="tutorial-monitor-published-apis"></a>チュートリアル:発行された API を監視する

Azure Monitor を使用すると、Azure API Management サービスのメトリックまたはログを視覚化、クエリ、ルーティング、アーカイブし、メトリックとログに対してアクションを実行できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * API のメトリックを表示する 
> * アラート ルールを設定する 
> * アクティビティ ログを表示する
> * リソース ログを有効にして表示する

API Management の組み込みの[分析](howto-use-analytics.md)を使用して、API シリーズの使用状況とパフォーマンスを監視することもできます。

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>API のメトリックを表示する

API Management は[メトリック](../azure-monitor/essentials/data-platform-metrics.md)を 1 分ごとに出力するので、API の状態と正常性をほぼリアルタイムで把握できます。 最も頻繁に使用される 2 つのメトリックを次に示します。 使用可能なすべてのメトリックの一覧については、[サポートされているメトリック](../azure-monitor/essentials/metrics-supported.md#microsoftapimanagementservice)に関する記事をご覧ください。

* **容量** - APIM サービスのアップグレードとダウングレードについて判断する際に役立ちます。 このメトリックは 1 分ごとに出力され、報告時のゲートウェイの容量を反映しています。 メトリックの範囲は 0 から 100 で、CPU やメモリの使用率などのゲートウェイ リソースに基づいて計算されます。
* **要求** - 対象の API Management サービスを通過する API トラフィックを分析する際に役立ちます。 メトリックは 1 分ごとに出力され、応答コード、場所、ホスト名、エラーなどのディメンションと共にゲートウェイ要求の数を報告します。 

> [!IMPORTANT]
> 次のメトリックは 2019 年 5 月の時点で非推奨であり、2023 年 8 月に廃止される予定です: ゲートウェイ要求の合計、成功したゲートウェイ要求、未承認ゲートウェイ要求、失敗したゲートウェイ要求、その他のゲートウェイ要求。 同等の機能を提供する要求メトリックに移行してください。

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="API Management の [概要] のメトリックのスクリーンショット":::

メトリックにアクセスするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。 **[概要]** ページで、API の主要メトリックを確認します。
1. メトリックを詳しく調べるには、ページの下部付近にあるメニューから **[メトリック]** を選択します。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="[監視] メニューの [メトリック] 項目のスクリーンショット":::

1. ドロップダウン リストで、関心のあるメトリックを選択します。 **Requests** など。 
1. グラフには、API 呼び出しの合計数が表示されます。
1. **[Requests]\(要求数\)** メトリックのディメンションを使用して、グラフをフィルター処理できます。 たとえば、 **[フィルターの追加]** を選択し、 **[Backend Response Code Category]\(バックエンド応答コード カテゴリ\)** を選択して、値として「500」を入力します。 これで、API バックエンドで失敗した要求の数がグラフに表示されるようになりました。   

## <a name="set-up-an-alert-rule"></a>アラート ルールを設定する 

メトリックとアクティビティ ログに基づいて、[アラート](../azure-monitor/alerts/alerts-metric-overview.md)を受け取ることができます。 Azure Monitor では、アラートがトリガーされたときに次の処理を実行するように[アラートを構成](../azure-monitor/alerts/alerts-metric.md)できます。

* 電子メール通知を送信する
* Webhook を呼び出す
* Azure Logic App を呼び出す

要求メトリックに基づいてサンプル アラート ルールを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. ページの下部にあるメニュー バーの **[アラート]** を選択します。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="[監視] メニューの [アラート] オプションのスクリーンショット":::

1. **[+ 新しいアラート ルール]** を選択します。
1. **[アラート ルールの作成]** ウィンドウで、 **[条件の選択]** を選択します。
1. **[シグナル ロジックの構成]** ウィンドウで、次の操作を行います。
    1. **[シグナルの種類]** で **[メトリック]** を選択します。
    1. **[シグナル名]** で **[要求]** を選択します。
    1. **[Split by dimensions]\(ディメンションによる分割\)** の **[ディメンション名]** で、 **[Gateway Response Code Category]\(ゲートウェイ応答コード カテゴリ\)** を選択します。
    1. **[ディメンション値]** で、承認されていない要求や無効な要求などのクライアント エラーに対応する、 **[4xx]** を選択します。
    1. **[アラート ロジック]** で、アラートをトリガーする際のしきい値を指定し、 **[完了]** を選択します。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="[シグナル ロジックの構成] ウィンドウのスクリーンショット":::

1. 既存のアクション グループを選択するか、新しいアクション グループを作成します。 次の例では、新しいアクション グループを作成しています。 通知メールは、admin@contoso.com に送信されます。 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="新しいアクション グループの通知のスクリーンショット":::

1. アラート ルールの名前と説明を入力し、重大度を選択します。 
1. **[アラート ルールの作成]** を選択します。
1. 次に、API キーなしで Conference API を呼び出してアラート ルールをテストします。 次に例を示します。

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    評価期間に基づいてアラートがトリガーされ、admin@contoso.com に電子メールが送信されます。 

    アラートは、API Management インスタンスの **[アラート]** ページにも表示されます。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="ポータルのアラートのスクリーンショット":::

## <a name="activity-logs"></a>アクティビティ ログ

アクティビティ ログは、API Management サービスで実行された操作に関する情報を提供します。 アクティビティ ログを使用すると、API Management サービスで発生した書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。

> [!NOTE]
> アクティビティ ログには、読み取り (GET) 操作、Azure Portal で実行された操作、または元の管理 API の使用に関する情報は含まれません。

アクティビティ ログには API Management サービスでアクセスするか、Azure Monitor ですべての Azure リソースのログにアクセスできます。 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="ポータルのアクティビティ ログのスクリーンショット":::

アクティビティ ログを表示するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。

1. **[アクティビティ ログ]** を選択します。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="[監視] メニューの [アクティビティ ログ] 項目のスクリーンショット":::
1. 目的のフィルター処理の範囲を選択し、 **[適用]** を選択します。

## <a name="resource-logs"></a>リソース ログ

リソース ログは、監査とトラブルシューティングを行うために重要な、操作とエラーについての豊富な情報を提供します。 リソース ログは、アクティビティ ログとは異なります。 アクティビティ ログは、Azure リソースで実行された操作に関する分析情報を提供します。 リソース ログは、自分のリソースが実行した操作に関する分析情報を提供します。

リソース ログを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
2. **[診断設定]** を選択します。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="[監視] メニューの [診断設定] 項目のスクリーンショット":::

1. **[+ 診断設定の追加]** を選択します。
1. 収集するログまたはメトリックを選択します。

   リソース ログをメトリックと共にストレージ アカウントにアーカイブし、それらをイベント ハブにストリーム配信したり、Log Analytics ワークスペースに送信したりできます。 

詳細については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。

## <a name="view-diagnostic-data-in-azure-monitor"></a>Azure Monitor で診断データを表示する

Log Analytics ワークスペースで GatewayLogs またはメトリックの収集を有効にした場合、データが Azure Monitor に表示されるまでに数分かかることがあります。 データを表示するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. ページの下部付近にあるメニューから **[ログ]** を選択します。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="[監視] メニューの [ログ] 項目のスクリーンショット":::

クエリを実行してデータを表示します。 [サンプル クエリ](../azure-monitor/logs/example-queries.md)がいくつか用意されています。自分で用意したものを実行してもかまいません。 たとえば、次のクエリでは、GatewayLogs テーブルから直近の 24 時間のデータを取得します。

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

API Management にリソース ログを使用する方法の詳細については、以下を参照してください。

* [Azure Monitor Log Analytics の使用を開始する](../azure-monitor/logs/log-analytics-tutorial.md)か、[Log Analytics のデモ環境](https://portal.loganalytics.io/demo)を試します。

* [Azure Monitor のログ クエリの概要](../azure-monitor/logs/log-query-overview.md)。

次の JSON は、API 要求を成功させるための GatewayLogs のサンプル エントリを示しています。 詳細については、[スキーマ リファレンス](gateway-log-schema-reference.md)を参照してください。 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * API のメトリックを表示する
> * アラート ルールを設定する 
> * アクティビティ ログを表示する
> * リソース ログを有効にして表示する


次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [呼び出しをトレースする](api-management-howto-api-inspector.md)