---
title: "Azure Data Lake Analytics の診断ログの表示 | Microsoft Docs"
description: "Azure Data Lake Analytics の診断ログの設定方法およびアクセス方法を学びます  "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/10/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: fb2fe0efe00a7ef7fd1c22ca94c76b2d5f4c5510
ms.openlocfilehash: 9366bce634fd3855080d267c797e2fecb8ab4ce9


---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics の診断ログへのアクセス

ここでは、Data Lake Analytics アカウントの診断ログを有効にする方法と、アカウント用に収集されたログを表示する方法について説明します。

組織が Azure Data Lake Analytics アカウントの診断ログを有効にすると、データ アクセスの監査証跡を収集できます。 診断ログからは、以下のような情報が得られます。

* データにアクセスしたユーザーの一覧。
* データがアクセスされる頻度。
* アカウントで保存されているデータの量。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure サブスクリプションを有効にします** 。 [手順](data-lake-analytics-get-started-portal.md)を参照してください。

* **Azure Data Lake Analytics アカウント**。 「 [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」の手順に従ってください。

## <a name="enable-logging"></a>ログの有効化

1. 新しい [Azure ポータル](https://portal.azure.com)にサインオンします。

2. Data Lake Analytics アカウントを開き、__[監視] セクション__から **[診断ログ]** を選択してください。 次に、__[診断を有効にする]__ を選択します。

    ![診断を有効にして、監査ログと要求ログを収集する](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. __[診断設定]__ で、状態を __[オン]__ にして、ログをとるオプションを選択します。

    ![診断を有効にして、監査ログと要求ログを収集する](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "診断ログを有効にする")

   * **[状態]** を **[オン]** に設定して診断ログを有効にします。

   * 2 種類の方法でデータを格納および処理することができます。

     * __[Archive to a storage account]__ (ストレージ アカウントへのアーカイブ) を選択して、Azure ストレージ アカウントにログを保存します。 データをアーカイブする場合には、このオプションを使用します。 このオプションを選択する場合は、ログの保存先の Azure ストレージ アカウントを指定する必要があります。

     * **[Stream to an event hub]** (イベント ハブへのストリーム) オプションを選択して、Azure イベント ハブにログ データをストリーミングします。 リアルタイムで受信したログを分析するためのダウン ストリーム処理パイプラインがある場合には、このオプションを使用します。 このオプションを選択する場合、使用する Azure Event Hub の詳細を指定する必要があります。

     * __[Send to Log Analytics]__ (Log Analytics に送信) を選択して、Log Analytics サービスにデータを送信します。 Log Analytics を使って分析ログを収集する場合は、この機能を使用します。
   * 監査ログ、要求ログ、またはその両方のいずれを取得するかを指定します。

   * データを保持する日数を指定します。

   * **[保存]**をクリックします。

        > [!NOTE]
        > __[保存]__ ボタンを使用する前に、__[Archive to a storage account]__(ストレージ アカウントへのアーカイブ)、__[Stream to an Event Hub]__(イベント ハブへのストリーム)、または __[Send to Log Analytics]__(Log Analytics に送信) のいずれかを選択する必要があります。

診断設定を有効にしたら、__[診断ログ]__ ブレードに戻ってログを表示できます。

## <a name="view-logs"></a>ログを表示する。

Data Lake Analytics アカウントのログ データを確認する方法は&2; つあります。

* Data Lake Analytics アカウントの設定から
* データが格納されている Azure Storage アカウントから

### <a name="using-the-data-lake-analytics-settings-view"></a>Data Lake Analytics の [設定] ビューの使用

1. Data Lake Analytics アカウントのブレードで **[診断ログ]** を選択し、ログを表示するエントリを選択します。

    ![診断ログの表示](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "診断ログの表示")

2. ログは、**[監査ログ]** および **[要求ログ]** によって分類されます。

    ![ログ エントリ](./media/data-lake-analytics-diagnostic-logs/diagnostic-log-entries.png)

   * 要求ログは、Data Lake Analytics アカウントで行われるすべての API 要求をキャプチャします。
   * 監査ログは要求ログとよく似ていますが、Data Lake Analytics アカウントで実行される操作についてより詳しい内訳を提供します。 たとえば、要求ログでは単一のアップロードの API 呼び出しが、監査ログでは複数の "追加" 操作になる可能性があります。

3. 各ログ エントリの **[ダウンロード]** リンクをクリックして、ログをダウンロードします。

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>ログ データを含む Azure Storage アカウントから

1. ログ記録用の Data Lake Analytics が関連付けられている [Azure Storage アカウント] ブレードを開き、[BLOB] をクリックします。 **[BLOB サービス]** ブレードに&2; つのコンテナーが一覧表示されます。

    ![診断ログの表示](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "診断ログの表示")

   * コンテナー **insights-logs-audit** には、監査ログが含まれます。
   * コンテナー **insights-logs-requests** には、要求ログが含まれます。
2. これらのコンテナー内で、ログは次の構造の下に格納されます。

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

   > [!NOTE]
   > パスのエントリの `##` の部分には、ログを作成した年、月、日、時間の情報が入ります。 Data Lake Analytics では、ログのファイルを&1; 時間に&1; つ作成します。このため、`m=` の値は常に `00` となります。

    たとえば、監査ログへの完全パスは以下のようになります。

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    同様に、要求ログへの完全パスは以下のようになります。

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>ログの構造

監査ログと要求ログは JSON 形式で作成されます。 このセクションでは、要求ログと監査ログの JSON 構造を確認します。

### <a name="request-logs"></a>要求ログ

JSON 形式の要求ログのエントリの例を次に示します。 各 BLOB には、ログ オブジェクトの配列を含む、 **レコード** と呼ばれるルート オブジェクトが&1; つあります。

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

#### <a name="request-log-schema"></a>要求ログのスキーマ

| Name | 型 | Description |
| --- | --- | --- |
| time |文字列 |ログのタイムスタンプ (UTC) |
| resourceId |文字列 |操作が行われたリソースの ID |
| カテゴリ |文字列 |ログのカテゴリ。 **Requests**など。 |
| operationName |文字列 |ログに記録される操作の名前。 GetAggregatedJobHistory など。 |
| resultType |文字列 |操作の状態。200 など。 |
| callerIpAddress |文字列 |要求を行うクライアントの IP アドレス |
| correlationId |文字列 |ログの ID。 この値は、関係のあるログ エントリをグループ化する際に使用します |
| ID |オブジェクト |ログを生成した ID |
| プロパティ |JSON |詳しくは、次のセクション (「要求ログのプロパティのスキーマ」) をご覧ください |

#### <a name="request-log-properties-schema"></a>要求ログのプロパティのスキーマ

| Name | 型 | Description |
| --- | --- | --- |
| HttpMethod |文字列 |操作に使用される HTTP メソッド。 GET など。 |
| パス |文字列 |操作が実行されたパス |
| RequestContentLength |int |HTTP 要求のコンテンツの長さ |
| ClientRequestId |文字列 |この要求を一意に識別する ID |
| StartTime |文字列 |サーバーが要求を受信した時刻 |
| EndTime |文字列 |サーバーが応答を送信した時間 |

### <a name="audit-logs"></a>監査ログ

JSON 形式の監査ログのエントリの例を次に示します。 各 BLOB には、ログ オブジェクトの配列を含む、 **レコード** と呼ばれるルート オブジェクトが&1; つあります。

    {
    "records":
      [        
        . . . .
        ,
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
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>監査ログのスキーマ

| Name | 型 | Description |
| --- | --- | --- |
| time |文字列 |ログのタイムスタンプ (UTC) |
| resourceId |文字列 |操作が行われたリソースの ID |
| カテゴリ |文字列 |ログのカテゴリ。 **Audit**など。 |
| operationName |文字列 |ログに記録される操作の名前。 JobSubmitted など。 |
| resultType |文字列 |ジョブの状態 (operationName) の副状態。 |
| resultSignature |文字列 |ジョブの状態 (operationName) に関する追加の詳細。 |
| ID |文字列 |操作を要求したユーザー。 たとえば、「susan@contoso.com」のように入力します。 |
| プロパティ |JSON |詳しくは、次のセクション (「監査ログのプロパティのスキーマ」) をご覧ください |

> [!NOTE]
> **resultType** と **resultSignature** は、操作の結果に関する情報を示すものであり、操作が完了した場合にのみ値が入ります。 この&2; つには、たとえば **operationName** の値が **JobStarted** または **JobEnded** の場合に値が入ります。
>
>

#### <a name="audit-log-properties-schema"></a>監査ログのプロパティのスキーマ

| Name | 型 | Description |
| --- | --- | --- |
| JobId |文字列 |ジョブに割り当てられた ID |
| JobName |文字列 |ジョブに与えられている名前 |
| JobRunTime |文字列 |ジョブの処理に使用するランタイム |
| SubmitTime |文字列 |ジョブが送信された時間 (UTC) |
| StartTime |文字列 |ジョブが送信された後に実行を開始した時刻 (UTC)。 |
| EndTime |文字列 |ジョブが終了した時刻。 |
| 並列処理 |文字列 |このジョブの送信中にこのジョブについて要求された Data Lake Analytics ユニットの数。 |

> [!NOTE]
> **SubmitTime**、**StartTime**、**EndTime**、**Parallelism** の&4; つは、操作に関する情報を提供するものであり、操作が開始または完了した場合にのみ値が入ります。 たとえば、**SubmitTime** であれば、**operationName** が **JobSubmitted** になった後に値が入ります。

## <a name="process-the-log-data"></a>ログ データの処理

Azure Data Lake Analytics では、ログ データの処理と分析方法に関するサンプルを提供しています。 サンプルについては [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)をご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)



<!--HONumber=Feb17_HO2-->


