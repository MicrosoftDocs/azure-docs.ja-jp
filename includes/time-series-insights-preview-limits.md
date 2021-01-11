---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 48080bb4d1e24f7f98d3dfe1fd63b65ba46df35e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289903"
---
### <a name="property-limits"></a>プロパティの制限

Azure Time Series Insights のプロパティ制限は、Gen1 の上限である 800 個から 1,000 個に引き上げられました。 提供されているイベント プロパティには、対応する JSON、CSV、およびグラフの列があり、[Azure Time Series Insights Gen2 エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)内で確認できます。

| SKU | 最大のプロパティ |
| --- | --- |
| Gen2 (L1) | 1,000 個のプロパティ (列) |
| Gen1 (S1) | 600 個のプロパティ (列) |
| Gen1 (S2) | 800 個のプロパティ (列) |

### <a name="event-sources"></a>イベント ソース

インスタンスごとに最大 2 つのイベント ソースがサポートされています。

* [イベント ハブ ソースを追加する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)方法を学習します。
* [IoT ハブ ソース](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)を構成します。

既定では、Gen2 環境で、**環境あたり最大で毎秒 1 メガバイト (MB/秒)** の[イングレス レートをサポート](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingress-throughput-limits)できます。 お客様は、必要に応じて、最大 **16 MB/秒**のスループットまで、環境をスケーリングすることができます。 また、パーティションごとに **0.5 MB/秒**の制限があります。

### <a name="api-limits"></a>API の制限

Azure Time Series Insights Gen2 の REST API の制限については、[REST API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)を参照してください。
