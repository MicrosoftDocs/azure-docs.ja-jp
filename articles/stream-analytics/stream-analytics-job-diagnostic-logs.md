---
title: リソース ログを使用した Azure Stream Analytics のトラブルシューティング
description: この記事では、Azure Stream Analytics でリソース ログを分析する方法について説明します。
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperf-fy21q1
ms.date: 06/18/2020
ms.openlocfilehash: 93d881419c4854b8e46608e150b55072267e0347
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574419"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>リソース ログを使用した Azure Stream Analytics のトラブルシューティング

Azure Stream Analytics ジョブは予期せず処理を停止することがあります。 このため、この種のイベントのトラブルシューティングを行えることが重要です。 障害は、予期しないクエリ結果、デバイスへの接続、または予期しないサービス停止によって引き起こされる可能性があります。 Stream Analytics のリソース ログは、問題が発生した際にその原因を特定し、復旧時間を短縮するのに役立ちます。

デバッグと監視に非常に役立つため、すべてのジョブのリソース ログを有効にすることを強くお勧めします。

## <a name="log-types"></a>ログの種類

Stream Analytics には 2 種類のログがあります。

* [アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md) (常に有効) では、ジョブで実行される操作の分析情報が得られます。

* [リソース ログ](../azure-monitor/essentials/platform-logs-overview.md) (構成可能) では、ジョブで発生するあらゆるイベントに関してより豊富な分析情報が得られます。 リソース ログはジョブの作成時に開始され、ジョブが削除されると終了します。 ジョブの更新時とジョブの実行中のイベントがログの対象です。

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

