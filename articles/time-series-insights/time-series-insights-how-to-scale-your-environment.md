---
title: お使いの環境をスケーリングする方法 ‐ Azure Time Series Insights | Microsoft Docs
description: Azure portal でお使いの Azure Time Series Insights 環境をスケーリングする方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 0dadf523c5d17ffb91f4fefa71b52d1d1855c978
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570225"
---
# <a name="how-to-scale-your-azure-time-series-insights-gen1-environment"></a>Azure Time Series Insights Gen1 環境をスケーリングする方法

> [!CAUTION]
> これは Gen1 の記事です。

この記事では、[Azure portal](https://portal.azure.com) を使用して Azure Time Series Insights 環境の容量を変更する方法について説明します。 容量は、受信レート、ストレージの容量、および選択された SKU に関連するコストに適用される乗数です。

Azure ポータルを使用して、容量を特定の価格 SKU 内で増減できます。

ただし、価格レベル SKU の変更は許可されません。 たとえば、S1 価格の SKU の環境を S2 に変換したり、その逆方向に変換したりすることはできません。

## <a name="ga-limits"></a>GA の制限

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>環境の容量を変更する

1. Azure portal で、Azure Time Series Insights 環境を見つけて選択します。

1. Azure Time Series Insights 環境のメニューから、 **[ストレージ構成]** を選択します。

   [![Azure Time Series Insights の容量を構成する](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. **容量** のスライダーを調整して、受信レートとストレージ容量の要件を満たす容量を選択します。 **受信レート**、**ストレージの容量**、および **コストの見積もり** が動的に更新されて変化の影響が示されることに注目してください。

   [![容量のスライダーを使用して環境を構成する](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   または、スライダーの右側にあるテキスト ボックスに、容量の乗数の数を入力することもできます。

1. **[保存]** を選択して環境をスケーリングします。 変更がコミットされるまで、一時的に進行状況のインジケーターが表示されます。

1. 新しい容量が、[スロットルを防止するのに十分](time-series-insights-diagnose-and-solve-problems.md)であることを確認します。

## <a name="next-steps"></a>次のステップ

- 詳細については、[Azure Time Series Insights のリテンション期間](time-series-insights-concepts-retention.md)に関するページを確認してください。

- [Azure Time Series Insights のデータ リテンションの構成](time-series-insights-how-to-configure-retention.md)について確認します。

- [環境の計画](time-series-insights-environment-planning.md)について確認します。
