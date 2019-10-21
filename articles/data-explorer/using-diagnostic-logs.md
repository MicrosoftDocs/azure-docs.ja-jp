---
title: 診断ログを使用して Azure Data Explorer インジェスト操作を監視する
description: Azure Data Explorer の診断ログを設定してインジェスト操作を監視する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 7d0fec56791c0d3e7ae60d78da83cf286532b9ab
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123921"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>診断ログを使用して Azure Data Explorer インジェスト操作を監視する (プレビュー)

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースのテーブルに取り込み (読み込み) ます。 [Azure Monitor 診断ログ](/azure/azure-monitor/platform/diagnostic-logs-overview)は、Azure リソースの動作状況に関するデータを提供します。 Azure Data Explorer は、診断ログを使用してインジェストの成功と失敗に関する分析情報を取得します。 操作ログを Azure Storage、イベント ハブ、または Log Analytics にエクスポートして、インジェスト ステータスを監視することができます。 Azure Storage と Azure イベント ハブからのログを Azure Data Explorer クラスター内のテーブルにルーティングして詳細な分析を行うことができます。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプションがない場合は、[Azure の無料アカウント](https://azure.microsoft.com/free/)を作成します。
* [クラスターとデータベース](create-cluster-database-portal.md)を作成します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターの診断ログを設定する

診断ログを使用すると、以下のログ データの収集を構成できます。
* 成功したインジェスト操作ログには、正常に完了したインジェスト操作に関する情報が含まれています。
* 失敗したインジェスト操作ログには、失敗したインジェスト操作に関する詳細情報 (エラーの詳細を含む) が含まれています。 

その後、データはストレージ アカウントにアーカイブされるか、イベント ハブにストリーミングされるか、または仕様に応じて Log Analytics に送信されます。

### <a name="enable-diagnostic-logs"></a>Traffic Manager で診断ログを有効にする

既定では、診断ログは無効になっています。 診断ログを有効にするには、次のステップを実行します。

1. [Azure portal](https://portal.azure.com) で、監視する Azure Data Explorer クラスター リソースを選択します。
1. **[監視]** で **[診断設定]** を選択します。
  
    ![診断ログを追加する](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. **[診断設定の追加]** を選択します。
1. **[診断設定]** ウィンドウで以下を行います。
 
    ![診断設定の構成](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. 診断設定の **[名前]** を選択します。
    1. ストレージ アカウント、イベント ハブ、Log Analytics から 1 つ以上のターゲットを選択します。
    1. 収集するログ (`SucceededIngestion` または `FailedIngestion`) を選択します。
    1. 収集する [[メトリック]](using-metrics.md) を選択します (省略可能)。   
    1. **[保存]** を選択して、新しい診断ログの設定とメトリックを保存します。
    1. 診断ログのアクティブ化を要求するには、Azure portal で **[新しいサポート要求]** を作成します。

新しい設定は数分で設定されます。 ログは、構成したアーカイブ ターゲット (ストレージ アカウント、イベント ハブ、Log Analytics) に表示されます。 

## <a name="diagnostic-logs-schema"></a>診断ログのスキーマ

すべての [Azure Monitor 診断ログは、共通の上位スキーマを使用します](/azure/azure-monitor/platform/diagnostic-logs-schema)。 Azure Data Explorer には、独自のイベントに固有のプロパティがあります。 すべてのログは JSON 形式で格納されます。

### <a name="ingestion-logs-schema"></a>インジェスト ログ スキーマ

ログの JSON 文字列には、次の表に示す要素が含まれます。

|名前               |説明
|---                |---
|time               |レポートされた時刻
|resourceId         |Azure Resource Manager リソース ID
|operationName      |操作の名前:'MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION'
|operationVersion   |スキーマのバージョン:'1.0' 
|category           |操作のカテゴリ。 `SucceededIngestion` または `FailedIngestion`。 [正常な運用](#successful-ingestion-operation-log)と[運用の失敗](#failed-ingestion-operation-log)それぞれでプロパティは異なります。
|properties         |操作の詳細情報。

#### <a name="successful-ingestion-operation-log"></a>成功したインジェスト操作のログ

**例:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**成功した操作の診断ログのプロパティ**

|名前               |説明
|---                |---
|succeededOn        |インジェストの完了時刻
|operationId        |Azure Data Explorer インジェスト操作 ID
|database           |ターゲット データベースの名前
|table              |ターゲット テーブルの名前
|ingestionSourceId  |インジェスト データ ソースの ID
|ingestionSourcePath|インジェスト データ ソースまたは BLOB URI のパス
|rootActivityId     |アクティビティ ID

#### <a name="failed-ingestion-operation-log"></a>インジェスト操作の失敗ログ

**例:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**失敗した操作の診断ログのプロパティ**

|名前               |説明
|---                |---
|failedOn           |インジェストの完了時刻
|operationId        |Azure Data Explorer インジェスト操作 ID
|database           |ターゲット データベースの名前
|table              |ターゲット テーブルの名前
|ingestionSourceId  |インジェスト データ ソースの ID
|ingestionSourcePath|インジェスト データ ソースまたは BLOB URI のパス
|rootActivityId     |アクティビティ ID
|details            |エラーとエラーメッセージの詳しい説明
|errorCode          |エラー コード 
|failureStatus      |`Permanent` または `Transient`。 一時的なエラーは、再試行することで成功する可能性があります。
|originatesFromUpdatePolicy|エラーが更新ポリシーによって発生した場合は True です
|shouldRetry        |再試行が成功した場合は True です

## <a name="next-steps"></a>次の手順

[メトリックを使用してクラスターの正常性を監視する](using-metrics.md)
