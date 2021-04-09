---
title: Azure Data Lake Storage Gen1 の診断ログの表示 | Microsoft Docs
description: 'Azure Data Lake Storage Gen1 の診断ログを設定し、それにアクセスする方法について説明します。 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 0b84e26962b00ee5b9d4c34cab7efbcc9aa0bf01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582802"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 の診断ログへのアクセス
Azure Data Lake Storage Gen1 アカウントの診断ログを有効にする方法、およびそのアカウント用に収集されたログを表示する方法について説明します。

組織は、データにアクセスしているユーザーの一覧、データがアクセスされる頻度、アカウントに格納されているデータの量などの情報を提供するデータ アクセスの監査証跡を収集するために、Azure Data Lake Storage Gen1 アカウントの診断ログを有効にすることができます。有効にすると、診断および/または要求が、ベストエフォートでログに記録されます。 要求と診断の両方のログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Storage Gen1 アカウント**。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントの診断ログを有効にする
1. 新しい [Azure Portal](https://portal.azure.com) にサインオンします。
2. Data Lake Storage Gen1 アカウントを開き、Data Lake Storage Gen1 アカウント ブレードから **[診断設定]** をクリックします。
3. **[診断設定]** ブレードで、 **[診断を有効にする]** をクリックします。

    ![[診断設定] オプションと [診断を有効にする] オプションが強調表示されている Data Lake Storage Gen1 アカウントのスクリーンショット。](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Traffic Manager で診断ログを有効にする")

3. **[診断設定]** ブレードで、診断ログの構成を次のように変更します。
   
    ![[名前] テキスト ボックスと [保存] オプションが強調表示されている [診断設定] セクションのスクリーンショット。](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Traffic Manager で診断ログを有効にする")
   
   * **[名前]** には、診断ログ構成の値を入力します。
   * さまざまな方法でデータを格納および処理することができます。
     
        * **[Archive to a storage account (ストレージ アカウントへのアーカイブ)]** オプションを選択して、Azure ストレージ アカウントにログを保存します。 後日バッチ処理するデータをアーカイブする場合は、このオプションを使用します。 このオプションを選択する場合は、ログの保存先の Azure ストレージ アカウントを指定する必要があります。
        
        * **[Stream to an event hub (イベント ハブへのストリーム)]** オプションを選択して、Azure Event Hub にログ データをストリーミングします。 リアルタイムで受信したログを分析するためのダウン ストリーム処理パイプラインがある場合は、ほとんどの場合、このオプションを使用します。 このオプションを選択する場合、使用する Azure Event Hub の詳細を指定する必要があります。

        * **[Send to Log Analytics (Log Analytics に送信)]** オプションを選択して、Azure Monitor サービスを使用して、生成されたログ データを分析します。 このオプションを選択する場合は、ログ分析を実行する際に使用する Log Analytics ワークスペースの詳細情報を指定する必要があります。 Azure Monitor ログの使用についての詳細は、「[Azure Monitor のログ検索で収集されたデータの表示または分析](../azure-monitor/logs/log-analytics-tutorial.md)」をご覧ください。
     
   * 監査ログ、要求ログ、またはその両方のいずれを取得するかを指定します。
   * データを保持する日数を指定します。 リテンション期間は、Azure ストレージ アカウントを使用してログ データをアーカイブする場合のみ適用されます。
   * **[保存]** をクリックします。

診断設定を有効にしたら、 **[診断ログ]** タブでログを確認できます。

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントの診断ログを表示する
Data Lake Storage Gen1 アカウントのログ データを表示するには、2 つの方法があります。

* Data Lake Storage Gen1 アカウントの [設定] ビューから
* データが格納されている Azure Storage アカウントから

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Data Lake Storage Gen1 の [設定] ビューの使用
1. Data Lake Storage Gen1 アカウントの **[設定]** ブレードから、 **[診断ログ]** をクリックします。
   
    ![診断ログを表示する](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "診断ログを表示する") 
2. **[診断ログ]** ブレードには、**監査ログ** と **要求ログ** によって分類されたログが表示されます。
   
   * 要求ログは、Data Lake Storage Gen1 アカウントで作成されたすべての API 要求をキャプチャします。
   * 監査ログは要求ログに似ていますが、Data Lake Storage Gen1 アカウントで実行されている操作のさらに詳細な内訳を提供します。 たとえば、要求ログでは単一のアップロードの API 呼び出しが、監査ログでは複数の "追加" 操作になる可能性があります。
3. ログをダウンロードするには、各ログ エントリに対する **[ダウンロード]** リンクをクリックします。

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>ログ データを含む Azure Storage アカウントから
1. ログ記録用に Data Lake Storage Gen1 に関連付けられている [Azure Storage アカウント] ブレードを開き、[BLOB] をクリックします。 **[Blob service]** ブレードに 2 つのコンテナーが一覧表示されます。
   
    ![[BLOB] オプションが選択され、[Blob service] ブレードで 2 つの Blob service の名前が強調表示されている、Data Lake Storage Gen1 ブレードのスクリーンショット。](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "診断ログを表示する")
   
   * コンテナー **insights-logs-audit** には、監査ログが含まれます。
   * コンテナー **insights-logs-requests** には、要求ログが含まれます。
2. これらのコンテナー内で、ログは次の構造の下に格納されます。
   
    ![コンテナーに格納されているログ構造のスクリーンショット。](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "診断ログを表示する")
   
    たとえば、監査ログへの完全パスは `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    同様に、要求ログへの完全パスは `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json` のようになります。

## <a name="understand-the-structure-of-the-log-data"></a>ログ データの構造について
監査ログと要求ログは JSON 形式で作成されます。 このセクションでは、要求ログと監査ログの JSON 構造を確認します。

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
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"StoreIngressSize":0 ,"StoreEgressSize":4096,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z","QueryParameters":"api-version=<version>&op=<operationName>"}
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
| operationName |String |ログに記録される操作の名前。 getfilestatus など。 |
| resultType |String |操作の状態。200 など。 |
| callerIpAddress |String |要求を行うクライアントの IP アドレス |
| correlationId |String |関連するログ エントリのセットをグループ化するために使用できる、ログの ID |
| identity |Object |ログを生成した ID |
| properties |JSON |詳細については、以下をご覧ください。 |

#### <a name="request-log-properties-schema"></a>要求ログのプロパティのスキーマ
| 名前 | Type | 説明 |
| --- | --- | --- |
| HttpMethod |String |操作に使用される HTTP メソッド。 GET など。 |
| Path |String |操作が実行されたパス |
| RequestContentLength |INT |HTTP 要求のコンテンツの長さ |
| ClientRequestId |String |この要求を一意に識別する ID |
| StartTime |String |サーバーが要求を受信した時刻 |
| EndTime |String |サーバーが応答を送信した時間 |
| StoreIngressSize |Long |Data Lake Store へのイングレスのバイト単位のサイズ |
| StoreEgressSize |Long |Data Lake Store からのエグレスのバイト単位のサイズ |
| QueryParameters |String |説明:HTTP クエリ パラメーターを次に示します。 例 1: api-version=2014-01-01&op=getfilestatus 例 2: op=APPEND&append=true&syncFlag=DATA&filesessionid=bee3355a-4925-4435-bb4d-ceea52811aeb&leaseid=bee3355a-4925-4435-bb4d-ceea52811aeb&offset=28313319&api-version=2017-08-01 |

### <a name="audit-logs"></a>監査ログ
JSON 形式の監査ログのエントリの例を次に示します。 各 BLOB には、ログ オブジェクトの配列を含む、 **レコード** と呼ばれるルート オブジェクトが 1 つあります。

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>監査ログのスキーマ
| 名前 | Type | 説明 |
| --- | --- | --- |
| time |String |ログのタイムスタンプ (UTC) |
| resourceId |String |操作が行われたリソースの ID |
| category |String |ログのカテゴリ。 **Audit** など。 |
| operationName |String |ログに記録される操作の名前。 getfilestatus など。 |
| resultType |String |操作の状態。200 など。 |
| resultSignature |String |操作に関する追加情報。 |
| correlationId |String |関連するログ エントリのセットをグループ化するために使用できる、ログの ID |
| identity |Object |ログを生成した ID |
| properties |JSON |詳細については、以下をご覧ください。 |

#### <a name="audit-log-properties-schema"></a>監査ログのプロパティのスキーマ
| 名前 | Type | 説明 |
| --- | --- | --- |
| StreamName |String |操作が実行されたパス |

## <a name="samples-to-process-the-log-data"></a>ログ データ処理のサンプル
Azure Data Lake Storage Gen1 から Azure Monitor ログにログを送信すると (Azure Monitor ログの使用の詳細については、「[Azure Monitor ログ検索で収集されたデータの表示または分析](../azure-monitor/logs/log-analytics-tutorial.md)」を参照)、次のクエリによってユーザーの表示名、イベントの時刻、およびイベントの時刻でのイベント数の一覧を含むテーブルがビジュアル グラフと共に返されます。 これに変更を加えて、ユーザーの GUID などの属性を簡単に表示できます。

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 には、ログ データを処理および分析する方法に関するサンプルが用意されています。 [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) でサンプルを見つけることができます。 

## <a name="see-also"></a>関連項目
* [Azure Data Lake Storage Gen1 の概要](data-lake-store-overview.md)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
