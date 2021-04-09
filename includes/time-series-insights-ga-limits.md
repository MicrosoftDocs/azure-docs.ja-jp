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
ms.openlocfilehash: e584b6eff16636f0657c586f6c630dbf8bbb99b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026364"
---
次に、Azure Time Series Insights Gen1 の主な制限の概要を示します。

### <a name="sku-ingress-rates-and-capacities"></a>SKU のイングレス レートと容量

新しい Azure Time Series Insights 環境を構成する場合、S1 および S2 SKU のイングレス レートと容量には柔軟性があります。 SKU の容量は、格納されているイベント数またはバイト数のいずれか早い方に基づいて、1 日のイングレス レートを示します。 イングレスは *分単位* で測定され、トークン バケット アルゴリズムを使用して **スロットリング** が適用される点に留意してください。 イングレスは 1 KB のブロック単位で測定されます。 たとえば、0.8 KB の実際のイベントは 1 つのイベントとして測定され、2.6 KB のイベントは 3 つのイベントとしてカウントされます。

| S1 SKU の容量 | イングレス レート | 最大ストレージ容量
| --- | --- | --- |
| 1 | 1 日あたり 1 GB (100 万イベント) | 月あたり 30 GB (3,000 万イベント) |
| 10 | 1 日あたり 10 GB (1,000 万イベント) | 月あたり 300 GB (3 億イベント) |

| S2 SKU の容量 | イングレス レート | 最大ストレージ容量
| --- | --- | --- |
| 1 | 1 日あたり 10 GB (1,000 万イベント) | 月あたり 300 GB (3 億イベント) |
| 10 | 1 日あたり 100 GB (1 億イベント) | 月あたり 3 TB (30 億イベント) |

> [!NOTE]
> 容量は直線的にスケーリングされるので、容量が 2 の S1 SKU であれば、サポートされるイベント受信レートは 1 日あたり 2 GB (200 万)、1 か月あたり 60 GB (6,000 万イベント) となります。

S2 SKU 環境では、サポートされる 1 か月あたりのイベント数が大幅に増え、イングレス容量が大幅に増えました。

| SKU  | 1 か月あたりのイベント数  | 1 分あたりのイベント数 | 1 分あたりのイベント サイズ  |
|---------|---------|---------|---------|---------|
| S1     |   30,000,000   |  720    |  720 KB   |
 |S2     |   300,000,000   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>プロパティの制限

Gen1 プロパティの制限は、選択されている SKU 環境によって異なります。 指定されたイベント プロパティには、対応する JSON、CSV、およびグラフの列があり、[Azure Time Series Insights エクスプローラー](../articles/time-series-insights/time-series-quickstart.md)内で表示できます。

| SKU | 最大のプロパティ |
| --- | --- |
| S1 | 600 個のプロパティ (列) |
| S2 | 800 個のプロパティ (列) |

### <a name="event-sources"></a>イベント ソース

インスタンスごとに最大 2 つのイベント ソースがサポートされています。

* [イベント ハブ ソースを追加する](../articles/time-series-insights/how-to-ingest-data-event-hub.md)方法を学習します。
* [IoT ハブ ソース](../articles/time-series-insights/how-to-ingest-data-iot-hub.md)を構成します。

### <a name="api-limits"></a>API の制限

Azure Time Series Insights Gen1 の REST API の制限については、[REST API リファレンス ドキュメント](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)を参照してください。