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
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: fb10effce8b94a6443e1daa8dadaa99111da0d4e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095384"
---
# <a name="streaming-ingestion-throughput-limits"></a>ストリーミング インジェストのスループットの制限

Azure Time Series Insights Gen2 のストリーミング データのイングレスの制限について以下で説明します。

> [!TIP]
> すべての制限の包括的な一覧については、「[Azure Time Series Insights Gen2 環境の計画](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)」を参照してください。

## <a name="per-environment-limitations"></a>環境ごとの制限

一般に、イングレス レートの要因としては、組織内のデバイスの数、イベント出力の頻度、各イベントのサイズがあります。

*  **デバイスの数** × **イベント出力の頻度** × **各イベントのサイズ**。

既定では、Azure Time Series Insights Gen2 では **Azure Time Series Insights Gen2 環境ごとに最大 1 MB/秒 (MBps)** の速度で受信データを取り込むことができます。 [ハブのパーティションごとに](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)追加の制限があります。

> [!TIP]
>
> * 要求により、環境でサポートされる取り込み速度を最大 16 MBps に指定できます。
> * より高いスループットが必要な場合は、Azure portal からサポート チケットを送信してお問い合わせください。
 
* **例 1:**

    Contoso Shipping には、1 分につき 3 回イベントを発生させる 100,000 個のデバイスがあります。 1 つのイベントのサイズは 200 バイトです。 それらは、4 つのパーティションを持つ IoT Hub を Azure Time Series Insights Gen2 イベント ソースとして使用しています。

    * それらの Azure Time Series Insights Gen2 環境のインジェスト率は次のようになります。**100,000 デバイス * 200 バイト/イベント * (3/60 イベント/秒) = 1 MBps**。
    * パーティションあたりのインジェスト率は 0.25 MBps です。
    * Contoso Shipping のインジェスト率は、スケールの制限内になります。

* **例 2:**

    Contoso Fleet Analytics には、毎秒 1 つのイベントを発生させる 60,000 個のデバイスがあります。 それらは、4 つのパーティションを持つイベント ハブを Azure Time Series Insights Gen2 イベント ソースとして使用しています。 1 つのイベントのサイズは 200 バイトです。

    * 環境のインジェスト率は次のようになります。**60,000 デバイス * 200 バイト/イベント * 1 イベント/秒 = 12 MBps**。
    * パーティションあたりの率は 3 MBps です。
    * Contoso Fleet Analytics のインジェスト率は、環境とパーティションの制限を超えています。 Azure portal を介して Azure Time Series Insights Gen2 に要求を送信して、環境のインジェスト率を上げることができます。また、制限内に収まるように、より多くのパーティションを備えたイベント ハブを作成することもできます。

## <a name="hub-partitions-and-per-partition-limits"></a>ハブのパーティションとパーティションごとの制限

Azure Time Series Insights Gen2 環境を計画するときは、Azure Time Series Insights Gen2 に接続するイベント ソースの構成を考慮することが重要です。 Azure IoT Hub と Event Hubs はどちらもパーティションを使用して、イベント処理のための水平スケールを有効にします。 

*パーティション*は、ハブで保持されている順序付けされた一連のイベントです。 パーティション数は、ハブの作成フェーズ中に設定され、変更することはできません。

Event Hubs のパーティション分割のベスト プラクティスについては、「[パーティションはいくつ必要ですか。](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)」を参照してください。

> [!NOTE]
> Azure Time Series Insights Gen2 で使用されるほとんどの IoT Hub には、4 つのパーティションのみが必要です。

Azure Time Series Insights Gen2 環境用の新しいハブを作成する場合でも、既存のハブを使用する場合でも、パーティションごとのインジェスト率を計算して、それが制限内にあるかどうかを判断する必要があります。 

Azure Time Series Insights Gen2 には現在、一般的な **0.5 MBps のパーティションごとの制限**があります。

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

* [IoT Hub のスケール](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [イベント ハブのスケール](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [イベント ハブのパーティション](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>次のステップ

* データ [ストレージ](./concepts-storage.md)について確認します