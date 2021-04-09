---
title: スケーラビリティ - Azure Event Hubs | Microsoft Docs
description: この記事では、パーティションとスループット ユニットを使用して Azure Event Hubs をスケーリングする方法について説明します。
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601282"
---
# <a name="scaling-with-event-hubs"></a>Event Hubs によるスケーリング

Event Hubs によるスケーリングに影響する 2 つの要素があります。
*   スループット ユニット
*   パーティション

## <a name="throughput-units"></a>スループット ユニット

Event Hubs のスループット容量は、"*スループット ユニット*" によって制御されます。 スループット ユニットとは、購入済みの容量ユニットのことです。 1 つのスループットでは次が可能です。

* イングレス: 1 秒あたり最大で 1 MB または 1,000 イベント (どちらか先に到達した方)
* エグレス: 1 秒あたり最大で 2 MB または 4,096 イベント

購入済みのスループット ユニットの容量を超えると、イングレスが調整され、[ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) が返されます。 エグレスではスロットル例外は発生しませんが、購入済みのスループット ユニットの容量に制限されます。 発行率の例外を受信するか、より高いエグレスが予想される場合は、名前空間に対して購入したスループット ユニットの数を確認してください。 スループット ユニットは、[Azure Portal](https://portal.azure.com) の名前空間の **[スケール]** ブレードで管理できます。 [Event Hubs API](./event-hubs-samples.md) を使用して、プログラムでスループット ユニットを管理することもできます。

スループット ユニットは事前に購入し、1 時間ごとに課金されます。 スループット ユニットを購入すると、少なくとも 1 時間の料金が課金されます。 Event Hubs の名前空間に対して最大 20 のスループット ユニットを購入でき、その名前空間内のすべてのイベント ハブで共有されます。

Event Hubs の **自動インフレ** 機能は、使用量のニーズに合わせてスループット単位の数を増やすことで、自動的にスケールアップします。 スループット単位を増やすことで、以下の状況で必要になる調整シナリオを防ぐことができます。

- データの受信レートが、設定されたスループット単位を超えている。
- データの送信要求レートが、設定されたスループット単位を超えている。

負荷が最小しきい値を超えていて、ServerBusy エラーなどで失敗した要求がない場合、Event Hubs サービスでスループットが増えます。 

自動インフレ機能の詳細については、[スループット単位の自動的なスケーリング](event-hubs-auto-inflate.md) に関する記事を参照してください。

## <a name="partitions"></a>パーティション
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

- [スループット単位の自動スケール](event-hubs-auto-inflate.md)
- [Event Hubs サービスの概要](./event-hubs-about.md)
