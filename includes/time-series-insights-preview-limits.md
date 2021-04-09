---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98109336"
---
### <a name="property-limits"></a>プロパティの制限

Azure Time Series Insights プロパティの制限が、ウォーム ストレージの場合は 1,000 個に引き上げられ、コールド ストレージの場合はプロパティの制限はありません。 提供されているイベント プロパティには、対応する JSON、CSV、およびグラフの列があり、[Azure Time Series Insights Gen2 エクスプローラー](../articles/time-series-insights/quickstart-explore-tsi.md)内で確認できます。

| SKU | 最大のプロパティ |
| --- | --- |
| Gen2 (L1) | ウォーム ストレージの場合は 1,000 個のプロパティ (列)、コールド ストレージの場合は無制限|
| Gen1 (S1) | 600 個のプロパティ (列) |
| Gen1 (S2) | 800 個のプロパティ (列) |

### <a name="streaming-ingestion"></a>ストリーミング インジェスト

* 環境ごとの[イベント ソース](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md)の数は、最大で 2 つです。

* イベント ソースのベストプラクティスと一般的なガイダンスについては、[こちら](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)を参照してください

* 既定では、Azure Time Series Insights Gen2 では **Azure Time Series Insights Gen2 環境ごとに最大 1 MB/秒 (MBps)** の速度で受信データを取り込むことができます。 [ハブのパーティションごとに](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)追加の制限があります。 Azure portal を介してサポート チケットを提出することで、最大 2 MBps の速度が提供されます。 詳細については、「[ストリーミング インジェストのスループットの制限](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)」を参照してください。

### <a name="api-limits"></a>API の制限

Azure Time Series Insights Gen2 の REST API の制限については、[REST API リファレンス ドキュメント](/rest/api/time-series-insights/preview#limits-1)を参照してください。