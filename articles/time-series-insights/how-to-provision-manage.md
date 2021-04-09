---
title: Gen 2 環境の管理 - Azure Time Series | Microsoft Docs
description: Azure Time Series Insights Gen 2 環境を管理する方法について説明します。
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461897"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 の管理

[Azure CLI](./how-to-create-environment-using-cli.md) または [Azure portal](./how-to-create-environment-using-portal.md) を使用して Azure Time Series Insights Gen2 環境を作成した後、ビジネス ニーズに合わせてアクセス ポリシーとその他の環境属性を変更できます。

## <a name="manage-the-environment"></a>環境の管理

[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights Gen2 環境を管理できます。 Azure portal を使用して環境を管理する場合、Gen2 環境と Gen1 S1 環境または Gen1 S2 環境との間には、注意すべき重要な違いがいくつかあります。

* Azure portal Gen2 の **[概要]** ペインには、次の変更点があります。

  * 容量は、Gen2 環境には適用されないため、削除されます。
  * **タイム シリーズ ID** プロパティが追加されます。 これによってデータのパーティション方法が決定されます。
  * 参照データ セットが削除されています。
  * 表示された URL から [Azure Time Series Insights エクスプローラー](./concepts-ux-panels.md)に移動できます。
  * お使いの Azure Storage アカウント名が表示されます。

* スケール ユニットは Azure Time Series Insights Gen2 環境には適用されないため、Azure portal の **[構成]** ペインは削除されています。 ただし、 **[ストレージの構成]** を使用して、新しく導入されたウォームストアを構成できます。

* 参照データの概念は [タイム シリーズ モデル (TSM)](./concepts-model-overview.md) に置き換えられたため、Azure portal の **[参照データ]** ペインは Azure Time Series Insights Gen2 では削除されています。

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure portal の Azure Time Series Insights Gen2 環境":::

## <a name="next-steps"></a>次のステップ

* [ストリーミング インジェストのベストプラクティス](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)の一覧を確認する
* [監視、診断、およびトラブルシューティング](./how-to-diagnose-troubleshoot.md)方法を理解する
