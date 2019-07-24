---
title: Azure Time Series Insights 環境をスケーリングする方法 | Microsoft Docs
description: この記事では、Azure Time Series Insights 環境をスケーリングする方法について説明します。 Azure Portal を使用して、価格 SKU 内の容量を追加または減算します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: f6c2c8c49f34c707c7b0fb4d525337fbfa169a3e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692427"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Time Series Insights 環境をスケーリングする方法

この記事では、Azure Portal を使用して Time Series Insights 環境の容量を変更する方法について説明します。 容量は、受信レート、ストレージの容量、および選択された SKU に関連するコストに適用される乗数です。 

Azure ポータルを使用して、容量を特定の価格 SKU 内で増減できます。 

ただし、価格レベル SKU の変更は許可されません。 たとえば、S1 価格の SKU の環境を S2 に変換したり、その逆方向に変換したりすることはできません。 


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

## <a name="change-the-capacity-of-your-environment"></a>環境の容量を変更する
1. Azure Portal で Time Series Insights 環境を見つけて選択します。 

2. Time Series Insighs 環境のメニューから、**[構成]** を選択します。

   ![configure.png](media/scale-your-environment/configure.png)

3. **容量**のスライダーを調整して、受信レートとストレージ容量の要件を満たす容量を選択します。 **受信レート**、**ストレージの容量**、および**コストの見積もり**が動的に更新されて変化の影響が示されることに注目してください。 

   ![スライダー](media/scale-your-environment/slider.png)

   または、スライダーの右側にあるテキスト ボックスに、容量の乗数の数を入力することもできます。 

4. **[保存]** を選択して環境をスケーリングします。 変更がコミットされるまで、一時的に進行状況のインジケーターが表示されます。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [新しい容量が、スロットルを防止するのに十分であることを確認します](time-series-insights-diagnose-and-solve-problems.md)。
