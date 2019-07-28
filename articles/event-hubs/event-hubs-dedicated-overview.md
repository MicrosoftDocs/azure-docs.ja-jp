---
title: 専用イベント ハブの概要 - Azure Event Hubs | Microsoft Docs
description: この記事では、イベント ハブのシングル テナント デプロイを提供する専用の Azure Event Hubs の概要について説明します。
services: event-hubs
documentationcenter: na
author: shubhavijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 52e092e6e48f004656860cb5d078e780039584ab
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730239"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated の概要

*Event Hubs クラスター*は、最も厳しいストリーミングのニーズを持つお客様にシングルテナント デプロイを提供します。 このシングルテナントのサービスでは、99.99% の SLA が保証されており、専用の価格レベルでのみご利用いただけます。 Event Hubs クラスターでは、1 秒あたり数百万のイベントを、保証された容量と 1 秒未満の待ち時間でイングレスすることができます。 専用クラスター内で作成された名前空間とイベント ハブには、標準サービスのすべての機能とそれ以外の機能が含まれますが、イングレスに関する制限はありません。 また、一般的な [Event Hubs Capture](event-hubs-capture-overview.md) 機能が追加料金なしで含まれているため、Azure Storage または Azure Data Lake に対してデータ ストリームを自動的にバッチおよびログ処理することができます。 

クラスターは、事前に割り当てられた CPU とメモリ リソースの量である**容量ユニット (CU)** を基準にプロビジョニングおよび請求が行われます。 クラスターごとに 1、2、4、8、12、16、または 20 の CU を購入することができます。 CU 単位でどれだけの容量を取り込んでストリーミングできるかは、プロデューサーとコンシューマーの数、ペイロードの形態、エグレス レートなどのさまざまな要因によって異なります (詳しくは、後述するベンチマークの結果をご覧ください)。 

> [!NOTE]
> すべての Event Hubs クラスターは既定で Kafka に対応しており、既存の Kafka ベースのアプリケーションで使用できる Kafka エンドポイントをサポートしています。 クラスターで Kafka を有効にしても、Kafka 以外のユース ケースには影響しません。クラスターで Kafka を無効にするオプションはなく、無効にする必要もありません。

## <a name="why-dedicated"></a>専用にする理由

専用 Event Hubs では、エンタープライズ レベルの容量を必要とするお客様に 3 つの魅力的なベネフィットが提供されます。

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>シングル テナントではパフォーマンス向上のための容量が保証される

専用クラスターでは、完全なスケールで容量が保証され、トラフィックのバーストに対応する十分な耐久性を備えたストレージと 1 秒未満の待機時間によりギガバイトのストリーミング データまでイングレスできます。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>機能への包括アクセスと排他アクセス 
専用オファリングには、追加コストのないキャプチャや、BYOK のような今後の機能への排他アクセスなどの機能が含まれています。 また、サービスではお客様のために負荷分散、OS 更新、セキュリティ パッチ、パーティション分割が管理され、お客様はインフラストラクチャのメンテナンスの時間を減らし、クライアント側機能の構築にかける時間を増やすことができます。  

#### <a name="cost-savings"></a>コスト削減
高いイングレス量 (> 100 TU) により、Standard オファリングで同等の量のスループット ユニットを購入するより、クラスターのコストが大幅に減ります。


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated のクォータと制限

Event Hubs Dedicated オファリングは固定の月額料金で課金され、最低の使用量は 4 時間です。 Dedicated レベルの機能は Standard プランとすべて同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量と制限で提供されます。 

| 機能 | Standard | 専用 |
| --- |:---:|:---:|
| 帯域幅 | 20 TU (最大 40 TU) | 20 CU |
| 名前空間 |  1 | CU あたり 50 |
| Event Hubs |  名前空間あたり 10 | 名前空間あたり 1,000 |
| イングレス イベント | 100 万イベントごとの課金 | あり |
| メッセージ サイズ | 100 万バイト | 100 万バイト |
| パーティション | 名前空間あたり 40 | CU あたり 2,000 |
| コンシューマー グループ | イベント ハブあたり 20 | CU あたりの制限なし、イベント ハブあたり 1,000 |
| 仲介型接続 | 1,000 (付属)、最大 5,000 | 100,000 (付属、最大) |
| メッセージのリテンション期間 | 7 日、TU あたり 84 GB を含む | 90 日、CU あたり 10 TB を含む |
| キャプチャ | 1 時間ごとの課金 | あり |

