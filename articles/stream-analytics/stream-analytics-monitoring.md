---
title: "Stream Analytics ジョブ監視について | Microsoft Docs"
description: "Stream Analytics ジョブ監視について"
keywords: "クエリ モニター"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: e9ea21be1fabb5846c4f3b7b05f3a93298830e57
ms.openlocfilehash: 5119d1d6c02b8e080a9e0905ae21a2bddefab03a
ms.lasthandoff: 01/05/2017


---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Stream Analytics ジョブ監視とクエリの監視方法の概要

## <a name="introduction-the-monitor-page"></a>概要: [監視] ページ
Azure ポータルは、クエリとジョブ パフォーマンスの監視とトラブルシューティングに使用できる主要なパフォーマンス メトリックを明らかにします。 これらのメトリックを表示するには、メトリックを確認したい Stream Analytics ジョブを参照し、[概要] ページの **[監視]** 画面を表示します。  

![監視リンク](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

次のようなウィンドウが表示されます。

![監視ジョブ、ダッシュボード](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics に利用できるメトリック
| メトリック                 | 定義                               |
| ---------------------- | ---------------------------------------- |
| SU % 使用率       | ジョブの [スケール] タブからジョブに割り当てられたストリーミング ユニットの使用率。 このインジケーターが 80% 以上に達した場合、イベントの処理が遅れたり、進行が遅延または停止する可能性があります。 |
| 入力イベント           | Stream Analytics のジョブが受信したデータのイベントの量。 イベントが入力ソースに送信されることを検証するために使用できます。 |
| 出力イベント          | Stream Analytics のジョブから出力ターゲットに送信されたデータのイベントの量。 |
| 異常イベント    | イベント順序ポリシーに基づいて、削除された、または調整されたタイムスタンプが付与された、順不同で受信したイベントの数。 誤順序の許容期間の設定の構成により、影響を受けることがあります。 |
| データ変換エラー | Stream Analytics のジョブによって発生した、データ変換エラーの数。 |
| 実行時エラー         | Stream Analytics ジョブの実行中に発生したエラーの合計数。 |
| 遅延入力イベント      | 到着遅延許容期間の設定のイベント順序ポリシーの構成に基づいて、削除された、または調整されたタイムスタンプが付与された、ソースから遅れて到着するイベントの数。 |
| 関数の要求      | Azure Machine Learning 関数に対する呼び出しの数 (存在する場合)。 |
| 失敗した関数の要求 | 失敗した Azure Machine Learning 関数呼び出しの数 (存在する場合)。 |
| 関数のイベント        | Azure Machine Learning 関数に送られたイベントの数 (存在する場合)。 |
| 入力イベントのバイト数      | Stream Analytics ジョブが受信したデータの量 (バイト単位)。 イベントが入力ソースに送信されることを検証するために使用できます。 |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Azure ポータルでの監視のカスタマイズ
グラフのタイプ、表示されるメトリック、および時間範囲を [グラフの編集] 設定で調整できます。 詳細については、「[監視のカスタマイズ方法](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)」をご覧ください。

  ![クエリ モニター、タイム グラフ](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


