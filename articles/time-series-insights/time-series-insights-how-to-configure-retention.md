---
title: 環境のリテンション期間の構成方法 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 環境のリテンション期間の構成方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91569412"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Azure Time Series Insights Gen1 のリテンション期間の構成

> [!CAUTION]
> これは Gen1 の記事です。

この記事では、Azure Time Series Insights の **データ リテンション期間** と **ストレージ制限を超過したときの動作** の構成方法について説明します。

## <a name="summary"></a>まとめ

各 Azure Time Series Insights 環境には、**データ リテンション期間** を構成するための設定があります。 この値は 1 から 400 日間となっています。 環境の記憶域容量に達したとき、またはリテンション期間 (1 から 400 日) が終了したときのいずれか早い方でデータが削除されます。

Azure Time Series Insights 環境ごとに、 **[ストレージ制限を超過したときの動作]** という追加設定があります。 この設定によって、環境の最大容量に到達したときのイングレスと消去の動作が制御されます。 次の 2 つの動作の選択肢があります。

- **[Purge old data]\(古いデータを消去\)** (既定値)
- **[Pause ingress]\(イングレスを一時停止\)**

これらの設定の詳細については、[Azure Time Series Insights のリテンション期間](time-series-insights-concepts-retention.md)に関するページを確認してください。  

## <a name="configure-data-retention"></a>データ リテンション期間の構成

1. [Azure portal](https://portal.azure.com) にサインインする

1. 既存の Azure Time Series Insights 環境を見つけます。 Azure Portal の左側のメニューにある **[すべてのリソース]** を選択します。 Azure Time Series Insights 環境を選択します。

1. **[設定]** の見出しで、 **[ストレージの構成]** を選択します。

    [![[設定] で [ストレージの構成] を選択する](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. **[データ リテンション期間 (日)]** を選択し、スライダー バーを使用するか、テキスト ボックスに数値を入力してリテンション期間を構成します。

1. **[容量]** の構成は、データ イベントの最大量やデータ格納用の記憶域の最大容量に影響するため、この設定をメモしておきます。

1. **[ストレージ制限を超過したときの動作]** を設定します。 **[Purge old data]\(古いデータを消去\)** と **[Pause ingress]\(イングレスを一時停止\)** のいずれかを選択します。

    [![イングレスの一時停止 - 同意して保存します。](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. データ損失の潜在的なリスクを理解するには、ドキュメントを参照してください。 **[保存]** を選択して、変更した構成を保存します。

## <a name="next-steps"></a>次のステップ

- 詳細については、[Azure Time Series Insights のリテンション期間](time-series-insights-concepts-retention.md)に関するページを確認してください。

- [Azure Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)を確認します。

- [環境の計画](time-series-insights-environment-planning.md)について確認します。
