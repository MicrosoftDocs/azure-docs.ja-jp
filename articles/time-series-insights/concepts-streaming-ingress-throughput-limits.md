---
title: ストリーミング インジェストのスループットの制限 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 におけるイングレス スループットの制限について説明します。
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 72b78f49ed8b7016b92262de7256492fa6d5d7d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676803"
---
# <a name="streaming-ingestion-throughput-limits"></a>ストリーミング インジェストのスループットの制限

Azure Time Series Insights Gen2 のストリーミング データのイングレスの制限について以下で説明します。

> [!TIP]
> すべての制限の包括的な一覧については、「[Azure Time Series Insights Gen2 環境の計画](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits)」を参照してください。

## <a name="per-environment-limitations"></a>環境ごとの制限

一般に、イングレス レートの要因としては、組織内のデバイスの数、イベント出力の頻度、各イベントのサイズがあります。

* **デバイスの数** × **イベント出力の頻度** × **各イベントのサイズ**。

既定では、Azure Time Series Insights Gen2 では **Azure Time Series Insights Gen2 環境ごとに最大 1 MB/秒 (MBps)** の速度で受信データを取り込むことができます。 [ハブのパーティションごとに](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)追加の制限があります。

> [!TIP]
>
> * リクエストにより、環境でサポートされる取り込み速度を最大 2 MBps に指定できます。
> * より高いスループットが必要な場合は、Azure portal からサポート チケットを送信してお問い合わせください。

* **例 1:**

    Contoso Shipping には、1 分につき 3 回イベントを発生させる 100,000 個のデバイスがあります。 1 つのイベントのサイズは 200 バイトです。 それらは、4 つのパーティションを持つ IoT Hub を Azure Time Series Insights Gen2 イベント ソースとして使用しています。

  * それらの Azure Time Series Insights Gen2 環境のインジェスト率は次のようになります。**100,000 デバイス * 200 バイト/イベント * (3/60 イベント/秒) = 1 MBps**。
    * パーティションのバランスが取れているとすると、パーティションあたりのインジェスト率は 0.25 MBps です。
    * Contoso Shipping のインジェスト率は、スケールの制限内になります。

* **例 2:**

    Contoso Fleet Analytics には、毎秒 1 つのイベントを発生させる 10,000 個のデバイスがあります。 それらは、2 つのパーティションを持つイベント ハブを Azure Time Series Insights Gen2 イベント ソースとして使用しています。 1 つのイベントのサイズは 200 バイトです。

  * 環境のインジェスト率は次のようになります。**10,000 デバイス * 200 バイト/イベント * 1 イベント/秒 = 2 MBps**。
    * パーティションのバランスが取れているとすると、パーティションあたりの率は 1 MBps になります。
    * Contoso Fleet Analytics のインジェスト率は、環境とパーティションの制限を超えています。 Azure portal で Azure Time Series Insights Gen2 に要求を送信して、環境のインジェスト率を上げることができます。また、制限内に収まるように、より多くのパーティションを備えたイベント ハブを作成することもできます。

## <a name="hub-partitions-and-per-partition-limits"></a>ハブのパーティションとパーティションごとの制限

Azure Time Series Insights Gen2 環境を計画するときは、Azure Time Series Insights Gen2 に接続するイベント ソースの構成を考慮することが重要です。 Azure IoT Hub と Event Hubs はどちらもパーティションを使用して、イベント処理のための水平スケールを有効にします。

*パーティション* は、ハブで保持されている順序付けされた一連のイベントです。 パーティション数は、ハブの作成フェーズ中に設定され、変更することはできません。

Event Hubs のパーティション分割のベスト プラクティスについては、「[パーティションはいくつ必要ですか。](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)」を参照してください。

> [!NOTE]
> Azure Time Series Insights Gen2 で使用されるほとんどの IoT Hub には、4 つのパーティションのみが必要です。

Azure Time Series Insights Gen2 環境用の新しいハブを作成する場合でも、既存のハブを使用する場合でも、パーティションごとのインジェスト率を計算して、それが制限内にあるかどうかを判断する必要があります。

Azure Time Series Insights Gen2 には現在、一般的な **0.5 MBps のパーティションごとの制限** があります。

### <a name="iot-hub-specific-considerations"></a>IoT Hub 固有の考慮事項

デバイスは、IoT Hub に作成されるとパーティションに永続的に割り当てられます。 これにより、IoT Hub ではイベントの順序を保証できます (この割り当ては変更されないため)。

固定パーティション割り当ては、IoT Hub のダウンストリームから送信されるデータを取り込んでいる Azure Time Series Insights Gen2 インスタンスにも影響します。 複数のデバイスからのメッセージが同じゲートウェイ デバイス ID を使用してハブに転送されると、それらは同じパーティションに同時に到着することがあるため、パーティションごとのスケール制限を超える可能性があります。

**影響**:

* 1 つのパーティションで制限を超えるインジェスト率が継続的に発生している場合は、IoT Hub のデータ保有期間を超える前に Azure Time Series Insights Gen2 がすべてのデバイス テレメトリを同期しない可能性があります。 その結果、インジェストの制限を一貫して超えている場合、送信されたデータが失われる可能性があります。

この状況を軽減するために、次のベスト プラクティスが推奨されます。

* ソリューションをデプロイする前に、環境ごとおよびパーティションごとのインジェスト率を計算します。
* IoT Hub のデバイスを可能な限り負荷分散するようにします。

> [!IMPORTANT]
> IoT Hub をイベント ソースとして使用している環境では、使用中のハブ デバイスの数を使ってインジェスト率を計算し、パーティションあたりの制限が確実に 0.5 MBps 未満になるようにします。
>
> * 複数のイベントが同時に到着した場合でも、制限を超えなくなります。

  ![IoT Hub パーティションのダイアグラム](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

ハブのスループットとパーティションの最適化の詳細については、次のリソースを参照してください。

* [IoT Hub のスケール](../iot-hub/iot-hub-scaling.md)
* [イベント ハブのスケール](../event-hubs/event-hubs-scalability.md#throughput-units)
* [イベント ハブのパーティション](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>次のステップ

* データ [ストレージ](./concepts-storage.md)について確認します
