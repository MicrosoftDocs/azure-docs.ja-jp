---
title: "Azure Stream Analytics 診断ログ | Microsoft Docs"
description: "Microsoft Azure で Stream Analytics ジョブから診断ログを分析する方法について説明します。"
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 0dac2cc79de884def8d4cf0ee89dc2f645d35b34
ms.lasthandoff: 03/29/2017


---
# <a name="job-diagnostic-logs"></a>ジョブの診断ログ

## <a name="introduction"></a>はじめに
Stream Analytics には 2 種類のログがあります。 
* [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs): 常に有効で、ジョブで実行される操作の洞察が示されます。
* [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs): 構成可能で、作成/更新時、実行中、および削除されるまでに開始されたジョブで発生している、あらゆる事象に関する詳細な洞察が示されます。

> [!NOTE]
> 非準拠データの分析に Azure Storage、イベント ハブ、Log Analytics などのサービスを使用すると、これらのサービスの料金モデルに基づいて課金されることに注意してください。

## <a name="how-to-enable-diagnostic-logs"></a>診断ログを有効にする方法
既定では、診断ログは**オフ**になっています。 有効にするには、次の手順に従います。

Azure Portal にサインインし、[ストリーミング ジョブ] ブレードに移動します。 次に、[監視] の [診断ログ] ブレードに移動します。

![ブレードで診断ログに移動する](./media/stream-analytics-job-diagnostic-logs/image1.png)  

次に、[診断を有効にする] リンクをクリックします。

![診断ログを有効にする](./media/stream-analytics-job-diagnostic-logs/image2.png)

開いている診断で、状態を [オン] に変更します。

![診断ログの状態を変更する](./media/stream-analytics-job-diagnostic-logs/image3.png)

必要なアーカイブ ターゲット (ストレージ アカウント、イベント ハブ、Log Analytics) を構成し、収集するログのカテゴリ (実行、作成) を選択します。 次に、新しい診断の構成を保存します。

保存後に構成が有効になるまで約 10 分かかります。 その後、構成したアーカイブ ターゲットのログが [診断ログ] ブレードに表示されます。

![ブレードで診断ログに移動する](./media/stream-analytics-job-diagnostic-logs/image4.png)

診断の構成の詳細については、[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)に関するページをご覧ください。

## <a name="diagnostic-logs-categories"></a>診断ログのカテゴリ
現時点で取得できる診断ログのカテゴリは 2 つあります。

* **作成:** 入出力の作成、追加、および削除、クエリの追加と更新、ジョブの開始と停止など、ジョブ作成操作に関連するログを取得します。
* **実行:** ジョブの実行中に発生したことを取得します。
    * 接続エラー
    * データ処理エラー
        * クエリ定義に準拠していないイベント (一致しないフィールドの種類と値、存在しないフィールドなど)
        * 式評価エラー
    * その他

## <a name="diagnostic-logs-schema"></a>診断ログのスキーマ
すべてのログが JSON 形式で格納され、各エントリには次の一般的な文字列フィールドが含まれます。

名前 | 説明
------- | -------
time | ログのタイムスタンプ (UTC)
resourceId | 操作が行われたリソースの ID (大文字)。 サブスクリプション ID、リソース グループ、およびジョブ名が含まれています。 例: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**
カテゴリ | ログのカテゴリ (**実行**または**作成**のいずれか)。
operationName | ログに記録される操作の名前。 例: **Send Events: SQL Output write failure to mysqloutput**
status | 操作の状態。 **失敗、成功**など。
最小限のレベル | ログ レベル。 **エラー、警告、情報**など。
properties | ログ エントリ固有の詳細。JSON 文字列としてシリアル化されています。詳細については、次を参照してください

### <a name="execution-logs-properties-schema"></a>実行ログ プロパティのスキーマ
実行ログには、Stream Analytics ジョブの実行中に発生したイベントに関する情報が含まれます。
イベントの種類に応じてプロパティのスキーマが異なります。 現在使用されている種類を次に示します。

### <a name="data-errors"></a>データ エラー
このログ カテゴリには、データ処理中のエラーが分類されます。 主にデータの読み取り、シリアル化、および書き込み操作中に生成されます。 汎用イベントとして処理される接続エラーは含まれません。

名前 | 説明
------- | -------
から | エラーが発生したジョブ入出力の名前。
メッセージ | エラーに関連付けられているメッセージ。
型 | エラーの種類。 **DataConversionError、CsvParserError、ServiceBusPropertyColumnMissingError** など。
データ | エラーの原因を正確に特定するうえで役に立つデータが含まれています。 サイズに応じて切り捨てられます。

データ エラーのスキーマは、**operationName** 値に応じて次のようになります。
* **イベントをシリアル化** - 入力データがクエリ スキーマを満たしていないときに、イベント読み取り操作中に発生します。
    * イベントのシリアル化 (逆シリアル化) 中の種類の不一致: エラーを引き起こしたフィールド。
    * イベントを読み取ることができません。無効なシリアル化: エラーが発生した入力データの場所に関する情報: BLOB 入力の BLOB 名、オフセット、データのサンプル。
* **イベントを送信** - 書き込み操作: エラーを引き起こしたストリーミング イベント。

### <a name="generic-events"></a>汎用イベント
その他すべて

名前 | 説明
-------- | --------
エラー | (省略可能) エラー情報。通常は例外情報 (使用できる場合)。
メッセージ| ログ メッセージ。
型 | メッセージの種類。エラーの内部カテゴリにマップされます: **JobValidationError、BlobOutputAdapterInitializationFailure** など。
関連付け ID | ジョブの実行を一意に識別する [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 ジョブが開始されてから停止するまでに生成された実行ログ エントリすべてに、同じ "関連付け ID" が付けられます。



## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


