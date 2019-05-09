---
title: チュートリアル:コードを 1 行も書かずに Azure Data Explorer で診断とアクティビティのログ データを取り込む
description: このチュートリアルでは、コードを 1 行も書かずに Azure Data Explorer にデータを取り込み、そのデータのクエリを実行する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 04/07/2019
ms.openlocfilehash: 9f4b7ee0dcc87ca03fd051be0dacedf0912b5320
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262909"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>チュートリアル:コードを 1 行も書かずに Azure Data Explorer にデータを取り込む

このチュートリアルでは、コードを書くことなく、診断ログとアクティビティ ログから Azure Data Explorer クラスターにデータを取り込む方法について説明します。 この簡単な取り込み方法を使用すると、データ分析のために Azure Data Explorer のクエリをすぐに開始できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Data Explorer のデータベースに、テーブルと取り込みのマッピングを作成します。
> * 更新ポリシーを使用して、取り込まれたデータの形式を設定します。
> * [イベント ハブ](/azure/event-hubs/event-hubs-about)を作成し、それを Azure Data Explorer に接続します。
> * [Azure Monitor 診断ログ](/azure/azure-monitor/platform/diagnostic-logs-overview)と [Azure Monitor アクティビティ ログ](/azure/azure-monitor/platform/activity-logs-overview)からイベント ハブにデータをストリーム配信します。
> * Azure Data Explorer を使用して、取り込まれたデータのクエリを実行します。

> [!NOTE]
> 同じ Azure の場所またはリージョンに、すべてのリソースを作成します。 これは、Azure Monitor 診断ログの要件です。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)。 このチュートリアルでのデータベース名は *TestDatabase* です。

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Azure Monitor データ プロバイダー: 診断ログとアクティビティ ログ

以下の Azure Monitor の診断ログとアクティビティ ログによって提供されたデータを表示して理解します。 これらのデータ スキーマに基づいて、取り込みパイプラインを作成します。 ログの各イベントにはレコードの配列が含まれます。 このチュートリアルの後半では、このレコードの配列が分割されます。

### <a name="diagnostic-logs-example"></a>診断ログの例

Azure 診断ログは、Azure サービスによって生成され、そのサービスの動作に関するデータを提供するメトリックです。 データは、1 分の時間グレインで集計されます。 クエリ期間についての Azure Data Explorer メトリックイベント スキーマの例を次に示します。

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

### <a name="activity-logs-example"></a>アクティビティ ログの例

Azure アクティビティ ログは、サブスクリプションのリソースに対して実行された操作を分析できるサブスクリプション レベルのログです。 アクセスを確認するためのアクティビティログ イベントの例を次に示します。

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Azure Data Explorer で取り込みパイプラインを設定する

