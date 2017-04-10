---
title: "Azure Event Hubs Dedicated 容量の概要 | Microsoft Docs"
description: "Microsoft Azure Event Hubs Dedicated 容量の概要。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: sethm;babanisa
translationtype: Human Translation
ms.sourcegitcommit: 9c1756d2c2c4dd73c60a71b24bfb4606cab289ed
ms.openlocfilehash: 8e718e74443e3d51dc54edaf667e34875bc67c5d
ms.lasthandoff: 02/21/2017


---

# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated の概要

*Event Hubs Dedicated* 容量は、最も厳しい要件を持つお客様にシングル テナント デプロイを提供します。 フルスケールの Azure Event Hubs では、十分な耐久性を備えたストレージと 1 秒未満の待機時間により、1 秒当たり 200 万件以上のイベントまたは 1 秒当たり最大 2 GB のテレメトリを受信できます。 また、同じシステム上でのリアルタイム処理とバッチ処理により、統合されたソリューションを実現できます。 このプランに含まれる Event Hubs Archive では、1 つのストリームでリアルタイムのパイプラインとバッチ ベースのパイプラインの両方をサポートすることで、ソリューションの複雑さが軽減できます。

次の表では、Event Hubs で提供されるサービス レベルを比較しています。 Standard および Basic のほとんどの機能が従量課金であるのに対し、Event Hubs Dedicated プランは月額固定価格となっています。 Dedicated レベルの機能は Standard プランと同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量で提供されます。 

| 機能 | 基本 | Standard | 専用 |
| --- |:---:|:---:|:---:|
| イングレス イベント | 100 万イベントごとの課金 | 100 万イベントごとの課金 | あり |
| スループット単位 (1 MB/秒イングレス、2 MB/秒エグレス) | 1 時間ごとの課金 | 1 時間ごとの課金 | あり |
| メッセージ サイズ | 256 KB | 256 KB | 1 MB |
| パブリッシャー ポリシー | 該当なし | はい | はい |     
| コンシューマー グループ | 1 - 既定 | 20 | 20 |
| メッセージ リプレイ | はい | あり | はい |
| 最大スループット ユニット | 20 | 20 (～ 100)    | 1 CU≈200 |
| 仲介型接続 | 100 (付属) | 1,000 (付属) | 100,000 (付属) |
| 追加の仲介型接続 | 該当なし | はい | はい |
| メッセージのリテンション期間 | 1 日分が含まれます | 1 日分が含まれます | 最大 7 日分が含まれます |
| アーカイブ (プレビュー) | 該当なし    | 1 時間ごとの課金 | あり |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Event Hubs Dedicated 容量の利点

Event Hubs Dedicated を使用することで実現する利点を次に示します。

* 他のテナントからのノイズがないシングル テナント ホスティング。
* メッセージ サイズを 1 MB に拡大 (Standard および Basic では 256 KB)。
* パフォーマンスを常に再現可能。
* ニーズの急増に対応するための容量を保証。
* 1 ～8 容量ユニット (CU) の範囲でスケーラブル - 1 秒あたり最大 200 万件のイングレス イベントを提供。
  * CU によって Event Hubs Dedicated のスケールを管理。CU ごとに約 200 スループット単位 (TU) を提供可能。
* メンテナンス不要: 負荷分散、OS の更新、セキュリティ パッチ、パーティション分割は Microsoft が管理。
* 月額固定価格。

Event Hubs Dedicated では、Standard プランのスループットの制限もいくつか削除されます。 Basic および Standard レベルのスループット単位では、1 秒あたり 1000 件のイベントまたは TU あたり 1 MBps の受信 (送信量の 2 倍) が提供されます。 Dedicated スケール プランには、イングレス イベントとエグレス イベントの数に制限はありません。 これらの制限は、購入した Event Hubs Dedicated の処理容量によってのみ決定されます。

このサービスは最大規模のテレメトリ ユーザーを対象としており、エンタープライズ契約で提供されます。

## <a name="how-to-onboard"></a>利用を開始する方法

Event Hubs Dedicated プラットフォームは、さまざまなサイズの CU でエンタープライズ契約を通じて一般に提供されます。 CU ごとに約 200 スループット ユニットが提供されます。 CU を追加または削除することによって、1 か月の間にニーズに合わせて容量をスケールアップまたはスケールダウンできます。 Dedicated プランは、お客様に適した柔軟なデプロイを実現するために、Event Hubs 製品チームからより実践的な利用方法が提供されるという点でほかにはないサービスとなっています。 

## <a name="next-steps"></a>次のステップ
Event Hubs Dedicated 容量の詳細については、Microsoft の営業担当者または Microsoft サポートにお問い合わせください。 次のリンク先で、Event Hubs の詳細を確認することもできます。

価格については、次のリンクを参照してください。

- [Event Hubs Dedicated の価格](https://azure.microsoft.com/pricing/details/event-hubs/)。 Microsoft の営業担当者または Microsoft サポートに連絡して、Event Hubs Dedicated 容量の詳細を確認することもできます。
- 「[Event Hubs の FAQ](event-hubs-faq.md)」では、Event Hubs の価格の情報について説明し、よく寄せられる質問のいくつかに回答します。 

