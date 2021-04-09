---
title: 外部可用性のエラー コード - Azure Stream Analytics
description: 外部可用性のエラー コードに関する Azure Stream Analytics の問題のトラブルシューティングを行います。
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 71625c4b81fc0795c376a89397e98659f4c72ff0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020555"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Azure Stream Analytics の外部可用性エラー コード

アクティビティ ログとリソース ログを使用して、予期しない動作のデバッグを Azure Stream Analytics ジョブから行うことができます。 この記事では、すべての外部可用性エラー コードについて説明します。 外部可用性エラーは、依存サービスを使用できない場合に発生します。

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **原因**:サービスが一時的に利用できません。
* **推奨事項**:Stream Analytics は引き続きサービスへの接続を試みます。

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **原因**:EventHub との通信中に、Stream Analytics でエラーが発生しました。 


## <a name="next-steps"></a>次のステップ

* [入力接続のトラブルシューティング](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics の出力のトラブルシューティング](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics のクエリのトラブルシューティング](stream-analytics-troubleshoot-query.md)
* [リソース ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics データ エラー](data-errors.md)
