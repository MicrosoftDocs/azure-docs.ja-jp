---
title: Visual Studio を使用して Azure Stream Analytics を監視および管理する
description: この記事では、Visual Studio を使って Azure Stream Analytics ジョブを監視および管理する方法について説明します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e0db101e47a5ec8eb88d3b999058e7c8521f0ff9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020283"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Visual Studio を使用して Stream Analytics ジョブを監視および管理する

この記事では、Visual Studio で Stream Analytics ジョブを監視する方法を示します。 Visual Studio の Azure Stream Analytics ツールは、IDE を離れることなく、Azure portal と同様の監視のエクスペリエンスを提供します。 **Script.asaql** から **Azure に送信** するとすぐに、ジョブの監視を開始できます。既存のジョブは、その作成方法に関係なく、監視することができます。 

## <a name="job-summary"></a>ジョブの概要

**[ジョブの概要]** と **[ジョブ メトリック]** は、ジョブの簡単なスナップショットを提供します。 ジョブの状態およびイベント情報をひとめで確認できます。

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>ジョブのメトリックス

**[ジョブの概要]** は、折りたたむことができます。重要なメトリックとともにグラフを表示するには、 **[ジョブ メトリック]** タブをクリックします。 グラフに追加するメトリックの種類にチェックを付け、グラフから削除するメトリックの種類のチェックを外します。

![Visual Studio での Stream Analytics メトリック](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>エラーの監視

**[エラー]** タブをクリックして、エラーを監視することもできます。

![Visual Studio での Stream Analytics エラー](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>サポートを受ける
詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。 

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [Visual Studio の Azure Stream Analytics ツールのインストール](stream-analytics-tools-for-visual-studio-install.md)