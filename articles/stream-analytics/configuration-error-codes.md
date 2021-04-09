---
title: 構成エラー コード - Azure Stream Analytics
description: 構成エラー コードに関する Azure Stream Analytics の問題のトラブルシューティングを行います。
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 6137ceeb0d86b1531adab910175ddbc4722ef858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019518"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Azure Stream Analytics の構成エラー コード

アクティビティ ログとリソース ログを使用して、予期しない動作のデバッグを Azure Stream Analytics ジョブから行うことができます。 この記事では、すべての構成エラー コードについて説明します。 構成エラーは、ジョブの構成または入力と出力の構成に関連しています。

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **原因**:イベント ハブで "*未承認アクセス*" エラーがスローされました。

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **原因**:エポック値が異なるイベント ハブ レシーバーが複数存在します。
* **推奨事項**:Stream Analytics ジョブの実行中に、*Service Bus Explorer* または *EventProcessorHost* アプリケーションが接続されていないことを確認します。

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **原因**:コンシューマー グループのパーティションごとに許可されるレシーバーの最大数に達しているため、Stream Analytics はパーティションに接続できません。
* **推奨事項**:他の Stream Analytics ジョブまたは Service Bus Explorer によって同じコンシューマー グループが使用されていないことを確認します。

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **原因**:スロットリングのため、イベント ハブへのデータの書き込み中にエラーが発生しました。
* **推奨事項**:これが常に発生する場合は、スループットをアップグレードします。

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **原因**:指定されている接続の構成が正しありません。
* **推奨事項**:構成を修正して、ジョブを再起動します。

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **原因**:イベント ハブ ホストに到達できません。
* **推奨事項**:指定したホスト名が正しいことを確認します。

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **原因**:EventHub センダーで、予期しない EventHub パーティション数が検出されました。
* **推奨事項**:EventHub のパーティション数が変更された場合は、Stream Analytics ジョブを再起動します。

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **原因**:Stream Analytics で、特定の Cosmos DB コレクションのパーティション キーをデータベースで検出できませんでした。
* **推奨事項**:Cosmos DB でコレクションに対して有効なパーティション キーが指定されていることを確認します。

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **原因**:パーティション キーがリーフ ノードでも最上位レベルでもない場合にスローされます。

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **原因**:主キー プロパティとして異なる列が選択されている場合、クエリ出力に列の \[id] を含めることはできません。

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **原因**:Stream Analytics で CosmosDB のデータベースが見つかりません。

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **原因**:Stream Analytics で、特定の Cosmos DB コレクションがデータベースに見つかりません。

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **原因**:Cosmos DB によるスロットリングのため、データの書き込み中にエラーが発生しました。
* **推奨事項**:コレクションのパフォーマンス レベルをアップグレードし、データベースのパフォーマンスを調整します。

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **原因**:Stream Analytics ジョブで認証エラーが発生しました。
* **推奨事項**:SQL Database の接続文字列が正しいことを確認します。

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **原因**:Stream Analytics ジョブで認証エラーが発生しました。 
* **推奨事項**:アカウント名が正しく構成されていることと、ジョブのマネージド ID で SQL データベースにアクセスできることを確認します。

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **原因**:Stream Analytics で、特定のテーブルのスキーマ情報が見つかりません。

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **原因**:SQL Database はサポートされていません。
* **推奨事項**:専用 SQL プールを使用します。

## <a name="next-steps"></a>次のステップ

* [入力接続のトラブルシューティング](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics の出力のトラブルシューティング](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics のクエリのトラブルシューティング](stream-analytics-troubleshoot-query.md)
* [リソース ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics データ エラー](data-errors.md)
