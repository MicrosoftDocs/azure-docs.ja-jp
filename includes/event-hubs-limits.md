---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 333f2317fcc834a10b7336bbda9a43ba16a7ad38
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317676"
---
次の表に、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に固有のクォータと制限を示します。 Event Hubs の価格については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

次の制限は、Basic と Standard サービス レベルで共通です。 

| 制限 | Scope | Notes | 値 |
| --- | --- | --- | --- |
| サブスクリプションあたりの Event Hubs 名前空間の数 |サブスクリプション |- |100 |
| 名前空間あたりのイベント ハブの数 |名前空間 |以後、新しいイベント ハブの作成要求は拒否されます。 |10 |
| イベント ハブあたりのパーティションの数 |Entity |- |32 |
| イベント ハブ名の最大サイズ |Entity |- | 256 文字 |
| コンシューマー グループ名の最大サイズ |Entity |- | 256 文字 |
| コンシューマー グループあたりの非エポック受信者の数 |Entity |- |5 |
| 最大スループット ユニット |名前空間 |スループット ユニットの制限を超えると、データが調整され、[サーバー ビジー例外](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)が生成されます。 Standard レベルに対してより多くのスループット ユニットを要求するには、[サポート リクエスト](/azure/azure-portal/supportability/how-to-create-azure-support-request)を申請します。 [追加スループット ユニット](../articles/event-hubs/event-hubs-auto-inflate.md)は、20 単位で購入できます。 |20 |
| 名前空間ごとの承認規則の数 |名前空間|上限を超えると承認規則の作成要求が拒否されます。|12 |
| GetRuntimeInformation メソッドの呼び出し数 | Entity | - | 1 秒あたり 50 | 
| 仮想ネットワーク (VNet) と IP 構成規則の数 | Entity | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic と Standard - クォータと制限
| 制限 | Scope | Notes | Basic | Standard |
| --- | --- | --- | -- | --- |
| Event Hubs イベントの最大サイズ|Entity | &nbsp; | 256 KB | 1 MB |
| イベント ハブあたりのコンシューマー グループの数 |Entity | &nbsp; |1 |20 |
| 名前空間あたりの AMQP 接続の数 |名前空間 |追加の接続に関する後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 |100 |5,000|
| イベント データの最大リテンション期間 |Entity | &nbsp; |1 日 |1 ～ 7 日間 |
|Apache Kafka 対応の名前空間|名前空間 |Event Hubs 名前空間では、Kafka プロトコルを使用してアプリケーションがストリーム配信されます |いいえ | はい |
|キャプチャ |Entity | 有効な場合、同じストリーム上のマイクロバッチ |いいえ |はい |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated - クォータと制限
Event Hubs Dedicated オファリングは固定の月額料金で課金され、最低の使用量は 4 時間です。 Dedicated レベルの機能は Standard プランとすべて同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量と制限で提供されます。 

| 機能 | 制限 |
| --- | ---|
| 帯域幅 |  20 CU |
| 名前空間 | CU あたり 50 |
| Event Hubs |  名前空間あたり 1,000 |
| イングレス イベント | Included |
| メッセージ サイズ | 1 MB |
| メジャー グループ | CU あたり 2,000 |
| コンシューマー グループ | CU あたりの制限なし、イベント ハブあたり 1,000 |
| 仲介型接続 | 100,000 (付属) |
| メッセージのリテンション期間 | 90 日、CU あたり 10 TB を含む |
| キャプチャ | Included |
