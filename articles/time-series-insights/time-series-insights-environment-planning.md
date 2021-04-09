---
title: Gen1 の環境を計画する - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen1 環境を準備、構成、およびデプロイするためのベスト プラクティス。
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 5e0f1ea42aa2ba888b89dd652d3397a3a2163a3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95016209"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>Azure Time Series Insights Gen1 環境の計画

> [!CAUTION]
> これは Gen1 の記事です。

この記事では、予想されるイングレス レートとデータ リテンション期間の要件に基づいて、Azure Time Series Insights Gen1 環境を計画する方法について説明します。

## <a name="video"></a>ビデオ

**Azure Time Series Insights でのデータ リテンション期間と、その計画方法について詳細を確認するには、以下のビデオをご覧ください**。<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>ベスト プラクティス

Azure Time Series Insights の使用を開始するには、分単位でプッシュすることが予想されるデータの量と、データを保存する必要がある期間を把握しておくことをお勧めします。  

Azure Time Series Insights の 2 つの SKU の容量とリテンション期間の詳細については、「[Azure Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」をご覧ください。

長期的な成功に向けて Azure Time Series Insights 環境を最善に計画するには、次の属性を検討します。

- [[ストレージ容量]](#storage-capacity)
- [データ リテンション期間](#data-retention)
- [イングレス容量](#ingress-capacity)
- [イベントの整形](#shape-your-events)
- [参照データが配置されていることを確認](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>ストレージの容量

既定では、Azure Time Series Insights は、プロビジョニングするストレージの容量 (ユニット数 &#215; ユニットごとのストレージ容量) とイングレスに基づいてデータを保持します。

## <a name="data-retention"></a>データの保持

お使いの Azure Time Series Insights 環境内で **[データ リテンション期間]** 設定を変更できます。 リテンション期間は最大 400 日まで有効にできます。

Azure Time Series Insights には 2 つのモードがあります。

- 1 つのモードでは、最新のデータに対して最適化されます。 **古いデータを消去する** ポリシーが強制され、最近のデータをインスタンスで利用できるように残します。 このモードは、既定でオンになっています。
- もう 1 つのモードでは、構成されているリテンション制限を下回るようにデータが最適化されます。 **[Pause ingress]\(イングレスを一時停止\)** が **ストレージ上限の超過動作** として選択されているとき、新しいデータのイングレスが防止されます。

Azure portal 内の環境の構成ページ上で、リテンション期間を調整し、2 つのモードを切り替えることができます。

> [!IMPORTANT]
> Azure Time Series Insights Gen1 環境では、最大 400 日のデータ リテンション期間を構成できます。

### <a name="configure-data-retention"></a>データ リテンション期間の構成

1. [Azure Portal](https://portal.azure.com) で、Time Series Insights 環境を選択します。

1. **[Time Series Insights 環境]** ウィンドウで、 **[設定]** 下の **[ストレージの構成]** を選択します。

1. **[データ リテンション期間 (日)]** ボックスに、1 から 400 までの値を入力します。

   [![リテンション期間の構成](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 適切なデータ リテンション ポリシーの実装方法について詳しくは、[リテンション期間の構成方法](./time-series-insights-how-to-configure-retention.md)に関する記事をご覧ください。

## <a name="ingress-capacity"></a>イングレス容量

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>環境の計画

Azure Time Series Insights 環境を計画する場合に注目すべき 2 つ目の分野は、イングレス容量です。 1 日あたりのイングレス ストレージとイベント容量は、1 KB ブロック単位で 1 分ごとに測定されます。 最大許容パケット サイズは 32 KB です。 32 KB を超えるデータ パケットは切り捨てられます。

1 つの環境で、S1 SKU または S2 SKU の容量を 10 ユニットまで増やすことができます。 S1 環境から S2 に移行することはできません。 S2 環境から S1 に移行することはできません。

イングレス容量については、最初に、1 か月単位を基にして必要な合計イングレスを決定します。 次に、分単位でのニーズを決定します。

分単位の容量においては、調整と待機時間を考慮します。 データ イングレスのスパイクの持続時間が 24 時間未満の場合、Azure Time Series Insights では、上の表に示したレートの 2 倍のイングレス レートまで "キャッチアップ" することが可能です。

たとえば、単一の S1 SKU を用意して 1 分あたり 720 イベントのレートでデータをイングレスすると、データ レートのスパイクが 1440 イベント以下のレートで 1 時間未満であれば、環境内で顕著な待機時間は発生しません。 ただし、1 分あたり 1440 イベントを超える状態が 1 時間以上続いた場合、環境内では、視覚化されクエリに使用できるデータに待機時間が発生する傾向があります。

プッシュすることが予想されるデータの量が事前にわからない場合があります。 この場合、Azure portal サブスクリプションで [Azure IoT Hub](../iot-hub/monitor-iot-hub.md) と [Azure Event Hubs](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) のデータ テレメトリを確認できます。 テレメトリは、環境をプロビジョニングする方法を決定する際に役立ちます。 Azure portal にあるそれぞれのイベント ソースの **[メトリック]** ウィンドウを使用して、テレメトリを表示します。 イベント ソースのメトリックを理解すると、Azure Time Series Insights 環境をより効果的に計画し、プロビジョニングできます。

### <a name="calculate-ingress-requirements"></a>イングレス要件の計算

イングレス要件を計算するには:

- イングレス容量が 1 分あたりの平均レートを上回っていることを確認し、環境が、用意している容量の 2 倍に相当する予想されるイングレスを 1 時間未満で十分に処理できる規模であることを確認します。

- 1 時間を超えて持続するイングレスのスパイクが発生した場合、そのスパイク レートを平均として使用します。 スパイク レートを処理するために、その容量を備えた環境をプロビジョニングします。

### <a name="mitigate-throttling-and-latency"></a>調整と待機時間の緩和

調整と待機時間が発生しないようにする方法については、[待機時間と調整の緩和](time-series-insights-environment-mitigate-latency.md)に関する記事をご覧ください。

## <a name="shape-your-events"></a>イベントの調整

Azure Time Series Insights へイベントを送信する方法によって、プロビジョニングしている環境のサイズが確実にサポートされることが重要です。 (逆に、Azure Time Series Insights が読み取るイベント数と各イベントのサイズに対して、環境のサイズをマップできます。)また、データにクエリを実行する際に、必要に応じてスライスとフィルターに使用する属性を検討することも重要です。

> [!TIP]
> [イベントの送信](time-series-insights-send-events.md)に関するページで、JSON の整形のドキュメントを参照してください。

## <a name="ensure-that-you-have-reference-data"></a>参照データの確認

*参照データセット* は、イベント ソースからのイベントを増幅する項目の集まりです。 イベント ソースから受信した各イベントは、Azure Time Series Insights のイングレス エンジンによって、指定した参照データ セット内の対応するデータ行と結合されます。 増幅されたイベントは、その後、クエリに利用できます。 結合は、参照データセットに定義されている **主キー** 列に基づきます。

> [!NOTE]
> 参照データは、遡及的に結合されることはありません。 構成されてアップロードされた後は、現在および将来のイングレス データのみが対応付けられ、参照データセットに結合されます。 大量の履歴データを Azure Time Series Insights に送信することを計画している場合、Azure Time Series Insights 内で最初に参照データのアップロードや作成を行わないと、作業をやり直す必要が生じることがあります (ちなみに、楽しいことではありません)。  

Azure Time Series Insights での参照データの作成、アップロード、管理方法について詳しくは、[参照データセットのドキュメント](time-series-insights-add-reference-data-set.md)をご覧ください。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>次のステップ

- [Azure portal で新しい Azure Time Series Insights 環境](time-series-insights-get-started.md)を作成することから開始します。

- Azure Time Series Insights に[イベント ハブのイベント ソースを追加する](./how-to-ingest-data-event-hub.md)方法を学習します。

- [IoT Hub イベント ソースを構成する](./how-to-ingest-data-iot-hub.md)方法を参照します。