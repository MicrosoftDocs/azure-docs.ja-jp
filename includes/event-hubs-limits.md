---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c163e3cce862640d43f8696dca4eeef29f2ae12a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912420"
---
次の表に、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に固有のクォータと制限を示します。 Event Hubs の価格については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

| 制限 | Scope (スコープ) | メモ | 値 |
| --- | --- | --- | --- |
| サブスクリプションあたりの Event Hubs 名前空間の数 |Subscription |- |100 |
| 名前空間あたりのイベント ハブの数 |名前空間 |以後、新しいイベント ハブの作成要求は拒否されます。 |10 |
| イベント ハブあたりのパーティションの数 |エンティティ |- |32 |
| Event Hubs イベントの最大サイズ|エンティティ |- |1 MB |
| イベント ハブ名の最大サイズ |エンティティ |- |50 文字 |
| コンシューマー グループあたりの非エポック受信者の数 |エンティティ |- |5 |
| 最大スループット ユニット |名前空間 |スループット ユニットの制限を超えると、データが調整され、[サーバー ビジー例外](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)が生成されます。 Standard レベルに対してより多くのスループット ユニットを要求するには、[サポート リクエスト](/azure/azure-supportability/how-to-create-azure-support-request)を申請します。 [追加スループット ユニット](../articles/event-hubs/event-hubs-auto-inflate.md)は、20 単位で購入できます。 |20 |
| 名前空間ごとの承認規則の数 |名前空間|上限を超えると承認規則の作成要求が拒否されます。|12 |
| GetRuntimeInformation メソッドの呼び出し数 | エンティティ | - | 1 秒あたり 50 | 
| 仮想ネットワーク (VNet) と IP 構成規則の数 | エンティティ | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic と Standard - クォータと制限
| 制限 | Scope (スコープ) | メモ | Basic | Standard |
| --- | --- | --- | -- | --- |
| イベント ハブあたりのコンシューマー グループの数 |エンティティ | - |1 |20 |
| 名前空間あたりの AMQP 接続の数 |名前空間 |追加の接続に関する後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 |100 |5,000|
| イベント データの最大リテンション期間 |エンティティ | - |1 日 |1 ～ 7 日間 |
|Apache Kafka 対応の名前空間|名前空間 |Event Hubs 名前空間では、Kafka プロトコルを使用してアプリケーションがストリーム配信されます |いいえ | はい |
|キャプチャ |エンティティ | 有効な場合、同じストリーム上のマイクロバッチ |いいえ |はい |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated - クォータと制限
Event Hubs Dedicated オファリングは固定の月額料金で課金され、最低の使用量は 4 時間です。 Dedicated レベルの機能は Standard プランとすべて同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量と制限で提供されます。 

| 機能 | 制限 |
| --- | ---|
| 帯域幅 |  20 CU |
| 名前空間 | CU あたり 50 |
| Event Hubs |  名前空間あたり 1,000 |
| イングレス イベント | あり |
| メッセージ サイズ | 100 万バイト |
| パーティション | CU あたり 2,000 |
| コンシューマー グループ | CU あたりの制限なし、イベント ハブあたり 1,000 |
| 仲介型接続 | 100,000 (付属) |
| メッセージのリテンション期間 | 最大 7 日間 (90日間に近日対応予定)、CU ごとに 10 TB を含む |
| キャプチャ | あり |
