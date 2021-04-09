---
title: Azure Stream Analytics ジョブでのサービスの中断の回避
description: この記事では、Stream Analytics ジョブのアップグレード時の障害耐性を高めるためのガイダンスについて説明します。
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: f4eda779b1bc719706f9eb42cf805a7d5ce864aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020334"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>サービス更新中におけるStream Analytics ジョブの信頼性を保証する

フル マネージドのサービスの一部は、サービスの新機能と機能強化を迅速なペースで紹介する機能です。 そのため、Stream Analytics は週単位で (またはより頻繁に) サービスの更新をデプロイできます。 どれだけテストが行われても、バグが組み込まれることで実行中である既存のジョブが停止するリスクは依然として存在します。 ミッション クリティカルなジョブを実行している場合は、これらのリスクを回避する必要があります。 次の Azure の **[ペア リージョン](../best-practices-availability-paired-regions.md)** モデルに従うことで、このリスクを低減できます。 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure ペア リージョンによるこの問題への対応方法について

Stream Analytics では、ペア リージョンのジョブは個別のバッチで更新されることが保証されています。 そのため、潜在的な問題を認識する更新とその修正との間に十分な時間差が生じます。

_インド中部の例外を除き_ (ペア リージョンであるインド南部では Stream Analytics プレゼンスがない)、Stream analytics に対する更新のデプロイは、1 組のペア リージョン内では同時に実行されません。 **同じグループ内** の複数のリージョンにおけるデプロイは **同時に** 起こる可能性があります。

**[可用性およびペアになっているリージョン](../best-practices-availability-paired-regions.md)** に関する記事には、どのリージョンがペアになっているかについての最新の情報が記述されています。

両方のペア リージョンに同じジョブをデプロイすることが推奨されます。 その後、[これらのジョブを監視](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)して、何らかの予期しない事柄が発生した場合に通知を受け取るようにします。 これらのジョブのいずれかが Stream Analytics サービスの更新後に[失敗状態](./job-states.md)になった場合は、カスタマー サポートに連絡して、根本原因を識別するための支援を受けることができます。 また、下流のコンシューマーを正常なジョブ出力にフェールオーバーする必要があります。

## <a name="next-steps"></a>次のステップ

* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics 管理 REST API リファレンス](/rest/api/streamanalytics/)