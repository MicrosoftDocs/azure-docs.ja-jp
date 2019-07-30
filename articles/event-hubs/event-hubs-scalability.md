---
title: スケーラビリティ - Azure Event Hubs | Microsoft Docs
description: Azure Event Hubs のスケーリングする方法について説明します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: c46b333f2cc304cc12ddf78670b60940c7bc0db3
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827666"
---
# <a name="scaling-with-event-hubs"></a>Event Hubs によるスケーリング

Event Hubs によるスケーリングに影響する 2 つの要素があります。
*   スループット ユニット
*   パーティション

## <a name="throughput-units"></a>スループット ユニット

Event Hubs のスループット容量は、"*スループット ユニット*" によって制御されます。 スループット ユニットとは、購入済みの容量ユニットのことです。 1 つのスループットでは次が可能です。

* イングレス: 1 秒あたり最大で 1 MB または 1,000 イベント (どちらか先に到達した方)
* エグレス: 1 秒あたり最大で 2 MB または 4,096 イベント

購入済みのスループット ユニットの容量を超えると、イングレスが調整され、[ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) が返されます。 エグレスではスロットル例外は発生しませんが、購入済みのスループット ユニットの容量に制限されます。 発行率の例外を受信するか、より高いエグレスが予想される場合は、名前空間に対して購入したスループット ユニットの数を確認してください。 スループット ユニットは、[Azure Portal](https://portal.azure.com) の名前空間の **[スケール]** ブレードで管理できます。 [Event Hubs API](event-hubs-api-overview.md) を使用して、プログラムでスループット ユニットを管理することもできます。

スループット ユニットは事前に購入し、1 時間ごとに課金されます。 スループット ユニットを購入すると、少なくとも 1 時間の料金が課金されます。 Event Hubs の名前空間に対して最大 20 のスループット ユニットを購入でき、その名前空間内のすべてのイベント ハブで共有されます。

Event Hubs の**自動インフレ**機能は、使用量のニーズに合わせてスループット単位の数を増やすことで、自動的にスケールアップします。 スループット単位を増やすことで、以下の状況で必要になる調整シナリオを防ぐことができます。

- データの受信レートが、設定されたスループット単位を超えている。
- データの送信要求レートが、設定されたスループット単位を超えている。

負荷が最小しきい値を超えていて、ServerBusy エラーなどで失敗した要求がない場合、Event Hubs サービスでスループットが増えます。 

自動インフレ機能の詳細については、[スループット単位の自動的なスケーリング](event-hubs-auto-inflate.md) に関する記事を参照してください。

## <a name="partitions"></a>パーティション
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>パーティション キー

[パーティション キー](event-hubs-programming-guide.md#partition-key)を使用すると、データ編成を目的として受信イベント データを特定のパーティションにマップすることができます。 パーティション キーは、送信者によって指定され、イベント ハブに渡される値です。 これは、パーティション割り当てを作成する静的なハッシュ関数で処理されます。 イベントを発行するときにパーティション キーを指定しないと、ラウンド ロビン割り当てが使用されます。

イベント発行元は、そのパーティション キーのみを認識し、イベントの発行先となるパーティションは認識しません。 このようにキーとパーティションを分離することにより、送信者はダウンストリーム処理について余分な情報を把握しなくてもよくなります。 デバイスごとまたはユーザーの一意の ID は適切なパーティション キーになりますが、地理的条件などのその他の属性を使用して関連するイベントを 1 つのパーティションにまとめることもできます。


## <a name="next-steps"></a>次の手順
Event Hubs の詳細については、次のリンク先を参照してください:

- [スループット単位の自動スケール](event-hubs-auto-inflate.md)
- [Event Hubs サービスの概要](event-hubs-what-is-event-hubs.md)
