---
title: Azure Stream Analytics ジョブでのサービスの中断の回避
description: この記事では、Stream Analytics ジョブのアップグレード時の障害耐性を高めるためのガイダンスについて説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090807"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>サービス更新中におけるStream Analytics ジョブの信頼性を保証する

フル マネージドのサービスの一部は、サービスの新機能と機能強化を迅速なペースで紹介する機能です。 そのため、Stream Analytics は週単位で (またはより頻繁に) サービスの更新をデプロイできます。 どれだけテストが行われても、バグが組み込まれることで実行中である既存のジョブが停止するリスクは依然として存在します。 重要なストリーミング処理ジョブを実行するユーザーは、これらのリスクを回避する必要があります。 このリスクを軽減するために利用できるメカニズムは、Azure の**[ペア リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** モデルです。 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure ペア リージョンによるこの問題への対応方法について

Stream Analytics では、ペア リージョンのジョブは個別のバッチで更新されることが保証されています。 そのため、潜在的な停止バグを認識する更新とその修正との間に十分な時間差が生じます。

_インド中部の例外を除き_ (ペア リージョンであるインド南部では Stream Analytics プレゼンスがない)、Stream analytics に対する更新のデプロイは、1 組のペア リージョン内では同時に実行されません。 **同じグループ内**の複数のリージョンにおけるデプロイは**同時に**起こる可能性があります。

**[可用性およびペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** に関する記事には、どのリージョンがペアになっているかについての最新の情報が記述されています。

両方のペア リージョンに同じジョブをデプロイすることをお勧めします。 Stream Analytics の内部監視機能に加えて、**両方**が運用ジョブであるように監視することをお勧めします。 停止が Stream Analytics サービスの更新の結果起こったと特定された場合、サポートを適切に拡大し、正常なジョブ出力のためにダウンストリームの顧客をフェールオーバーします。 サポートの拡大によってペア リージョンが新たなデプロイによって影響を受けることを防ぎ、ペアになっているジョブの整合性を保ちます。

## <a name="next-steps"></a>次の手順

* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)
