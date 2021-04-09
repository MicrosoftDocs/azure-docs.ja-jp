---
title: Azure Event Grid での geo ディザスター リカバリー | Microsoft Docs
description: Azure Event Grid で geo ディザスター リカバリー (GeoDR) が自動的にサポートされる方法について説明します。
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94980851"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure Event Grid でのサーバー側 geo ディザスター リカバリー
Event Grid には、新規だけでなく既存のすべてのドメイン、トピック、およびイベント サブスクリプション用のメタ データの自動 geo ディザスター リカバリー (GeoDR) が追加されました。 Azure リージョン全体が停止しても、ご利用のイベント関連のインフラストラクチャ メタデータはすべて、Event Grid によって既にペアのリージョンに同期されています。 ご利用の新しいイベントは、ご自分が介入しなくても再び流れ始めます。 

ディザスター リカバリーは 2 つのメトリックで測定されます。

- 回復ポイントの目標 (RPO): 失われる可能性がある分または時間分のデータ。
- 回復時刻の目標 (RTO): サービスが停止している可能性がある時間。

Event Grid の自動フェイルオーバーでは、ご利用のメタデータ (イベント サブスクリプションなど) およびデータ (イベント) に対してさまざまな RPO と RTO が用意されています。 以下の仕様とは異なる仕様が必要な場合でも、独自の[クライアント側フェイルオーバーをトピックの正常性 API を使用して実装](custom-disaster-recovery.md)することができます。

## <a name="recovery-point-objective-rpo"></a>目標復旧時点 (RPO)
- **メタデータの RPO**: 0 分 Event Grid でリソースが作成されるたびに、それは即座にリージョン間で複製されます。 フェイルオーバーが発生しても、メタデータは失われません。
- **データの RPO**: ご利用のシステムが正常であり、リージョンのフェールオーバー時に既存のトラフィックで同期された場合、イベントの RPO は約 5 分です。

## <a name="recovery-time-objective-rto"></a>目標復旧時間 (RTO)
- **メタデータの RTO**: 一般的にはきわめて迅速に行われますが、60 分以内に、Event Grid ではトピックとサブスクリプションに対する作成/更新/削除の呼び出しの受け入れが開始されます。
- **データの RTO**:メタデータのように、一般的にはきわめて迅速に行われます。ただし、60 分以内に、Event Grid ではリージョン フェイルオーバー後の新しいトラフィックの受け入れが開始されます。

> [!NOTE]
> Event Grid でのメタデータ GeoDR の費用は $0 です。


## <a name="next-steps"></a>次のステップ
独自のクライアント側フェイルオーバー ロジックを実装する場合は、「[Event Grid のカスタム トピック用に独自のディザスター リカバリーを構築する](custom-disaster-recovery.md)」を参照してください。
