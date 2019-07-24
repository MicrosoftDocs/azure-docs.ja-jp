---
title: 専用イベント ハブの概要 - Azure Event Hubs | Microsoft Docs
description: この記事では、イベント ハブのシングル テナント デプロイを提供する専用の Azure Event Hubs の概要について説明します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
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
ms.openlocfilehash: 2a1785b9c749a8c413987974446190aafc08ed3a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105592"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated の概要

*Event Hubs Dedicated* 容量は、最も厳しい要件を持つお客様にシングル テナント デプロイを提供します。 フルスケールの Azure Event Hubs では、十分な耐久性を備えたストレージと 1 秒未満の待機時間により、1 秒当たり 200 万件以上のイベントまたは 1 秒当たり最大 2 GB のテレメトリを受信できます。 また、同じシステム上でのリアルタイム処理とバッチ処理により、統合されたソリューションを実現できます。 このプランに含まれる [Event Hubs Capture](event-hubs-capture-overview.md) では、1 つのストリームでリアルタイムのパイプラインとバッチ ベースのパイプラインの両方をサポートすることで、ソリューションの複雑さが軽減できます。

次の表では、Event Hubs で提供されるサービス レベルを比較しています。 Standard のほとんどの機能が従量課金であるのに対し、Event Hubs Dedicated プランは月額固定価格となっています。 Dedicated レベルの機能は Standard プランとすべて同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量で提供されます。 

| 機能 | Standard | 専用 |
| --- |:---:|:---:|
| イングレス イベント | 100 万イベントごとの課金 | あり |
| スループット単位 (1 MB/秒イングレス、2 MB/秒エグレス) | 1 時間ごとの課金 | あり |
| メッセージ サイズ | 1 MB | 1 MB |
| パブリッシャー ポリシー | はい | はい |   
| コンシューマー グループ | 20 | 20 |
| メッセージ リプレイ | はい | はい |
| 最大スループット ユニット | 20 (～ 100)   | 1 容量ユニット (CU) ≈ 50 |
| 仲介型接続 | 1,000 (付属) | 100,000 (付属) |
| 追加の仲介型接続 | はい | はい |
| メッセージのリテンション期間 | 1 日分が含まれます | 最大 7 日分が含まれます |
| キャプチャ | 1 時間ごとの課金 | あり |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Event Hubs Dedicated 容量の利点

Event Hubs Dedicated を使用することで実現する利点を次に示します。

* 他のテナントからのノイズがないシングル テナント ホスティング。
* パフォーマンスを常に再現可能。
* ニーズの急増に対応するための容量を保証。
* マイクロバッチおよび長期リテンション期間と統合できるように、Event Hubs の[キャプチャ](event-hubs-capture-overview.md)機能が含まれます。
* メンテナンス不要: 負荷分散、OS の更新、セキュリティ パッチ、パーティション分割はサービスが管理。
* 固定の時間単位料金。
* 追加料金なしで最大 7 日間のメッセージ リテンション期間。

Event Hubs Dedicated では、Standard プランのスループットの制限もいくつか削除されます。 Standard レベルのスループット単位では、1 秒あたり 1000 件のイベント、または TU あたり 1 Mbps の受信とその倍量の送信が提供されます。 Dedicated スケール プランには、イングレス イベントとエグレス イベントの数に制限はありません。 これらの制限は、購入したイベント ハブの処理容量によってのみ決定されます。

この予約された専用の環境では、次のようなこのレベル固有の他の機能が提供されます。

* クラスター内の名前空間の数を制御します。
* 各名前空間に対するスループット制限を指定します。
* 各名前空間に含まれる Event Hubs の数を構成します。
* パーティションの数に対する制限を決定します。

このサービスは最大規模のテレメトリ ユーザーを対象としており、マイクロソフトエンタープライズ契約で提供されます。

## <a name="how-to-onboard"></a>利用を開始する方法

CU を追加または削除することによって、1 か月の間にニーズに合わせて容量をスケールアップまたはスケールダウンできます。 Dedicated プランは、お客様に適した柔軟なデプロイを実現するために、Event Hubs 製品チームからより実践的な利用方法が提供されるという点でほかにはないサービスとなっています。 この SKU の利用を始めるには、[課金サポート](https://ms.portal.azure.com/#create/Microsoft.Support)または Microsoft の担当者にお問い合わせください。

## <a name="next-steps"></a>次の手順

Event Hubs Dedicated 容量の詳細については、Microsoft の営業担当者または Microsoft サポートにお問い合わせください。 次のリンク先で、Event Hubs の価格レベルの詳細を確認することもできます。

- [Event Hubs Dedicated の価格](https://azure.microsoft.com/pricing/details/event-hubs/)。 Microsoft の営業担当者または Microsoft サポートに連絡して、Event Hubs Dedicated 容量の詳細を確認することもできます。
- 「[Event Hubs の FAQ](event-hubs-faq.md)」では、Event Hubs の価格の情報について説明し、よく寄せられる質問のいくつかに回答します。 
