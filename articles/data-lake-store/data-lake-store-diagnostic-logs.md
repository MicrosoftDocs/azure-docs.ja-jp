---
title: "Azure Data Lake Store の診断ログの表示 | Microsoft Docs"
description: "Azure Data Lake Store の診断ログの設定方法およびアクセス方法を理解する  "
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 59f072c7a8272fc04e1d662c0ab17e7ee4500fa6
ms.openlocfilehash: f139674f96793b8486c541c9e3f1ead751b97232


---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Azure Data Lake Store の診断ログへのアクセス
Data Lake Store アカウントの診断ログを有効にする方法と、アカウント用に収集されたログを表示する方法について説明します。

データにアクセスするユーザーの一覧、データにアクセスする頻度、アカウントに格納されているデータの量などの情報を提供するデータ アクセスの監査証跡を収集するには、組織で Azure Data Lake Store アカウントの診断ログを有効にします。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 「 [Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Data Lake Store アカウントの診断ログを有効にする
1. 新しい [Azure ポータル](https://portal.azure.com)にサインオンします。
2. Data Lake Store アカウントを開き、Data Lake Store アカウントのブレードで **[設定]**、**[診断ログ]** の順にクリックします。
3. **[診断ログ]** ブレードで、**[診断を有効にする]** をクリックします。

    ![診断ログの有効化](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "診断ログの有効化")

3. **[診断]** ブレードで、診断ログの構成を次のように変更します。
   
    ![診断ログの有効化](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "診断ログの有効化")
   
   * **[状態]** を **[オン]** に設定して診断ログを有効にします。
   * さまざまな方法でデータを格納および処理することができます。
     
        * **[Archive to a storage account (ストレージ アカウントへのアーカイブ)]** オプションを選択して、Azure ストレージ アカウントにログを保存します。 後日バッチ処理するデータをアーカイブする場合は、このオプションを使用します。 このオプションを選択する場合は、ログの保存先の Azure ストレージ アカウントを指定する必要があります。
        
        * **[Stream to an event hub (イベント ハブへのストリーム)]** オプションを選択して、Azure Event Hub にログ データをストリーミングします。 リアルタイムで受信したログを分析するためのダウン ストリーム処理パイプラインがある場合は、ほとんどの場合、このオプションを使用します。 このオプションを選択する場合、使用する Azure Event Hub の詳細を指定する必要があります。

        * **[Send to Log Analytics (Log Analytics に送信)]** オプションを選択して、Azure Log Analytics サービスを使用して、生成されたログ データを分析します。 このオプションを選択する場合は、ログ分析を実行する際に使用する Operations Management Suite ワークスペースの詳細情報を指定する必要があります。
     
   * 監査ログ、要求ログ、またはその両方のいずれを取得するかを指定します。
   * データを保持する日数を指定します。 リテンション期間は、Azure ストレージ アカウントを使用してログ データをアーカイブする場合のみ適用されます。
   * **[保存]**をクリックします。

診断設定を有効にしたら、 **[診断ログ]** タブでログを確認できます。

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Data Lake Store アカウントの診断ログを表示する
Data Lake Store アカウントのログ データを確認する方法は&2; つあります。

* Data Lake Store アカウントの [設定] ビューから
* データが格納されている Azure Storage アカウントから

### <a name="using-the-data-lake-store-settings-view"></a>Data Lake Store [設定] ビューの使用
1. Data Lake Store アカウントの **[設定]** ブレードで、**[診断ログ]** をクリックします。
   
    ![診断ログの表示](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "診断ログの表示") 
2. **[診断ログ]** ブレードに、**監査ログ**と**要求ログ**によって分類されたログが表示されます。
   
   * 要求ログは、Data Lake Store アカウントで行われるすべての API 要求をキャプチャします。
   * 監査ログは、要求ログと同様ですが、Data Lake Store アカウントで実行される操作についてより詳しい内訳を提供します。 たとえば、要求ログでは単一のアップロードの API 呼び出しが、監査ログでは複数の "追加" 操作になる可能性があります。
3. 各ログ エントリに対する **[ダウンロード]** リンクをクリックして、ログをダウンロードします。

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>ログ データを含む Azure Storage アカウントから
1. Data Lake Store とログが関連付けられている [Azure Storage アカウント] ブレードを開き、[BLOB] をクリックします。 **[BLOB サービス]** ブレードに&2; つのコンテナーが一覧表示されます。
   
    ![診断ログの表示](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "診断ログの表示")
   
   * コンテナー **insights-logs-audit** には、監査ログが含まれます。
   * コンテナー **insights-logs-requests** には、要求ログが含まれます。
2. これらのコンテナー内で、ログは次の構造の下に格納されます。
   
    ![診断ログの表示](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "診断ログの表示")
   
    たとえば、監査ログへの完全パスは `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    同様に、要求ログへの完全パスは `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>ログ データの構造について
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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName |文字列 |ログに記録される操作の名前。 getfilestatus など。 |
| resultType |文字列 |操作の状態。200 など。 |
| callerIpAddress |文字列 |要求を行うクライアントの IP アドレス |
| correlationId |文字列 |関連するログ エントリのセットをグループ化するために使用できるログの ID |
| ID |オブジェクト |ログを生成した ID |
| プロパティ |JSON |詳細については、以下をご覧ください。 |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName |文字列 |ログに記録される操作の名前。 getfilestatus など。 |
| resultType |文字列 |操作の状態。200 など。 |
| correlationId |文字列 |関連するログ エントリのセットをグループ化するために使用できるログの ID |
| ID |オブジェクト |ログを生成した ID |
| プロパティ |JSON |詳細については、以下をご覧ください。 |

#### <a name="audit-log-properties-schema"></a>監査ログのプロパティのスキーマ
| Name | 型 | Description |
| --- | --- | --- |
| StreamName |文字列 |操作が実行されたパス |

## <a name="samples-to-process-the-log-data"></a>ログ データ処理のサンプル
Azure Data Lake Store では、ログ データの処理と分析方法のサンプルを提供しています。 サンプルについては [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)をご覧ください。 

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)




<!--HONumber=Feb17_HO1-->