Azure Data Explorer のパイプラインの設定には、[テーブルの作成やデータの取り込み](/azure/data-explorer/ingest-sample-data#ingest-data)など、いくつかの手順が含まれています。 データの操作、マップ、更新を行うこともできます。

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Azure Data Explorer の Web UI に接続する

Azure Data Explorer の *TestDatabase* データベースで **[クエリ]** を選択して、Azure Data Explorer の Web UI を開きます。

![[クエリ] ページ](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>ターゲット テーブルを作成する

Azure Monitor ログの構造は表形式ではありません。 データを操作し、各イベントを 1 つまたは複数のレコードに展開します。 生データは、アクティビティ ログの場合は *ActivityLogsRawRecords*、診断ログの場合は *DiagnosticLogsRawRecords* という中間テーブルに取り込まれます。 その時点で、データを操作および展開します。 更新ポリシーを使用して、展開されたデータはアクティビティ ログの場合は *ActivityLogsRecords* テーブル、診断ログの場合は *DiagnosticLogsRecords* に取り込まれます。 つまり、アクティビティ ログを取り込むために 2 つの異なるテーブルを作成し、診断ログを取り込むために 2 つの異なるテーブルを作成する必要があります。

Azure Data Explorer の Web UI を使用して、Azure Data Explorer データベースにターゲット テーブルを作成します。

#### <a name="the-diagnostic-logs-table"></a>診断ログ テーブル

1. *TestDatabase* データベースで、診断ログ レコードを格納する *DiagnosticLogsRecords* という名前のテーブルを作成します。 次の `.create table` 管理コマンドを使用します。

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. **[実行]** を選択してテーブルを作成します。

    ![Run query](media/ingest-data-no-code/run-query.png)

1. 次のクエリを使用して、データ操作用の *DiagnosticLogsRawRecords* という名前の中間データ テーブルを *TestDatabase* データベース内に作成します。 **[実行]** を選択してテーブルを作成します。

    ```kusto
    .create table DiagnosticLogsRawRecords (Records:dynamic)
    ```

#### <a name="the-activity-logs-tables"></a>アクティビティ ログ テーブル

1. アクティビティ ログ レコードを受け取る *ActivityLogsRecords* という名前のテーブルを、*TestDatabase* データベース内に作成します。 テーブルを作成するには、次の Azure Data Explorer クエリを実行します。

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. データ操作用の *ActivityLogsRawRecords* という名前の中間データ テーブルを *TestDatabase* データベース内に作成します。

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>テーブル マッピングを作成する

 データ形式が `json` であるため、データのマッピングが必須です。 `json` のマッピングでは、json の各パスがテーブルの列名にマップされます。

#### <a name="table-mapping-for-diagnostic-logs"></a>診断ログのテーブル マッピング

診断ログのデータをテーブルにマップするには、次のクエリを使用します。

```kusto
.create table DiagnosticLogsRawRecords ingestion json mapping 'DiagnosticLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>アクティビティ ログのテーブル マッピング

アクティビティ ログのデータをテーブルにマップするには、次のクエリを使用します。

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-log-data"></a>ログ データの更新ポリシーを作成する

#### <a name="activity-log-data-update-policy"></a>アクティビティ ログ データによるポリシーの更新

1. コレクション内の各値が個別の行を受け取るようにアクティビティ ログ レコードのコレクションを展開する[関数](/azure/kusto/management/functions)を作成します。 [`mv-expand`](/azure/kusto/query/mvexpandoperator) 演算子を使用します。

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. [更新ポリシー](/azure/kusto/concepts/updatepolicy)をターゲット テーブルに追加します。 このポリシーでは、*ActivityLogsRawRecords* 中間データ テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、*ActivityLogsRecords* テーブルにその結果が取り込まれます。

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

#### <a name="diagnostic-log-data-update-policy"></a>診断ログ データによるポリシーの更新

1. コレクション内の各値が個別の行を受け取るように診断ログ レコードのコレクションを展開する[関数](/azure/kusto/management/functions)を作成します。 [`mv-expand`](/azure/kusto/query/mvexpandoperator) 演算子を使用します。
     ```kusto
    .create function DiagnosticLogRecordsExpand() {
        DiagnosticLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            MetricName = tostring(events["metricName"]),
            Count = toint(events["count"]),
            Total = todouble(events["total"]),
            Minimum = todouble(events["minimum"]),
            Maximum = todouble(events["maximum"]),
            Average = todouble(events["average"]),
            TimeGrain = tostring(events["timeGrain"])
    }
    ```

2. [更新ポリシー](/azure/kusto/concepts/updatepolicy)をターゲット テーブルに追加します。 このポリシーでは、*DiagnosticLogsRawRecords* 中間データ テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、*DiagnosticLogsRecords* テーブルにその結果が取り込まれます。

    ```kusto
    .alter table DiagnosticLogsRecords policy update @'[{"Source": "DiagnosticLogsRawRecords", "Query": "DiagnosticLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs 名前空間を作成する

Azure 診断ログでは、ストレージ アカウントまたはイベント ハブにメトリックをエクスポートできます。 このチュートリアルでは、イベント ハブ経由でメトリックをルーティングします。 Event Hubs 名前空間と、診断ログ用のイベント ハブを、次の手順で作成します。 Azure Monitor では、アクティビティ ログ用にイベント ハブ *insights-operational-logs* が作成されます。

1. Azure portal で Azure Resource Manager テンプレートを使用して、イベント ハブを作成します。 この記事の残りの手順を実行するには、**[Azure へのデプロイ]** ボタンを右クリックして、**[新しいウィンドウで開く]** を選択します。 **[Azure へのデプロイ]** ボタンをクリックすると、Azure portal に移動します。

    [![[Azure へのデプロイ] ボタン](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Event Hubs 名前空間と、診断ログ用のイベント ハブを作成します。

    ![イベント ハブの作成](media/ingest-data-no-code/event-hub.png)

1. フォームに次の情報を入力します。 次の表に記載されていない設定については、既定値を使用してください。

    **設定** | **推奨値** | **説明**
    |---|---|---|
    | **サブスクリプション** | *該当するサブスクリプション* | イベント ハブに使用する Azure サブスクリプションを選択します。|
    | **リソース グループ** | *test-resource-group* | 新しいリソース グループを作成します。 |
    | **場所** | ニーズに最も適したリージョンを選択します。 | 他のリソースと同じ場所に、Event Hubs 名前空間を作成します。
    | **名前空間名** | *AzureMonitoringData* | 名前空間を識別する一意の名前を選択します。
    | **イベント ハブ名** | *DiagnosticLogsData* | イベント ハブは、固有のスコープ コンテナーを提供する名前空間以下にあります。 |
    | **コンシューマー グループ名** | *adxpipeline* | コンシューマー グループ名を作成します。 コンシューマー グループを使用すると、複数の使用アプリケーションがそれぞれイベント ストリーム ビューを持つことができるようになります。 |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Azure Monitor ログをイベント ハブに接続する

次に、診断ログとアクティビティ ログをイベント ハブに接続する必要があります。

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>診断ログをイベント ハブに接続する

メトリックをエクスポートするリソースを選択します。 診断ログのエクスポートは、Event Hubs 名前空間、Azure Key Vault、Azure IoT Hub、Azure Data Explorer クラスターなど、複数のリソースの種類でサポートされます。 このチュートリアルでは、Azure Data Explorer クラスターをリソースとして使用します。

1. Azure portal でお使いの Kusto クラスターを選択します。
1. **[診断設定]** を選択してから、**[診断をオンにする]** リンクを選択します。 

    ![診断設定](media/ingest-data-no-code/diagnostic-settings.png)

1. **[診断設定]** ウィンドウが開きます。 次の手順を実行します。
   1. 診断ログ データに *ADXExportedData* という名前を付けます。
   1. **[メトリック]** で、**[AllMetrics]** チェック ボックスをオンにします (省略可能)。
   1. **[イベント ハブへのストリーム]** チェック ボックスをオンにします。
   1. **[構成]** をクリックします。

      ![[診断設定] ウィンドウ](media/ingest-data-no-code/diagnostic-settings-window.png)

1. **[イベント ハブの選択]** ウィンドウで、自分が作成したイベント ハブに診断ログからデータをエクスポートする方法を構成します。
    1. **[イベント ハブの名前空間の選択]** リストで、*AzureMonitoringData* を選択します。
    1. **[イベント ハブ名を選択する]** リストで、*diagnosticlogsdata* を選択します。
    1. **[イベント ハブ ポリシー名の選択]** リストで、**RootManagerSharedAccessKey** を選択します。
    1. **[OK]** を選択します。

1. **[保存]** を選択します。

### <a name="connect-activity-logs-to-your-event-hub"></a>アクティビティ ログをイベント ハブに接続する

1. Azure portal の左側のメニューで、**[アクティビティ ログ]** を選択します。
1. **[アクティビティ ログ]** ウィンドウが開きます。 **[イベント ハブにエクスポート]** を選択します。

    ![[アクティビティ ログ] ウィンドウ](media/ingest-data-no-code/activity-log.png)

1. **[アクティビティ ログのエクスポート]** ウィンドウが開きます。
 
    ![[アクティビティ ログのエクスポート] ウィンドウ](media/ingest-data-no-code/export-activity-log.png)

1. **[アクティビティ ログのエクスポート]** ウィンドウで、次の手順を実行します。
      1. サブスクリプションを選択します。
      1. **[リージョン]** リストで、**[すべて選択]** を選択します。
      1. **[イベント ハブにエクスポート]** チェック ボックスをオンにします。
      1. **[Service Bus 名前空間を選択してください。]** を選択して、**[イベント ハブの選択]** ウィンドウを開きます。
      1. **[イベント ハブの選択]** ウィンドウで、ご自分のサブスクリプションを選択します。
      1. **[イベント ハブの名前空間の選択]** リストで、*AzureMonitoringData* を選択します。
      1. **[イベント ハブ ポリシー名の選択]** リストで、既定のイベント ハブ ポリシー名を選択します。
      1. **[OK]** を選択します。
      1. ウィンドウの左上隅にある **[保存]** を選択します。
   *insights-operational-logs* という名前のイベント ハブが作成されます。

### <a name="see-data-flowing-to-your-event-hubs"></a>イベント ハブに送られてくるデータを確認する

1. 接続が定義され、イベント ハブへのアクティビティログのエクスポートが完了するまで、数分待ちます。 ご自分の Event Hubs 名前空間に移動して、自分が作成したイベント ハブを確認します。

    ![作成されたイベント ハブ](media/ingest-data-no-code/event-hubs-created.png)

1. イベント ハブに送られてくるデータを確認します。

    ![イベント ハブのデータ](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>イベント ハブを Azure Data Explorer に接続する

次に、診断ログとアクティビティ ログのデータ接続を作成する必要があります。

### <a name="create-the-data-connection-for-diagnostic-logs"></a>診断ログのデータ接続を作成する

1. *kustodocs* という名前の Azure Data Explorer クラスターで、左側のメニューの **[データベース]** を選択します。
1. **[データベース]** ウィンドウで、ご自分の *TestDatabase* データベースを選択します。
1. 左側のメニューで、**[データ インジェスト]** を選択します。
1. **[データ インジェスト]** ウィンドウで、**[+ データ接続の追加]** をクリックします。
1. **[データ接続]** ウィンドウで、次の情報を入力します。

    ![イベント ハブのデータ接続](media/ingest-data-no-code/event-hub-data-connection.png)

    データ ソース:

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | **データ接続名** | *DiagnosticsLogsConnection* | Azure データ エクスプローラーで作成する接続の名前。|
    | **イベント ハブの名前空間** | *AzureMonitoringData* | 以前に選択した、名前空間を識別する名前。 |
    | **イベント ハブ** | *diagnosticlogsdata* | 作成したイベント ハブ。 |
    | **コンシューマー グループ** | *adxpipeline* | 作成したイベント ハブに定義されているコンシューマー グループ。 |
    | | |

    ターゲット テーブル: 

    ルーティングには、"*静的*" と "*動的*" という 2 つのオプションがあります。 このチュートリアルでは、静的ルーティング (既定) を使用し、テーブル名、データ形式、およびマッピングを指定します。 **[My data includes routing info]\(データにルーティング情報が含まれている\)** をオフのままにしておきます。

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | **テーブル** | *DiagnosticLogsRawRecords* | 自分が *TestDatabase* データベースに作成したテーブル。 |
    | **データ形式** | *JSON* | テーブルで使用される形式。 |
    | **列マッピング** | *DiagnosticLogsRecordsMapping* | 自分が *TestDatabase* データベースに作成したマッピング。これにより、受信 JSON データが *DiagnosticLogsRecords* の列名とデータ型にマッピングされます。|
    | | |

1. **作成** を選択します。  

### <a name="create-the-data-connection-for-activity-logs"></a>アクティビティ ログのデータ接続を作成する

「診断ログのデータ接続を作成する」セクションの手順を繰り返して、アクティビティ ログのデータ接続を作成します。

1. **[データ接続]** ウィンドウで、次の設定を使用します。

    データ ソース:

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | **データ接続名** | *ActivityLogsConnection* | Azure データ エクスプローラーで作成する接続の名前。|
    | **イベント ハブの名前空間** | *AzureMonitoringData* | 以前に選択した、名前空間を識別する名前。 |
    | **イベント ハブ** | *insights-operational-logs* | 作成したイベント ハブ。 |
    | **コンシューマー グループ** | *$Default* | 既定のコンシューマー グループ。 必要な場合は、異なるコンシューマー グループを作成できます。 |
    | | |

    ターゲット テーブル: 

    ルーティングには、"*静的*" と "*動的*" という 2 つのオプションがあります。 このチュートリアルでは、静的ルーティング (既定) を使用し、テーブル名、データ形式、およびマッピングを指定します。 **[My data includes routing info]\(データにルーティング情報が含まれている\)** をオフのままにしておきます。

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | **テーブル** | *ActivityLogsRawRecords* | 自分が *TestDatabase* データベースに作成したテーブル。 |
    | **データ形式** | *JSON* | テーブルで使用される形式。 |
    | **列マッピング** | *ActivityLogsRawRecordsMapping* | 自分が *TestDatabase* データベースに作成したマッピング。これにより、受信 JSON データが *ActivityLogsRawRecords* の列名とデータ型にマッピングされます。|
    | | |

1. **作成** を選択します。  

## <a name="query-the-new-tables"></a>新しいテーブルのクエリを実行する

パイプラインをデータが流れるようになりました。 クラスター経由での取り込みには既定で 5 分かかるので、データが流れるのを数分待ってから、クエリを始めます。

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>診断ログ テーブルのクエリの例

次のクエリでは、Azure Data Explorer の診断ログ レコードからクエリ期間のデータを分析します。

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

クエリの結果:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="an-example-of-querying-the-activity-logs-table"></a>アクティビティ ログ テーブルのクエリの例

次のクエリでは、Azure Data Explorer のアクティビティ ログ レコードのデータを分析します。

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

クエリの結果:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>次の手順

次の記事を使用して、Azure Data Explorer から抽出したデータに対するさらに多くのクエリを記述する方法を学習します。

> [!div class="nextstepaction"]
> [Azure Data Explorer のクエリを記述する](write-queries.md)
