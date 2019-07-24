---
title: Azure Time Series Insights 環境の規模を計画する | Microsoft Docs
description: この記事では、ストレージ容量、データ保有期間、イングレス容量、監視、ビジネスのディザスター リカバリー (BCDR) など、Azure Time Series Insights 環境を計画する際のベスト プラクティスに従う方法について説明します。
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: ce31b22dcbf3fbe79192647e9a21983911e77a9c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556035"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Azure Time Series Insights 環境の計画

この記事では、予想されるイングレス レートとデータ リテンション期間の要件に基づいて、Azure Time Series Insights 環境を計画する方法について説明します。

## <a name="video"></a>ビデオ: 

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>この動画では、Time Series Insights データの保有と、そのための計画方法を紹介しています。</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>ベスト プラクティス

Time Series Insights の使用を開始するときは、分単位でプッシュすることが予想されるデータの量と、データを保存する必要がある期間を把握しておくことをお勧めします。  

Time Series Insights の 2 つの SKU の容量とリテンション期間の詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」をご覧ください。

長期的な成功に向けて環境を最適に計画するには、次の属性を検討します。 
- ストレージの容量
- データ保持期間
- イングレス容量 
- イベントの整形
- 参照データの配置確認

## <a name="understand-storage-capacity"></a>ストレージ容量について
既定では、Time Series Insight は、プロビジョニング済みのストレージの容量 (ユニットごとのストレージの単位時間あたりの容量) とイングレスに基づいてデータを保持します。

## <a name="understand-data-retention"></a>データ リテンション期間について
Time Series Insight 環境の **[データ リテンション期間]** 設定を構成できます。最大 400 日のリテンション期間を設定できます。  Time Series Insights には 2 つのモードがあります。1 つは、環境で最新のデータを確保するように最適化するモードです (既定で有効)。もう 1 つは、リテンション期間の制限が満たされるように最適化するモードです。このモードでは、環境の全ストレージ容量に達した場合にイングレスが一時停止されます。  Azure Portal の環境の構成ページで、リテンション期間を調整し、2 つのモードを切り替えることができます。

Time Series Insights 環境では、最大 400 日のデータ リテンション期間を構成できます。

## <a name="configure-data-retention"></a>データ リテンション期間の構成

