---
title: Azure Time Series Insights プレビューの環境を計画する | Microsoft Docs
description: Azure Time Series Insights プレビューの環境を計画します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 251e95744f57d9b5e42df9bdc3743f4880ff5381
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076998"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Azure Time Series Insights プレビューの環境を計画する

この記事では、Azure Time Series Insights プレビューを使用して、迅速に計画し、開始するためのベスト プラクティスについて説明します。

## <a name="best-practices-for-planning-and-preparation"></a>計画と準備のベスト プラクティス

Time Series Insights の使用を開始するには、次のことを理解しておくことをお勧めします。

* Time Series Insights プレビュー環境をプロビジョニングする際に、取得するもの。
* Time Series ID プロパティと Timestamp プロパティ。
* 新しいタイム シリーズ モデルと独自のモデルの構築方法。
* JSON で効率的にイベントを送信する方法。 
* Time Series Insights のビジネス ディザスター リカバリーのオプション。

Time Series Insights では、従量課金制のビジネス モデルが採用されています。 料金と容量の詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」を参照してください。

## <a name="the-time-series-insights-preview-environment"></a>Time Series Insights プレビュー環境

Time Series Insights プレビュー環境をプロビジョニングする際に、次の 2 つの Azure リソースを作成します。

* Time Series Insights プレビュー環境
* Azure Storage 汎用 V1 アカウント

始めに、3 つの追加項目が必要です。
 