4. JSON の **[プロパティ]** セクションまで下へスクロールして、失敗した操作の原因となったエラーの詳細を表示します。 この例では、失敗は範囲外の緯度値からのランタイム エラーが原因でした。 Stream Analytics ジョブによって処理されるデータに矛盾があると、データ エラーが発生します。 さまざまな[入力データと出力データのエラーとそれらの発生する理由](./data-errors.md)を学習できます。

   ![JSON エラーの詳細](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. JSON のエラー メッセージに基づいて是正措置を取ることができます。 この例では、-90 度から 90 度の間の緯度値をクエリに追加する必要があることを確認します。

6. 根本原因を特定するのにアクティビティ ログのエラー メッセージが役に立たない場合は、リソース ログを有効にして Azure Monitor ログを使用します。

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Azure Monitor ログへの診断データの送信

リソース ログをオンにして Azure Monitor ログに送信することを、強くお勧めします。 既定では、**オフ** になっています。 オンにするには、次の手順を完了します。

1.  Log Analytics ワークスペースを作成します (まだ作成していない場合)。 対象の Stream Analytics ジョブと同じリージョン内に Log Analytics ワークスペースを用意することをお勧めします。

2.  Azure portal にサインインして、Stream Analytics ジョブに移動します。 **[監視]** の下の **[診断ログ]** を選択します。 次に、 **[診断を有効にする]** を選択します。

    ![リソース ログへのブレード ナビゲーション](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  **[診断設定の名前]** に **名前** を指定し、 **[ログ]** の下にある **[実行]** および **[作成]** と、 **[メトリック]** の下にある **[AllMetrics]** の各ボックスをオンにします。 次に、 **[Log Analytics への送信]** を選択し、ワークスペースを選択します。 **[保存]** をクリックします。

    ![リソース ログの設定](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. Stream Analytics ジョブが開始すると、リソース ログが Log Analytics ワークスペースにルーティングされます。 ジョブのリソース ログを表示するには、 **[監視]** セクションの **[ログ]** を選択します。

   ![スクリーンショットには、[ログ] が選択された [全般] メニューが示されています。](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics には、関心のあるログを簡単に検索できる定義済みのクエリが用意されています。 左側のペインで事前定義済みのクエリを選択し、 **[実行]** を選択できます。 下部のペインにクエリの結果が表示されます。 

   ![スクリーンショットには、Stream Analytics ジョブのログが示されています。](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>リソース ログのカテゴリ

Azure Stream Analytics では、次の 2 つのカテゴリのリソース ログをキャプチャします。

* **作成**:ジョブ作成操作 (ジョブの作成、入出力の追加と削除、クエリの追加と更新、ジョブの開始と停止など) に関連するログ イベントを取得します。

* **実行**:ジョブの実行中に発生したイベントを取得します。
    * 接続エラー
    * データ処理エラー。次のエラーが含まれます。
        * クエリ定義に準拠していないイベント (フィールドの種類と値の不一致、フィールドの不足など)
        * 式評価エラー
    * その他のイベントとエラー

## <a name="resource-logs-schema"></a>リソース ログのスキーマ

すべてのログは JSON 形式で格納されます。 各エントリには、次の一般的な文字列フィールドが含まれています。

名前 | 説明
------- | -------
time | ログのタイムスタンプ (UTC)。
resourceId | 操作が行われたリソースの ID (大文字)。 サブスクリプション ID、リソース グループ、ジョブ名が含まれています。 例: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**
category | ログのカテゴリ (**実行** または **作成** のいずれか)。
operationName | ログに記録される操作の名前。 例: **Send Events:SQL Output write failure to mysqloutput**。
status | 操作の状態。 たとえば、**失敗** または **成功**。
level | ログ レベル。 たとえば、**エラー**、**警告**、または **情報**。
properties | ログ エントリ固有の詳細。JSON 文字列としてシリアル化されています。 詳細については、この記事の次のセクションを参照してください。

### <a name="execution-log-properties-schema"></a>実行ログ プロパティのスキーマ

実行ログには、Stream Analytics ジョブの実行中に発生したイベントに関する情報が含まれます。 プロパティのスキーマは、イベントがデータ エラーまたは汎用イベントのどちらであるかによって異なります。

### <a name="data-errors"></a>データ エラー

ジョブがデータを処理している間に発生したエラーはすべて、ログのこのカテゴリに含まれます。 これらのログはほとんどの場合、データ読み取り、シリアル化、書き込み操作が実行されている間に作成されます。 ここには接続エラーが含まれません。 接続エラーは汎用イベントとして扱われます。 さまざまな[入力および出力データ エラー](./data-errors.md)の原因についてさらに学習することができます。

名前 | 説明
------- | -------
source | エラーが発生したジョブ入出力の名前。
Message | エラーに関連付けられているメッセージ。
Type | エラーの種類。 たとえば、**DataConversionError**、**CsvParserError**、または **ServiceBusPropertyColumnMissingError**。
Data | エラーの原因を正確に特定するうえで役に立つデータが含まれています。 サイズに応じて切り捨てられます。

データ エラーのスキーマは、**operationName** 値に応じて次のようになります。

* **シリアル化イベント** はイベント読み取り操作中に発生します。 これらは、次のいずれかの理由で入力データがクエリ スキーマを満たしていない場合に発生します。

   * "*イベントの (逆) シリアル化中の種類の不一致*":エラーを引き起こしたフィールドが特定されます。

   * "*イベントを読み取ることができない。無効なシリアル化*":エラーが発生した入力データの場所に関する情報が一覧で表示されます。 ここには、BLOB 入力の BLOB 名、オフセット、データのサンプルが含まれます。

* **送信イベント** は書き込み操作中に発生します。 エラーを引き起こしたストリーミング イベントが特定されます。

### <a name="generic-events"></a>汎用イベント

汎用イベントには、上に挙げた以外のあらゆるイベントが含まれます。

名前 | 説明
-------- | --------
エラー | (省略可能) エラー情報。 使用できる場合は通常、例外情報です。
Message| ログ メッセージ。
Type | メッセージの種類。 エラーの内部カテゴリにマップされます。 たとえば、**JobValidationError** または **BlobOutputAdapterInitializationFailure**。
関連付け ID | ジョブの実行を一意に識別する GUID。 ジョブが開始されてから停止するまでに生成された実行ログ エントリすべてに、同じ **関連付け ID** の値が付けられます。

## <a name="next-steps"></a>次のステップ

* [Stream Analytics データ エラー](./data-errors.md)
* [Stream Analytics クエリ言語リファレンス](/stream-analytics-query/stream-analytics-query-language-reference)