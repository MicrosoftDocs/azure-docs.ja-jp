---
title: 診断ログを使用した Azure Stream Analytics のトラブルシューティング
description: この記事では、Azure Stream Analytics で診断ログを分析する方法について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 68c40cf893bf150756f0a03056473e82cff5754f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620956"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>診断ログを使用した Azure Stream Analytics のトラブルシューティング

Azure Stream Analytics ジョブは予期せず処理を停止することがあります。 このため、この種のイベントのトラブルシューティングを行えることが重要です。 障害は、予期しないクエリ結果、デバイスへの接続、または予期しないサービス停止によって引き起こされる可能性があります。 Stream Analytics の診断ログは、問題が発生した際にその原因を特定し、復旧時間を短縮するのに役立ちます。

運用環境のすべてのジョブに対して診断ログを有効にすることを強くお勧めします。

## <a name="log-types"></a>ログの種類

Stream Analytics には 2 種類のログがあります。

* [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (常に有効) では、ジョブで実行される操作の分析情報が得られます。

* [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (構成可能) では、ジョブで発生するあらゆるイベントに関して豊富な分析情報が得られます。 診断ログはジョブの作成時に開始され、ジョブが削除されると終了します。 ジョブの更新時とジョブの実行中のイベントがログの対象です。

> [!NOTE]
> Azure Storage、Azure Event Hubs、Azure Monitor ログなどのサービスを使用して、問題となったデータを分析できます。 これらのサービスでは、価格モデルに基づいて料金が発生します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>アクティビティ ログを使用したデバッグ

アクティビティ ログは既定で有効になっていて、Stream Analytics ジョブで実行される操作の高度な分析情報が得られます。 ジョブに影響を与える問題の根本原因を見つけるには、アクティビティ ログに含まれている情報が役立ちます。 Stream Analytics でアクティビティ ジョブを使用するためには、次の手順を実行します。

1. Azure portal にサインインし、 **[概要]** で **[アクティビティ ログ]** を選択します。

   ![Stream Analytics アクティビティ ログ](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. 実行された操作の一覧を確認できます。 ジョブが失敗する原因となったすべての操作には、赤い情報バブルが表示されます。

3. 操作をクリックすると、その概要ビューが表示されます。 多くの場合、ここにある情報は制限されています。 操作の詳細を表示するには、**JSON** をクリックします。

   ![Stream Analytics アクティビティ ログ操作の概要](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. JSON の **[プロパティ]** セクションまで下へスクロールして、失敗した操作の原因となったエラーの詳細を表示します。 この例では、失敗は範囲外の緯度値からのランタイム エラーが原因でした。 Stream Analytics ジョブによって処理されるデータに矛盾があると、データ エラーが発生します。 さまざまな[入力データと出力データのエラーとそれらの発生する理由](https://docs.microsoft.com/azure/stream-analytics/data-errors)を学習できます。

   ![JSON エラーの詳細](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. JSON のエラー メッセージに基づいて是正措置を取ることができます。 この例では、-90 度から 90 度の間の緯度値をクエリに追加する必要があることを確認します。

6. 根本原因を特定するのにアクティビティ ログのエラー メッセージが役に立たない場合は、診断ログを有効にして Azure Monitor ログを使用します。

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Azure Monitor ログへの診断データの送信

診断ログを有効にして Azure Monitor ログに送信することを、強くお勧めします。 既定では、診断ログは**オフ**になっています。 診断ログを有効にするには、次の手順を実行します。

1.  Azure portal にサインインして、Stream Analytics ジョブに移動します。 **[監視]** の下の **[診断ログ]** を選択します。 次に、 **[診断を有効にする]** を選択します。

    ![ブレードを使った診断ログへの移動](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  **[診断設定]** で **[名前]** を作成し、 **[Log Analytics への送信]** の横にあるチェック ボックスをオンにします。 次に、既存の **Log Analytics ワークスペース**を追加するか、新規作成します。 **[ログ]** で **[実行]** と **[作成]** 、および **[メトリック]** で **[AllMetrics]** のチェック ボックスをオンにします。 **[Save]** をクリックします。 追加のコストを防ぐために、対象の Stream Analytics ジョブと同じ Azure リージョン内の Log Analytics ワークスペースを使用することをお勧めします。

    ![診断ログの設定](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Stream Analytics ジョブが開始すると、診断ログが Log Analytics ワークスペースにルーティングされます。 Log Analytics ワークスペースに移動して、 **[全般]** セクションで **[ログ]** を選択します。

   ![[全般] セクションの Azure Monitor ログ](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. 語句の検索、傾向の把握、パターンの分析、およびデータに基づいた分析情報の提供を行う、[独自のクエリを記述](../azure-monitor/log-query/get-started-portal.md)することができます。 たとえば、“The streaming job failed (ストリーミング ジョブが失敗しました)” というメッセージが含まれている診断ログのみをフィルター処理するクエリを記述できます。 Azure Stream Analytics からの診断ログは、**AzureDiagnostics** テーブルに格納されます。

   ![診断のクエリと結果](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. 適切なログを検索するクエリがある場合は、 **[保存]** を選択して保存し、名前とカテゴリを入力します。 その後、 **[新しいアラート ルール]** を選択することでアラートを作成できます。 次に、アラートの条件を指定します。 **[条件]** を選択し、このカスタム ログ検索を評価するしきい値と頻度を入力します。  

   ![診断ログ検索クエリ](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. アラート ルールを作成する前に、アクション グループを選択し、名前や説明などのアラートの詳細を指定します。 さまざまなジョブの診断ログを、同じ Log Analytics ワークスペースにルーティングすることができます。 これにより、すべてのジョブで機能するアラートを 1 回設定することができます。  

## <a name="diagnostics-log-categories"></a>診断ログのカテゴリ

Azure Stream Analytics では、次の 2 つのカテゴリの診断ログをキャプチャします。

* **作成**:ジョブ作成操作 (ジョブの作成、入出力の追加と削除、クエリの追加と更新、ジョブの開始と停止など) に関連するログ イベントを取得します。

* **実行**:ジョブの実行中に発生したイベントを取得します。
    * 接続エラー
    * データ処理エラー。次のエラーが含まれます。
        * クエリ定義に準拠していないイベント (フィールドの種類と値の不一致、フィールドの不足など)
        * 式評価エラー
    * その他のイベントとエラー

## <a name="diagnostics-logs-schema"></a>診断ログのスキーマ

すべてのログは JSON 形式で格納されます。 各エントリには、次の一般的な文字列フィールドが含まれています。

EnableAdfsAuthentication | 説明
------- | -------
time | ログのタイムスタンプ (UTC)。
resourceId | 操作が行われたリソースの ID (大文字)。 サブスクリプション ID、リソース グループ、ジョブ名が含まれています。 例: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**
category | ログのカテゴリ (**実行**または**作成**のいずれか)。
operationName | ログに記録される操作の名前。 例: **Send Events:SQL Output write failure to mysqloutput**。
status | 操作の状態。 たとえば、**失敗**または**成功**。
level | ログ レベル。 たとえば、**エラー**、**警告**、または**情報**。
properties | ログ エントリ固有の詳細。JSON 文字列としてシリアル化されています。 詳細については、この記事の次のセクションを参照してください。

### <a name="execution-log-properties-schema"></a>実行ログ プロパティのスキーマ

実行ログには、Stream Analytics ジョブの実行中に発生したイベントに関する情報が含まれます。 プロパティのスキーマは、イベントがデータ エラーまたは汎用イベントのどちらであるかによって異なります。

### <a name="data-errors"></a>データ エラー

ジョブがデータを処理している間に発生したエラーはすべて、ログのこのカテゴリに含まれます。 これらのログはほとんどの場合、データ読み取り、シリアル化、書き込み操作が実行されている間に作成されます。 ここには接続エラーが含まれません。 接続エラーは汎用イベントとして扱われます。 さまざまな[入力および出力データ エラー](https://docs.microsoft.com/azure/stream-analytics/data-errors)の原因についてさらに学習することができます。

EnableAdfsAuthentication | 説明
------- | -------
source | エラーが発生したジョブ入出力の名前。
Message | エラーに関連付けられているメッセージ。
Type | エラーの種類。 たとえば、**DataConversionError**、**CsvParserError**、または **ServiceBusPropertyColumnMissingError**。
データ | エラーの原因を正確に特定するうえで役に立つデータが含まれています。 サイズに応じて切り捨てられます。

データ エラーのスキーマは、**operationName** 値に応じて次のようになります。

* **シリアル化イベント**はイベント読み取り操作中に発生します。 これらは、次のいずれかの理由で入力データがクエリ スキーマを満たしていない場合に発生します。

   * "*イベントの (逆) シリアル化中の種類の不一致*":エラーを引き起こしたフィールドが特定されます。

   * "*イベントを読み取ることができない。無効なシリアル化*":エラーが発生した入力データの場所に関する情報が一覧で表示されます。 ここには、BLOB 入力の BLOB 名、オフセット、データのサンプルが含まれます。

* **送信イベント**は書き込み操作中に発生します。 エラーを引き起こしたストリーミング イベントが特定されます。

### <a name="generic-events"></a>汎用イベント

汎用イベントには、上に挙げた以外のあらゆるイベントが含まれます。

EnableAdfsAuthentication | 説明
-------- | --------
Error | (省略可能) エラー情報。 使用できる場合は通常、例外情報です。
Message| ログ メッセージ。
Type | メッセージの種類。 エラーの内部カテゴリにマップされます。 たとえば、**JobValidationError** または **BlobOutputAdapterInitializationFailure**。
関連付け ID | ジョブの実行を一意に識別する [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 ジョブが開始されてから停止するまでに生成された実行ログ エントリすべてに、同じ**関連付け ID** の値が付けられます。

## <a name="next-steps"></a>次の手順

* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics データ エラー](https://docs.microsoft.com/azure/stream-analytics/data-errors)
