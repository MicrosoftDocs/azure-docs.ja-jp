---
title: Azure Data Lake Analytics で利用でき、閲覧できる診断ログ
description: Azure Data Lake Analytics の診断ログの設定方法およびアクセス方法の解釈
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: cd339729f2300ff7e13e7422bf73373b4ce4658e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221011"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics の診断ログへのアクセス

診断ログの記録では、データ アクセスの監査証跡を収集することができます。 診断ログからは、以下のような情報が得られます。

* データにアクセスしたユーザーの一覧。
* データがアクセスされる頻度。
* アカウントで保存されているデータの量。

## <a name="enable-logging"></a>ログの有効化

1. [Azure Portal](https://portal.azure.com) にサインオンします。

2. Data Lake Analytics アカウントを開き、 __[監視]__ セクションから **[診断ログ]** を選択します。 次に、 __[診断を有効にする]__ を選択します。

    ![[診断ログ] アクションが選択され、[Turn on diagnostics to collect the following logs]\(診断をオンにして次のログを収集する\) が強調表示されていることを示すスクリーンショット。](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. __[診断設定]__ で、このログ構成の __名前__ を入力し、ログ オプションを選択します。

    ![診断を有効にして、監査ログと要求ログを収集する](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Traffic Manager で診断ログを有効にする")

   * 3 種類の方法でデータを格納および処理することができます。

     * __[Archive to a storage account]__ (ストレージ アカウントへのアーカイブ) を選択して、Azure ストレージ アカウントにログを保存します。 データをアーカイブする場合には、このオプションを使用します。 このオプションを選択する場合は、ログの保存先の Azure ストレージ アカウントを指定する必要があります。

     * **[Stream to an event hub]** (イベント ハブへのストリーム) オプションを選択して、Azure Event Hub にログ データをストリーミングします。 リアルタイムで受信したログを分析するためのダウン ストリーム処理パイプラインがある場合には、このオプションを使用します。 このオプションを選択する場合、使用する Azure Event Hub の詳細を指定する必要があります。

     * __[Send to Log Analytics]\(Log Analytics に送信\)__ を選択して、Azure Monitor サービスにデータを送信します。 Azure Monitor ログを使ってログを収集し分析する場合は、このオプションを使用します。
   * 監査ログ、要求ログ、またはその両方のいずれを取得するかを指定します。  要求ログは、すべての API 要求をキャプチャします。 監査ログは、その API 要求によってトリガーされるすべての操作を記録します。

   * __[ストレージ アカウントへのアーカイブ]__ で、データを保持する日数を指定します。

   * [__保存__] をクリックします。

        > [!NOTE]
        > __[保存]__ ボタンをクリックする前に、 __[Archive to a storage account]__ (ストレージ アカウントへのアーカイブ)、 __[Stream to an Event Hub]__ (イベント ハブへのストリーム)、または __[Send to Log Analytics]__ (Log Analytics に送信) のいずれかを選択する必要があります。

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>ログ データを含む Azure ストレージ アカウントを使用する

1. ログ データを保持する BLOB コンテナーを表示するには、ログ記録用の Data Lake Analytics に使用する Azure Storage アカウントを開き、 __[BLOB]__ をクリックします。

   * コンテナー **insights-logs-audit** には、監査ログが含まれます。
   * コンテナー **insights-logs-requests** には、要求ログが含まれます。

2. これらのコンテナー内で、ログは次のファイル構造の下に格納されます。

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > パスのエントリの `##` の部分には、ログを作成した年、月、日、時間の情報が入ります。 Data Lake Analytics では、ログのファイルを 1 時間に 1 つ作成します。このため、`m=` の値は常に `00` となります。

    たとえば、監査ログへの完全パスは以下のようになります。

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    同様に、要求ログへの完全パスは以下のようになります。

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>ログの構造

監査ログと要求ログは構造化された JSON 形式で作成されます。

### <a name="request-logs"></a>要求ログ

JSON 形式の要求ログのエントリの例を次に示します。 各 BLOB には、ログ オブジェクトの配列を含む、 **レコード** と呼ばれるルート オブジェクトが 1 つあります。

```json
{
"records":
  [
    . . . .
    ,
    {
         "time": "2016-07-07T21:02:53.456Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>要求ログのスキーマ

| 名前 | Type | 説明 |
| --- | --- | --- |
| time |String |ログのタイムスタンプ (UTC) |
| resourceId |String |操作が行われたリソースの ID |
| category |String |ログのカテゴリ。 **Requests** など。 |
| operationName |String |ログに記録される操作の名前。 GetAggregatedJobHistory など。 |
| resultType |String |操作の状態。200 など。 |
| callerIpAddress |String |要求を行うクライアントの IP アドレス |
| correlationId |String |ロールの ID この値は、関係のあるログ エントリをグループ化する際に使用します |
| identity |Object |ログを生成した ID |
| properties |JSON |詳しくは、次のセクション (「要求ログのプロパティのスキーマ」) をご覧ください |

#### <a name="request-log-properties-schema"></a>要求ログのプロパティのスキーマ

| 名前 | Type | 説明 |
| --- | --- | --- |
| HttpMethod |String |操作に使用される HTTP メソッド。 GET など。 |
| Path |String |操作が実行されたパス |
| RequestContentLength |INT |HTTP 要求のコンテンツの長さ |
| ClientRequestId |String |この要求を一意に識別する ID |
| StartTime |String |サーバーが要求を受信した時刻 |
| EndTime |String |サーバーが応答を送信した時間 |

### <a name="audit-logs"></a>監査ログ

JSON 形式の監査ログのエントリの例を次に示します。 各 BLOB には、ログ オブジェクトの配列を含む、 **レコード** と呼ばれるルート オブジェクトが 1 つあります。

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>監査ログのスキーマ

| 名前 | Type | 説明 |
| --- | --- | --- |
| time |String |ログのタイムスタンプ (UTC) |
| resourceId |String |操作が行われたリソースの ID |
| category |String |ログのカテゴリ。 **Audit** など。 |
| operationName |String |ログに記録される操作の名前。 JobSubmitted など。 |
| resultType |String |ジョブの状態 (operationName) の副状態。 |
| resultSignature |String |ジョブの状態 (operationName) に関する追加の詳細。 |
| identity |String |操作を要求したユーザー。 たとえば、「 susan@contoso.com 」のように入力します。 |
| properties |JSON |詳しくは、次のセクション (「監査ログのプロパティのスキーマ」) をご覧ください |

> [!NOTE]
> **resultType** と **resultSignature** は、操作の結果に関する情報を示すものであり、操作が完了した場合にのみ値が入ります。 この 2 つには、たとえば **operationName** の値が **JobStarted** または **JobEnded** の場合にのみ値が入ります。
>
>

#### <a name="audit-log-properties-schema"></a>監査ログのプロパティのスキーマ

| 名前 | Type | 説明 |
| --- | --- | --- |
| JobId |String |ジョブに割り当てられた ID |
| JobName |String |ジョブに与えられている名前 |
| JobRunTime |String |ジョブの処理に使用するランタイム |
| SubmitTime |String |ジョブが送信された時間 (UTC) |
| StartTime |String |ジョブが送信された後に実行を開始した時刻 (UTC) |
| EndTime |String |ジョブが終了した時刻 |
| Parallelism |String |このジョブの送信中にこのジョブについて要求された Data Lake Analytics ユニットの数 |

> [!NOTE]
> **SubmitTime**、**StartTime**、**EndTime**、**Parallelism** は操作に関する情報を提供します。 これらのエントリには、その操作が開始または完了した場合にのみ値が含まれます。 たとえば、**SubmitTime** であれば、**operationName** の値が **JobSubmitted** になった後にのみ値が入ります。

## <a name="process-the-log-data"></a>ログ データの処理

Azure Data Lake Analytics では、ログ データの処理と分析方法に関するサンプルを提供しています。 [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) でサンプルを見つけることができます。

## <a name="next-steps"></a>次のステップ

[Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
