---
title: Azure Stream Analytics ジョブでのサービスの中断の回避
description: この記事では、Stream Analytics ジョブのアップグレード時の障害耐性を高めるためのガイダンスについて説明します。
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: e38f8a923daa210d8aa5b56631e5f8157d4b3f70
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620876"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>サービス更新中におけるStream Analytics ジョブの信頼性を保証する

フル マネージドのサービスの一部は、サービスの新機能と機能強化を迅速なペースで紹介する機能です。 そのため、Stream Analytics は週単位で (またはより頻繁に) サービスの更新をデプロイできます。 どれだけテストが行われても、バグが組み込まれることで実行中である既存のジョブが停止するリスクは依然として存在します。 ミッション クリティカルなジョブを実行している場合は、これらのリスクを回避する必要があります。 次の Azure の **[ペア リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** モデルに従うことで、このリスクを低減できます。 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure ペア リージョンによるこの問題への対応方法について

Stream Analytics では、ペア リージョンのジョブは個別のバッチで更新されることが保証されています。 そのため、潜在的な問題を認識する更新とその修正との間に十分な時間差が生じます。

_インド中部の例外を除き_ (ペア リージョンであるインド南部では Stream Analytics プレゼンスがない)、Stream analytics に対する更新のデプロイは、1 組のペア リージョン内では同時に実行されません。 **同じグループ内**の複数のリージョンにおけるデプロイは**同時に**起こる可能性があります。

**[可用性およびペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** に関する記事には、どのリージョンがペアになっているかについての最新の情報が記述されています。

両方のペア リージョンに同じジョブをデプロイすることが推奨されます。 その後、[これらのジョブを監視](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)して、何らかの予期しない事柄が発生した場合に通知を受け取るようにします。 これらのジョブのいずれかが Stream Analytics サービスの更新後に[失敗状態](https://docs.microsoft.com/azure/stream-analytics/job-states)になった場合は、カスタマー サポートに連絡して、根本原因を識別するための支援を受けることができます。 また、下流のコンシューマーを正常なジョブ出力にフェールオーバーする必要があります。

## <a name="next-steps"></a>次の手順

* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)
