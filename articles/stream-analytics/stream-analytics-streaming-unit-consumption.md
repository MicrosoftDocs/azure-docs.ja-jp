---
title: "Azure Stream Analytics: ストリーミング ユニットを効率的に使用できるようにジョブを最適化する | Microsoft Docs"
description: "Azure Stream Analytics でのスケーリングとパフォーマンスのためのクエリのベスト プラクティス"
keywords: "ストリーミング ユニット, クエリ パフォーマンス"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 43dcbd474bc66b194959616611aa109f1cdec230
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---

# <a name="optimize-your-job-to-use-streaming-units-efficiently"></a>ストリーミング ユニットを効率的に使用できるようにジョブを最適化する

Azure Stream Analytics では、ジョブを実行する際のパフォーマンスの "重さ" をストリーミング ユニット (SU) に集約しています。 SU とは、ジョブの実行に消費するコンピューティング リソースのことです。 SU は、CPU、メモリ、および読み取りと書き込みのレートを組み合わせた測定に基づいて、相対的なイベントの処理能力を記述する方法を提供します。 これにより、クエリ ロジックに集中することができ、ストレージ層でのパフォーマンスに関する考慮事項を知っておく必要がなくなります。また、ジョブに手動でメモリを割り当てたり、ジョブを時間内に実行するために必要な CPU コア数を概算したりすることもできます。

## <a name="how-many-sus-are-required-for-a-job"></a>ジョブに必要な SU の数

特定のジョブに必要な SU の数の選択は、入力のパーティション構成と、ジョブで定義されたクエリによって異なります。 **[スケール]** ブレードでは、適切な SU の数を設定することができます。 SU は、必要な量より多めに割り当てておくことをお勧めします。 メモリをより多く割り当てると、Stream Analytics 処理エンジンによって待ち時間とスループットが最適化されます。

一般に、*PARTITION BY* を使用していないクエリであれば、6 個の SU から始めることがベスト プラクティスです。 そのうえで、試行錯誤により最適な数を検討します。具体的には、典型的な量のデータを渡して SU 使用率のメトリックを確認し、SU の数を調整する作業を繰り返すことによって、最適な SU の数を割り出します。

Azure Stream Analytics では、どの処理を開始する場合でも、それに先立って "並べ替えバッファー" と呼ばれる時間枠でイベントが保持されます。 イベントは、並べ替え時間枠内で時間順に並べ替えられます。後続の操作は、これらの時間順に並べ替えられたイベントに対して実行されます。 時間でイベントが並べ替えられるため、オペレーターは規定の時間枠内のすべてのイベントを視覚的に確認できます。 オペレーターは、必要な処理を実行して出力を生成することもできます。 このメカニズムの副作用は、並べ替え時間枠の時間の長さによって処理が遅延することです。 ジョブのメモリ フットプリント (SU の消費量に影響します) は、この並べ替え時間枠のサイズおよびそこに含まれるイベントの数と相関関係にあります。

> [!NOTE]
> ジョブのアップグレード中にリーダーの数が変更されると、一時的な警告が監査ログに書き込まれます。 Stream Analytics ジョブは、これらの一時的な問題から自動的に回復します。

## <a name="common-high-memory-causes-for-high-su-usage-for-running-jobs"></a>ジョブの実行のために SU 使用率が高くなっている際にメモリが高負荷になる一般的な原因

### <a name="high-cardinality-for-group-by"></a>GROUP BY が高基数である

受信イベントの基数は、ジョブのメモリ使用量を決定します。

たとえば、`SELECT count(*) from input group by clustered, tumblingwindow (minutes, 5)` では、**clustered** に関連付けられた数字がクエリの基数です。

高基数を原因とする問題を緩和するには、**PARTITION BY** を使用してパーティションを増やすことでクエリをスケールアウトします。

```
Select count(*) from input
Partition By clusterid
GROUP BY clustered tumblingwindow (minutes, 5)
```

ここでは、*clustered* の数が GROUP BY の基数です。

クエリは、パーティション分割されると、複数のノードに広がります。 その結果、各ノードで受信されるイベントの数が減少するため、並べ替えバッファーのサイズが縮小されます。 イベント ハブ パーティションも、partitionid でパーティション分割する必要があります。

### <a name="high-unmatched-event-count-for-join"></a>JOIN での不一致のイベントが多数ある

JOIN 内の不一致のイベントの数は、クエリのメモリ使用率に影響します。 例として、クリックに結びつく広告のインプレッション数を検索する次のクエリを見てみましょう。

```
SELECT id from clicks INNER JOIN,
impressions on impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10
```

このシナリオでは、多くの広告が表示されていてもクリック数が少ない場合があります。 このような結果が出ると、時間枠内のすべてのイベントをジョブで保持する必要があります。 メモリの消費量は、時間枠のサイズとイベント レートに比例します。 

この状況を緩和するには、PARTITION BY を使用してパーティションを増やし、クエリをスケールアウトします。 

クエリは、パーティション分割されると、複数の処理ノードに広がります。 その結果、各ノードで受信されるイベントの数が減少するため、並べ替えバッファーのサイズが縮小されます。

### <a name="large-number-of-out-of-order-events"></a>順不同のイベントが多数ある 

長い時間枠に順不同のイベントが多数あると、"並び替えバッファー" のサイズがさらに拡大する原因になります。 この状況を緩和するには、PARTITION BY を使用してパーティションを増やし、クエリをスケールします。 クエリは、パーティション分割されると、複数のノードに広がります。 その結果、各ノードで受信されるイベントの数が減少するため、並べ替えバッファーのサイズが縮小されます。 


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics の管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)

