---
title: 外部エラー コード - Azure Stream Analytics
description: 外部エラー コードに関する Azure Stream Analytics の問題のトラブルシューティングを行います。
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9f55a715b11b126ea340e665e008d7245e578190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016390"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Azure Stream Analytics の外部エラー コード

アクティビティ ログとリソース ログを使用して、予期しない動作のデバッグを Azure Stream Analytics ジョブから行うことができます。 この記事では、すべての外部エラー コードについて説明します。 外部エラーは、アップストリームまたはダウンストリームのサービスによってスローされる、Stream Analytics によってデータ エラー、構成エラー、または外部可用性エラーとして識別できない一般的なエラーです。

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **原因**:アダプターの初期化時にエラーが発生しました。

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **原因**:アダプターへのデータの書き込み中にエラーが発生しました。

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **原因**:Azure Functions から HTTP エラーが返されました。

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **原因**:Stream Analytics による Azure Functions へのイベントの書き込みが失敗しました。

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **原因**:Azure Functions への出力時にリダイレクト エラーが発生しました。

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **原因**:Azure Functions への出力でクライアント エラーが発生しました。

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **原因**:Azure Functions への出力でサーバー エラーが発生しました。

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **原因**:HTTP 要求がタイムアウトを超えたため、Azure Functions への書き込みに失敗しました。 
* **推奨事項**:Azure Functions ログで遅延が発生していないかどうかを確認してください。

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **原因**:入力のオフセットが無効です。 これはフェールオーバーに起因する可能性があります。
* **推奨事項**:前回の出力の時刻から Stream Analytics ジョブを再開してください。

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **原因**:イベント ハブへのデータの送信中にエラーが発生しました。

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **原因**:最大再試行回数を超えた後、Stream Analytics で Cosmos DB アカウントへの接続に失敗しました。

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **原因**:最大再試行回数を超えた後、Stream Analytics で Cosmos DB データベースおよびコレクションの照会に失敗しました。

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **原因**:複数回再試行した後、CosmosDB でストアド プロシージャを作成できません。

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **原因**:upsert ストアド プロシージャからエラーが返されました。 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **原因**:Stream Analytics で SQL Database 出力を初期化できません。

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **原因**:Stream Analytics で SQL Database 出力にイベントを書き込むことができません。

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **原因**:専用 SQL プール出力を初期化しているときにエラーが発生しました。

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **原因**:専用 SQL プールに出力を書き込んでいるときにエラーが発生しました。

## <a name="next-steps"></a>次のステップ

* [入力接続のトラブルシューティング](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics の出力のトラブルシューティング](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics のクエリのトラブルシューティング](stream-analytics-troubleshoot-query.md)
* [リソース ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics データ エラー](data-errors.md)