1. [Azure Portal](https://portal.azure.com) で、Time Series Insights 環境を選択します。

2. **[Time Series Insights 環境]** ページで、**[設定]** の **[構成]** を選択します。 

3. **[データ リテンション期間 (日)]** ボックスに、1 ～ 400 の値を入力します。

   ![リテンション期間の構成](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>イングレス容量について

計画を立てる際に重視すべきもう 1 つの領域は、1 分あたりの割り当ての派生物であるイングレス容量です。 

調整の観点から、パケット サイズが 32 KB のイングレス データ パケットは、それぞれ 1 KB のサイズの 32 個のイベントとして扱われます。 イベントの最大許容サイズは 32 KB です。32 KB を超えるデータ パケットは切り捨てられます。

各 SKU のイングレス容量を次の表に示します。

|SKU  |ユニットあたりのイベント数/月  |ユニットあたりのイベント サイズ/月  |ユニットあたりのイベント数/分  | ユニットあたりのサイズ/分   |
|---------|---------|---------|---------|---------|
|S1     |   30,000,000     |  30 GB     |  720    |  720 KB   |
|S2     |   300,000,000    |   300 GB   | 7,200   | 7,200 KB  |

1 つの環境で、S1 SKU または S2 SKU の容量を 10 ユニットまで増やすことができます。 S1 環境から S2 環境、または S2 環境から S1 環境に移行することはできません。

イングレス容量については、1 か月あたりに必要なイングレスの合計数をまず決定する必要があります。 次に、1 分あたりのニーズを決定します。これには、調整と待機時間が関与するためです。

データ イングレスのスパイクの持続が 24 時間未満の場合、Time Series Insights は上記のレートの 2 倍のイングレス レートで "キャッチアップ" できます。 

たとえば、単一の S1 SKU を使用し、1 分あたり 720 イベントのレートでデータをイングレスしている場合、1440 イベント以下のレートでスパイクの持続が 1 時間未満であれば、環境に顕著な待機時間は発生しません。 ただし、1 分あたり 1440 イベントを超える状態が 1 時間以上続いた場合、環境内で視覚化され、クエリに使用できるデータに待機時間が発生する可能性があります。 

プッシュすることが予想されるデータの量が事前にわからない場合があります。 この場合、Azure Portal で [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) と [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) のデータ テレメトリを確認できます。 このテレメトリは、環境をプロビジョニングする方法を決定する際に役立ちます。 Azure Portal で、それぞれのイベント ソースの **[メトリック]** ページを使用してテレメトリを表示します。 イベント ソースのメトリックを理解すると、Time Series Insights 環境をより効果的に計画し、プロビジョニングできます。

### <a name="calculate-ingress-requirements"></a>イングレス要件の計算

- イングレス容量が 1 分あたりの平均レートを上回っていることを確認し、環境が、現在の容量の 2 倍に相当する予想されるイングレスを 1 時間未満で十分に処理できる規模であることを確認します。

- 1 時間以上続くイングレス スパイクが発生した場合は、スパイク レートを平均値として使用し、スパイク レートに対応する容量を備えた環境をプロビジョニングします。
 
### <a name="mitigate-throttling-and-latency"></a>調整と待機時間の緩和

調整と待機時間が発生しないようにする方法については、[待機時間と調整の緩和](time-series-insights-environment-mitigate-latency.md)に関する記事をご覧ください。

## <a name="shaping-your-events"></a>イベントの整形
イベントを TSI に送信する方法がプロビジョニングする環境のサイズに対応していることを確認することが重要です (逆に言えば、TSI で読み込まれるイベントの数と各イベントのサイズに環境のサイズをマッピングできます)。  同様に、データにクエリを実行するときのスライスとフィルターの基準にする属性について考えることが重要です。  このことを念頭に、[Sending events](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events) に関する文書の JSON 整形セクションをご覧になることをお勧めします。  ページの下のほうにあります。  

## <a name="ensuring-you-have-reference-data-in-place"></a>参照データの配置確認
参照データ セットは、イベント ソースからのイベントを増幅する項目の集まりです。 イベント ソースから受信した各イベントは、Time Series Insights のイングレス エンジンによって、指定した参照データ セット内の対応するデータ行と結合されます。 こうして増幅されたイベントをクエリで利用することができます。 この結合操作は、参照データ セットに定義されている主キー列に基づいて行われます。

参照データは遡及的に結合されないことにご注意ください。 つまり、データが構成されてアップロードされると、現在および将来のイングレス データのみが対応付けられ、参照日付セットに結合されます。  大量の履歴データを TSI に送信する予定のとき、TSI で最初に参照データをアップロードまたは作成しない場合、作業をやり直す必要があるかもしれません (それは楽しい作業ではないかもしれません)。  

TSI での参照データの作成、アップロード、管理方法についての詳細は、Microsoft の[参照データ](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)に関する文書をご覧ください。

## <a name="business-disaster-recovery"></a>ビジネスのディザスター リカバリー
Time Series Insights は、Azure サービスとして、Azure リージョン レベルでの冗長性を利用して高可用性 (HA) を提供します。その際、ソリューションによる追加操作は必要ありません。 さらに、Microsoft Azure Platform には、ディザスター リカバリー (DR) 機能または複数のリージョンにわたる可用性を備えたソリューションを構築するのに役立つ機能が用意されています。 複数のリージョンにわたるグローバルな高可用性をデバイスまたはユーザーに提供する場合は、これらの Azure DR 機能を利用してください。 ビジネス継続性および障害復旧のための Azure の組み込み機能については、[Azure のビジネス継続性テクニカル ガイダンス](../resiliency/resiliency-technical-guidance.md)に関する記事を参照してください。 [Azure アプリケーションの障害復旧と高可用性](https://docs.microsoft.com/azure/architecture/resiliency/index)に関するページでは、HA と DR を実現するための Azure アプリケーションの戦略に関するアーキテクチャ指針を提供しています｡

Time Series Insights には、ビジネスのディザスター リカバリー (BCDR) は組み込まれていません。
既定では、Azure IoT Hub と Event Hubs には共にリカバリーが組み込まれています。

IoT Hub の BCDR ポリシーの詳細については、[ここ](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)を参照してください。  

イベント ハブの BCDR ポリシーの詳細については、[ここ](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)を参照してください。

ただし、BCDR を必要とするお客様は、以下の方法を使って復旧戦略を実装することもできます。
バックアップの Azure リージョン内に 2 番目の Time Series Insights 環境を作成し、プライマリ イベント ソースからこのセカンダリ環境にイベントを送信して、2 番目の専用のコンシューマー グループとそのイベント ソースの BCDR ガイドラインを活用する。  

1.  2 番目のリージョン内に環境を作成します。  Time Series Insights 環境の作成について詳しくは、[ここ](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)を参照してください。
2.  イベント ソースのために 2 番目の専用コンシューマー グループを作成し、そのイベント ソースを新しい環境に接続します。  必ず 2 番目の専用コンシューマー グループを指定してください。  これに関する詳細については、[IoT Hub のドキュメント](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)または[イベント ハブのドキュメント](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)を参照してください。
3.  障害インシデントの発生時にプライマリ リージョンが停止した場合は、運用をバックアップの Time Series Insights 環境に切り換えます。  

TSI が再度メッセージの処理を開始できるようにするために､フェールオーバー シナリオ中に遅延が消磁することがある**ことに注意して**ください｡これにより､メッセージの処理でスパイクが生じることがあります。 詳細は、[このドキュメント](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)を参照してください｡

## <a name="next-steps"></a>次の手順
- [イベント ハブ イベント ソースを追加する方法](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [IoT Hub イベント ソースを追加する方法](time-series-insights-how-to-add-an-event-source-iothub.md)
