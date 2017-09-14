---
title: "診断ログによる Azure Stream Analytics のトラブルシューティング | Microsoft Docs"
description: "Microsoft Azure で Stream Analytics ジョブから診断ログを分析する方法について説明します。"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: ea90a62ffee9c766985f76e1c0abc1585bebc69b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017

---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>診断ログを使用した Azure Stream Analytics のトラブルシューティング

Azure Stream Analytics ジョブは予期せず処理を停止することがあります。 このため、この種のイベントのトラブルシューティングを行えることが重要です。 このようなイベントは、予期しないクエリ結果、デバイスへの接続、または予期しないサービス停止によって引き起こされる可能性があります。 Stream Analytics の診断ログは、問題が発生した際にその原因を特定し、復旧時間を短縮するのに役立ちます。

## <a name="log-types"></a>ログの種類

Stream Analytics には 2 種類のログがあります。 
* [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (常に有効)。 アクティビティ ログでは、ジョブで実行される操作の洞察が得られます。
* [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (構成可能)。 診断ログでは、ジョブで発生するあらゆるイベントに関して豊富な洞察が得られます。 診断ログはジョブの作成時に開始され、ジョブが削除されると終了します。 ジョブの更新時とジョブの実行中のイベントがログの対象です。

> [!NOTE]
> Azure Storage、Azure Event Hubs、Azure Log Analytics などのサービスを使用して、問題となったデータを分析できます。 これらのサービスでは、価格モデルに基づいて料金が発生します。
>

## <a name="turn-on-diagnostics-logs"></a>診断ログの有効化

既定では、診断ログは**オフ**になっています。 診断ログを有効にするには、次の手順を実行します。

1.  Azure Portal にサインインし、[ストリーミング ジョブ] ブレードに移動します。 **[監視]** の下の **[診断ログ]** を選択します。

    ![ブレードを使った診断ログへの移動](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  **[診断を有効にする]** を選択します。

    ![診断ログの有効化](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  **[診断設定]** ページの **[状態]** を **[オン]** に選択します。

    ![診断ログの状態の変更](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  目的のアーカイブ ターゲットを設定します (ストレージ アカウント、イベント ハブ、Log Analytics)。 次に、収集するログのカテゴリを選択します (実行、作成)。 

5.  新しい診断構成を保存します。

診断構成が有効になるまで約 10 分かかります。 その後、構成したアーカイブ ターゲットのログが **[診断ログ]** ページに表示されます。

![ブレードを使った診断ログへの移動 - アーカイブ ターゲット](./media/stream-analytics-job-diagnostic-logs/image4.png)

診断の構成の詳細については、「[Azure リソースからの診断データの収集と使用](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)」を参照してください。

## <a name="diagnostics-log-categories"></a>診断ログのカテゴリ

現時点では、取得する診断ログのカテゴリは次の 2 つです。

* **作成**。 ジョブ作成操作に関連するログ イベントを取得します (ジョブの作成、入出力の追加と削除、クエリの追加と更新、ジョブの開始と停止)。
* **実行**。 ジョブの実行中に発生したイベントを取得します。
    * 接続エラー
    * データ処理エラー。次のエラーが含まれます。
        * クエリ定義に準拠していないイベント (フィールドの種類と値の不一致、フィールドの不足など)
        * 式評価エラー
    * その他のイベントとエラー

## <a name="diagnostics-logs-schema"></a>診断ログのスキーマ

すべてのログは JSON 形式で格納されます。 各エントリには、次の一般的な文字列フィールドが含まれています。

名前 | 説明
------- | -------
time | ログのタイムスタンプ (UTC)。
resourceId | 操作が行われたリソースの ID (大文字)。 サブスクリプション ID、リソース グループ、ジョブ名が含まれています。 例: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**
カテゴリ | ログのカテゴリ (**実行**または**作成**のいずれか)。
operationName | ログに記録される操作の名前。 たとえば、**Send Events: SQL Output write failure to mysqloutput**。
status | 操作の状態。 たとえば、**失敗**または**成功**。
最小限のレベル | ログ レベル。 たとえば、**エラー**、**警告**、または**情報**。
properties | ログ エントリ固有の詳細。JSON 文字列としてシリアル化されています。 詳細については、次のセクションを参照してください。

### <a name="execution-log-properties-schema"></a>実行ログ プロパティのスキーマ

実行ログには、Stream Analytics ジョブの実行中に発生したイベントに関する情報が含まれます。 イベントの種類に応じてプロパティのスキーマが異なります。 現在使用されている実行ログの種類を次に示します。

### <a name="data-errors"></a>データ エラー

ジョブがデータを処理している間に発生したエラーはすべて、ログのこのカテゴリに含まれます。 これらのログはほとんどの場合、データ読み取り、シリアル化、書き込み操作が実行されている間に作成されます。 ここには接続エラーが含まれません。 接続エラーは汎用イベントとして扱われます。

名前 | 説明
------- | -------
から | エラーが発生したジョブ入出力の名前。
メッセージ | エラーに関連付けられているメッセージ。
型 | エラーの種類。 たとえば、**DataConversionError**、**CsvParserError**、または **ServiceBusPropertyColumnMissingError**。
データ | エラーの原因を正確に特定するうえで役に立つデータが含まれています。 サイズに応じて切り捨てられます。

データ エラーのスキーマは、**operationName** 値に応じて次のようになります。
* **シリアル化イベント**。 シリアル化イベントはイベント読み取り操作中に発生します。 これらは、次のいずれかの理由で入力データがクエリ スキーマを満たしていない場合に発生します。
    * "*イベントのシリアル化 (逆シリアル化) 中の種類の不一致*": エラーを引き起こしたフィールドが特定されます。
    * "*イベントを読み取ることができない。無効なシリアル化*": エラーが発生した入力データの場所に関する情報が一覧で表示されます。 ここには、BLOB 入力の BLOB 名、オフセット、データのサンプルが含まれます。
* **送信イベント**。 送信イベントは書き込み操作中に発生します。 エラーを引き起こしたストリーミング イベントが特定されます。

### <a name="generic-events"></a>汎用イベント

汎用イベントには、上に挙げた以外のあらゆるイベントが含まれます。

名前 | 説明
-------- | --------
エラー | (省略可能) エラー情報。 使用できる場合は通常、例外情報です。
メッセージ| ログ メッセージ。
型 | メッセージの種類。 エラーの内部カテゴリにマップされます。 たとえば、**JobValidationError** または **BlobOutputAdapterInitializationFailure**。
関連付け ID | ジョブの実行を一意に識別する [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 ジョブが開始されてから停止するまでに生成された実行ログ エントリすべてに、同じ**関連付け ID** の値が付けられます。

## <a name="next-steps"></a>次のステップ

* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)

