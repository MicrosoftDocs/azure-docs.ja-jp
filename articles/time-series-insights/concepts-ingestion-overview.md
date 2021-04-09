---
title: インジェストの概要 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 へのデータ インジェストについて説明します。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 2b76490ab23cb14755f6f90a27ec8b176bb30a11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780027"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Azure Time Series Insights Gen2 のデータ インジェストの概要

Azure Time Series Insights Gen2 環境には、ストリーミング時系列データを収集、処理、格納するための "*インジェスト エンジン*" が含まれています。 データがイベント ソースに到着すると、Azure Time Series Insights Gen2 によってほぼリアルタイムでデータが消費され、格納されます。

[![インジェストの概要](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>インジェストに関するトピック

以下の記事では、推奨されるベスト プラクティスなど、データ処理の詳細が説明されています。

* [イベント ソース](./concepts-streaming-ingestion-event-sources.md)と、イベント ソースのタイムスタンプの選択に関するガイダンスを参照します。

* サポートされている[データ型](./concepts-supported-data-types.md)を確認します

* インジェスト エンジンによって JSON プロパティに一連の[規則](./concepts-json-flattening-escaping-rules.md)がどのように適用され、ストレージ アカウント列が作成されるかを理解します。

* お使いの環境の[スループットの制限](./concepts-streaming-ingress-throughput-limits.md)を確認し、スケールのニーズを計画します。

## <a name="next-steps"></a>次のステップ

* 引き続き、Azure Time Series Insights Gen2 環境の[イベント ソース](./concepts-streaming-ingestion-event-sources.md)について確認します。
