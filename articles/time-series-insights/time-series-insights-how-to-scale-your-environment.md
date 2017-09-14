---
title: "Azure Time Series Insights 環境をスケーリングする方法 | Microsoft Docs"
description: "このチュートリアルでは、Azure Time Series Insights 環境をスケーリングする方法について説明します。"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8f6c66ea2173c98179ec899d6626c2ab6f7ec4b6
ms.contentlocale: ja-jp
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Time Series Insights 環境をスケーリングする方法

このチュートリアルでは、Time Series Insights 環境をスケーリングする方法について説明します。

> [!NOTE]
> SKU の種類をまたいでスケールアップすることはできません。 S1 の SKU のある環境を S2 環境に変換することはできません。

## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 SKU の受信レートと容量

| S1 SKU の容量 | 受信レート | 最大ストレージ容量
| --- | --- | --- |
| 1 | 1 GB (100 万イベント) | 月あたり 30 GB (3,000 万イベント) |
| 10 | 10 GB (1,000 万イベント) | 月あたり 300 GB (3 億イベント) |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU の受信レートと容量

| S2 SKU の容量 | 受信レート | 最大ストレージ容量
| --- | --- | --- |
| 1 | 10 GB (1,000 万イベント) | 月あたり 300 GB (3 億イベント) |
| 10 | 100 GB (1 億イベント) | 月あたり 3 TB (30 億イベント) |

容量は直線的にスケーリングされるので、容量が 2 の S1 SKU であれば、サポートされるイベント受信レートは 1 日あたり 2 GB (200 万)、1 か月あたり 60 GB (6,000 万イベント) となります。

## <a name="changing-the-capacity-of-your-environment"></a>環境の容量を変更する

1. Azure Portal で、容量を変更する環境を選択します。
1. [設定] で [構成] をクリックします。
1. 容量のスライダーを使用して、受信レートとストレージ容量の要件を満たす容量を選択します。

## <a name="next-steps"></a>次のステップ

* 新しい容量が、スロットルを防止するのに十分であることを確認します。 詳細については、[こちら](time-series-insights-diagnose-and-solve-problems.md)の「*Your environment might be getting throttled (環境がスロットルされている可能性がある)*」セクションを参照してください。
