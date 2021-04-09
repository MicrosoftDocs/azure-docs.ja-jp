---
title: データ エラー コード - Azure Stream Analytics
description: データ エラー コードに関する Azure Stream Analytics の問題のトラブルシューティングを行います。
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016492"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Azure Stream Analytics のデータ エラー コード

アクティビティ ログとリソース ログを使用して、予期しない動作のデバッグを Azure Stream Analytics ジョブから行うことができます。 この記事では、すべてのデータ エラーのエラー コードについて説明します。 データ エラーは、ストリーム内に不適切なデータ (予期しないレコード スキーマなど) がある場合に発生します。

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **原因**:入力データの逆シリアル化中にエラーが発生しました。

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **原因**:Stream Analytics でリソースのタイムスタンプを取得できません。 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **原因**:Stream Analytics で `TIMESTAMP BY OVER COLUMN` の値を取得できません。

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **原因**:構成された許容値よりも後に入力イベントが送信されました。

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **原因**:入力イベントのアプリケーション タイムスタンプのしきい値よりも前に入力イベントが到着しました。

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **原因**:Azure Functions へのメッセージ出力がサイズ制限を超えています。

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **原因**:イベント ハブへの書き込み時に、出力レコードがサイズの上限を超えています。

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **原因**:特定の列の値または型が無効です。
* **推奨事項**:255 文字以内の一意の空でない文字列を指定します。

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **原因**:出力レコードの Document ID に無効な文字が含まれています。

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **原因**:出力レコードに、主キー プロパティとして使用する列 \[id] が含まれていません。

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **原因**:出力レコードに Document ID プロパティが含まれていません。 
* **推奨事項**:クエリ出力に、'255' 文字未満の一意の空でない文字列を含む列が含まれていることを確認します。

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **原因**:出力レコードに、パーティション キー プロパティとして使用する列がありません。

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **原因**:Cosmos DB に対する 1 つのレコードの書き込みが大きすぎます。

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **原因**:データに問題があるため、Stream Analytics で SQL Database にイベントを書き込むことができません。

## <a name="next-steps"></a>次のステップ

* [入力接続のトラブルシューティング](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics の出力のトラブルシューティング](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics のクエリのトラブルシューティング](stream-analytics-troubleshoot-query.md)
* [リソース ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics データ エラー](data-errors.md)