- [タイム シリーズ モデル](./time-series-insights-update-tsm.md) 
- [Time Series Insights に接続されているイベント ソース](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- どちらもモデルにマップされ、有効な JSON 形式である[イベント ソースに送られるイベント](./time-series-insights-send-events.md) 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Time Series ID プロパティと Timestamp プロパティの構成

新しい Time Series Insights 環境を作成するには、タイム シリーズ ID を選択します。 そうすることで、データの論理パーティションとして機能します。 前述のように、タイム シリーズ ID の準備ができていることを確認します。

> [!IMPORTANT]
> タイム シリーズ ID は、*不変*で*後で変更することはできません*。 最終的な選択と初めて使用する前に、それぞれを確認してください。

リソースを一意に区別するために、最大 3 つのキーを選択できます。 詳細については、「[Best practices for choosing a Time Series ID](./time-series-insights-update-how-to-id.md)」(タイム シリーズ ID の選択のベスト プラクティス) および[ストレージとイングレス](./time-series-insights-update-storage-ingress.md)に関するページを参照してください。

Timestamp プロパティも重要です。 イベント ソースを追加する際に、このプロパティを指定できます。 各イベント ソースには、時間の経過と共にイベント ソースを追跡するために使用されるオプションの Timestamp プロパティがあります。 Timestamp の値は大文字と小文字が区別され、各イベント ソースの個々の仕様に書式設定される必要があります。

> [!TIP]
> イベント ソースの書式設定と解析の要件を確認します。

空白のままにすると、イベント ソースのイベント エンキュー時間が、イベント タイムスタンプとして使用されます。 履歴データまたはバッチ処理されたイベントを送信する場合は、既定のイベント エンキュー時間より、Timestamp プロパティをカスタマイズすることが役立ちます。 詳細については、[IoT Hub にイベント ソースを追加する方法](./time-series-insights-how-to-add-an-event-source-iothub.md)に関するページを参照してください。 

## <a name="understand-the-time-series-model"></a>タイム シリーズ モデルについて

Time Series Insights 環境のタイム シリーズ モデルを構成できるようになりました。 新しいモデルにより、IoT データの検索と分析が簡単になります。 時系列データのキュレーション、メンテナンス、およびエンリッチメントが可能になり、コンシューマー対応データ セットの準備に役立ちます。 モデルでは、**タイム シリーズ ID** が使われ、これは、型と呼ばれる変数と階層に一意のリソースを関連付けるインスタンスにマップされます。 新しい[タイム シリーズ モデル](./time-series-insights-update-tsm.md)について参照してください。

モデルは動的であるため、いつでも構築できます。 すばやく開始するため、データを Time Series Insights にプッシュする前に、モデルを構築してアップロードしてください。 モデルを構築するには、[タイム シリーズ モデルの使用](./time-series-insights-update-how-to-tsm.md)に関するページを参照してください。

多くのお客様で、タイム シリーズ モデルは既存の資産モデルや既に設置されている ERP システムにマップされます。 既存のモデルがない場合は、迅速に開始できるように、事前構築済みのユーザー エクスペリエンスが[提供されて](https://github.com/Microsoft/tsiclient)います。 モデルがどのように役立つかを考えるため、[サンプル デモ環境](https://insights.timeseries.azure.com/preview/demo)を参照してください。 

## <a name="shape-your-events"></a>イベントの調整

Time Series Insights にイベントを送信する方法を確認できます。 理想的には、イベントは適切かつ効率的に非正規化されます。

お勧めの方法:

* タイム シリーズ モデルにメタデータを格納します
* タイム シリーズ モード、インスタンス フィールド、およびイベントには、次のような必要な情報のみが含まれます。
  * Time Series ID
  * Timestamp

詳細については、[イベントの調整](./time-series-insights-send-events.md#json)に関するページを参照してください。

## <a name="business-disaster-recovery"></a>ビジネスのディザスター リカバリー

Time Series Insights は、Azure リージョン レベルで冗長性を使用する高可用性サービスです。 これらの固有の機能を使用するために、構成は必要はありません。 さらに、Microsoft Azure Platform には、ディザスター リカバリー機能または複数のリージョンにわたる可用性を備えたソリューションを構築するのに役立つ機能が用意されています。 複数のリージョンにわたるグローバルな高可用性をデバイスまたはユーザーに提供するには、これらの Azure ディザスター リカバリー機能を利用してください。 

事業継続とディザスター リカバリー (BCDR) のための Azure の組み込み機能については、[Azure の事業継続テクニカル ガイダンス](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance)に関する記事を参照してください。 高可用性とディザスター リカバリーを実現するための Azure アプリケーションの戦略に関するアーキテクチャのガイダンスについては、[Azure アプリケーションのディザスター リカバリーと高可用性](https://docs.microsoft.com/azure/architecture/resiliency/index)に関するページを参照してください。

> [!NOTE]
> 
>  Time Series Insights には、組み込みの BCDR がありません。
> 既定で、Azure Storage、Azure IoT Hub、および Azure Event Hubs には共にリカバリーが組み込まれています。

詳細については、次を参照してください。

* [Azure Storage の冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [IoT Hub の高可用性ディザスター リカバリー](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Event Hub ポリシー](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

BCDR を必要とする場合は、回復戦略を実装することもできます。 バックアップ Azure リージョンに 2 つ目の Time Series Insights 環境を作成します。 プライマリ イベント ソースからこのセカンダリ環境に、イベントを送信します。 2 つ目の専用コンシューマー グループとそのイベント ソースの BCDR ガイドラインを使用します。

以下の手順に従って、セカンダリ Time Series Insights 環境を作成して使用します。

1. 2 つ目のリージョンに環境を作成します。 詳細については、[Time Series Insights 環境](./time-series-insights-get-started.md)に関するページを参照してください。
1. イベント ソースの 2 つ目の専用コンシューマー グループを作成します。 そのイベント ソースを新しい環境に接続します。 必ず 2 つ目の専用コンシューマー グループを指定してください。 詳細については、[IoT Hub のドキュメント](./time-series-insights-how-to-add-an-event-source-iothub.md)または[Event Hub のドキュメント](./time-series-insights-data-access.md)を参照してください。
1. 障害インシデントの発生時にプライマリ リージョンが影響を受けた場合は、運用をバックアップの Time Series Insights 環境に経路変更します。

> [!IMPORTANT]
> * フェールオーバーの際に遅延が発生する可能性があることに注意してください。
> * フェールオーバーによって、操作が経路変更されるため、メッセージの処理に瞬間的なスパイクが発生する可能性もあります。
> * 詳細については、[Time Series Insights の待機時間の短縮](./time-series-insights-environment-mitigate-latency.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

- [Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)
- [データ モデリング](./time-series-insights-update-tsm.md)