---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/06/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 54796f0c0810afd5559af836c566bf66b430223f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980845"
---
これは一般提供の主な制限事項をまとめた記事です。

### <a name="sku-ingress-rates-and-capacities"></a>SKU のイングレス レートと容量

新しい Time Series Insights 環境を構成する場合、S1 および S2 SKU のイングレス レートと容量には柔軟性があります。

| S1 SKU の容量 | イングレス レート | 最大ストレージ容量
| --- | --- | --- |
| 1 | 1 GB (100 万イベント) | 月あたり 30 GB (3,000 万イベント) |
| 10 | 10 GB (1,000 万イベント) | 月あたり 300 GB (3 億イベント) |

| S2 SKU の容量 | イングレス レート | 最大ストレージ容量
| --- | --- | --- |
| 1 | 10 GB (1,000 万イベント) | 月あたり 300 GB (3 億イベント) |
| 10 | 100 GB (1 億イベント) | 月あたり 3 TB (30 億イベント) |

> [!NOTE]
> 容量は直線的にスケーリングされるので、容量が 2 の S1 SKU であれば、サポートされるイベント受信レートは 1 日あたり 2 GB (200 万)、1 か月あたり 60 GB (6,000 万イベント) となります。

S2 SKU 環境では、サポートされる 1 か月あたりのイベント数が大幅に増え、イングレス容量が大幅に増えました。

| SKU  | 1 か月あたりのイベント数  | 1 か月あたりのイベント サイズ  | 1 分あたりのイベント数 | 1 分あたりのイベント サイズ  |
|---------|---------|---------|---------|---------|
| S1     |   30,000,000     |  30 GB     |  720    |  720 KB   |
 |S2     |   300,000,000    |   300 GB   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>プロパティの制限

GA プロパティの制限は、選択されている SKU 環境によって異なります。 指定されたイベント プロパティには対応する JSON、CSV、およびグラフの列があり、[Time Series Insights エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)内で表示できます。

| SKU | 最大のプロパティ |
| --- | --- |
| S1 | 600 個のプロパティ (列) |
| S2 | 800 個のプロパティ (列) |

### <a name="event-sources"></a>イベント ソース

インスタンスごとに最大 2 つのイベント ソースがサポートされています。 

* [イベント ハブ ソースを追加する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)方法を学習します。
* [IoT ハブ ソース](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)を構成します。

### <a name="api-limits"></a>API の制限

Time Series Insights 一般提供の REST API の制限については、[REST API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)を参照してください。