## <a name="how-to-onboard"></a>利用を開始する方法

[Azure portal](https://aka.ms/eventhubsclusterquickstart) を使用して [Event Hubs クラスター](event-hubs-dedicated-cluster-create-portal.md)を作成するセルフサービス エクスペリエンスは、プレビュー段階になりました。 不明な点がある場合や、Event Hubs Dedicated へのオンボードについてサポートが必要な場合は、[Event Hubs チーム](mailto:askeventhubs@microsoft.com)にお問い合わせください。

## <a name="faqs"></a>FAQ

#### <a name="what-can-i-achieve-with-a-cluster"></a>クラスターでは何ができますか?

Event Hubs クラスターの場合、どれだけの容量を取り込んでストリーミングできるかは、プロデューサー、コンシューマー、取り込みや処理の速度などのさまざまな要因によって異なります。 

次の表は、当社でのテスト中に実現したベンチマーク結果を示しています。

| ペイロードの形態 | 受信者 | イングレス帯域幅| イングレス メッセージ | エグレス帯域幅 | エグレス メッセージ | 合計 TU 数 | CU あたりの TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB のバッチ | 2 | 400 MB/秒 | 400,000 メッセージ/秒 | 800 MB/秒 | 800,000 メッセージ/秒 | 400 TU | 100 TU | 
| 10x10KB のバッチ | 2 | 666 MB/秒 | 66,600 メッセージ/秒 | 1.33 GB/秒 | 133,000 メッセージ/秒 | 666 TU | 166 TU |
| 6x32KB のバッチ | 1 | 1.05 GB/秒 | 34,000 メッセージ/秒 | 1.05 GB/秒 | 34,000 メッセージ/秒 | 1000 TU | 250 TU |

このテストでは、次の条件が使用されました。

- 4 容量ユニット (CU) を持つ専用レベルの Event Hubs クラスターが使用されました。 
- 取り込みに使用されたイベント ハブには 200 パーティションが存在しました。 
- 取り込まれたデータは、すべてのパーティションから受信している 2 つの受信側アプリケーションによって受信されました。

#### <a name="can-i-scale-updown-my-cluster"></a>クラスターをスケールアップ/ダウンできますか?

作成後、クラスターは 4 時間以上の使用に対して課金されます。 セルフサービス エクスペリエンスのプレビュー リリースでは、 *[技術] > [クォータ] > [Request to Scale Up or Scale Down Dedicated Cluster]\(Dedicated Cluster のスケールアップまたはスケールダウンを要求する\)* でクラスターをスケールアップまたはスケールダウンする[サポート リクエスト](https://ms.portal.azure.com/#create/Microsoft.Support)を Event Hubs チームに送ることができます。 クラスターのスケールダウンが要求するまで、最大で 7 日かかる場合があります。 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>クラスターでは geo DR はどのようにどうしますか?

Dedicated レベルのクラスターの名前空間を、Dedicated レベルのクラスターの別の名前空間と、geo ペアリングすることができます。 Dedicated レベルの名前空間と Standard オファリングの名前空間のペアリングは、スループットの制限に互換性がないためエラーが発生するので、お勧めしません。 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Standard の名前空間を Dedicated レベルのクラスターに属するように移行できますか?
Standard 名前空間から Dedicated 名前空間にイベント ハブ データを移行する自動移行プロセスを、現在はサポートされていません。 

## <a name="next-steps"></a>次の手順

Event Hubs Dedicated の詳細については、Microsoft の営業担当者または Microsoft サポートにお問い合わせください。 次のリンク先で、クラスターを作成することや、Event Hubs の価格レベルの詳細を確認することもできます。

- [Azure portal を使用して Event Hubs クラスターを作成する](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated の価格](https://azure.microsoft.com/pricing/details/event-hubs/)。 Microsoft の営業担当者または Microsoft サポートに連絡して、Event Hubs Dedicated 容量の詳細を確認することもできます。
- 「[Event Hubs の FAQ](event-hubs-faq.md)」では、Event Hubs の価格の情報について説明し、よく寄せられる質問のいくつかに回答します。
