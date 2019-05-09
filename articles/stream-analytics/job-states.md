---
title: Azure Stream Analytics のジョブの状態
description: この記事では、Stream Analytics ジョブのさまざまな状態について説明します
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961586"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics のジョブの状態

Stream Analytics ジョブは、常に 4 つの状態のいずれかに当てはまります。 ジョブの状態は、Azure portal の Stream Analytics ジョブの概要ページで確認できます。 

| 状態 | 説明 | 推奨アクション |
| --- | --- | --- |
| **実行中** | ジョブは Azure 上で実行されており、定義された入力ソースからのイベントを読み取って、それらを処理し、構成済みの出力シンクに結果を書き込んでいます。 | [主要メトリック](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)を監視して、ジョブのパフォーマンスを追跡することをお勧めします。 |
| **Stopped** | ジョブは停止し、イベントを処理していません。 | NA | 
| **デグレード** | 一時的なエラーによってジョブが影響を受けている可能性があります。 Stream Analytics は、このようなエラーからすぐに回復し、(数分内に) 実行中の状態に戻ろうと試行します。 これらのエラーの原因としては、ネットワークの問題、他の Azure リソースの可用性、逆シリアル化エラーなどが考えられます。ジョブがデグレード状態の時には、ジョブのパフォーマンスが低下する可能性があります。| [診断ログやアクティビティ ログ](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)を見て、これらの一時的エラーの原因に関する詳細を調べることができます。 逆シリアル化エラーなどの場合には、イベントが無効にならないよう、是正措置を実行することをお勧めします。 ジョブのリソース使用率が何度も制限値に達している場合は、SU の数を増やすか、[ジョブを並列化する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)ことをお試しください。 ユーザーが是正措置を実行できない場合には、Stream Analytics が*実行中*の状態に回復しようと試みます。  |
| **失敗** | ジョブで重大なエラーが発生し、失敗の状態になりました。 イベントは読み取られず、処理もされていません。 ジョブが失敗の状態になる一般的な原因は、ランタイム エラーです。 | [アラートを構成](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal)すると、ジョブが失敗状態になった際に通知を受けることができます。 <br> <br>[アクティビティ ログや診断ログ](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)を使用してデバッグを行うことにより、根本原因を特定し、問題に対処することができます。|

## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics ジョブのアラートを設定する](stream-analytics-set-up-alerts.md)
* [Stream Analytics で使用できるメトリック](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [アクティビティ ログと診断ログを使用したトラブルシューティング](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
