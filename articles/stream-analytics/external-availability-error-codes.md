---
title: 外部可用性のエラー コード - Azure Stream Analytics
description: 外部可用性のエラー コードに関する Azure Stream Analytics の問題のトラブルシューティングを行います。
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c70a3a2d0630148a077f9c149ba40d48a6b7c0ae
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045282"
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
