---
title: 問題の診断、トラブルシューティング、解決 - Azure Time Series Insights | Microsoft Docs
description: この記事では、Azure Time Series Insights 環境内の一般的な問題を診断、トラブルシューティング、解決する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.custom: seodec18
ms.openlocfilehash: ff723f490a3f6d34f652e0b21e5f6e0b16f0a841
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900245"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Time Series Insights 環境の問題を診断して解決する

この記事では、Azure Time Series Insights 環境で発生する可能性があるいくつかの問題について説明します。 この記事では、考えられる原因と解決のためのソリューションを示します。

## <a name="video"></a>ビデオ

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Time Series Insights でお客様が直面する一般的な課題とその軽減策を説明します。</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>問題: データが表示されない

以下のいくつかの一般的な理由で、[Azure Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)にデータが表示されません。

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>原因 A: イベント ソース データが JSON 形式でない

Azure Time Series Insights は JSON データのみをサポートしています。 JSON のサンプルについては、「[サポートされている JSON 構造](./how-to-shape-query-json.md)」を参照してください。

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>原因 B: イベント ソース キーに必要なアクセス許可がない

* Azure IoT Hub の IoT Hub の場合は、**サービス接続**アクセス許可を備えたキーを指定する必要があります。 **iothubowner** または **service** ポリシーのいずれかを選択します。両方とも**サービス接続**アクセス許可を持っているためです。

   [![IoT Hub サービス接続のアクセス許可](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Azure Event Hubs のイベント ハブの場合は、**リッスン** アクセス許可を持つキーを指定する必要があります。 **読み取り**ポリシーと**管理**ポリシーは、どちらも**リッスン** アクセス許可があるため、どちらでも使用できます。

   [![イベント ハブのリッスン アクセス許可](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>原因 C: 指定したコンシューマー グループが Time Series Insights 専用ではない

IoT Hub またはイベント ハブを登録するときに、データの読み取りに使用するコンシューマー グループを設定する必要があります。 このコンシューマー グループは*共有できません*。 コンシューマー グループを共有すると、基盤となる IoT Hub またはイベント ハブによってリーダーのいずれかが自動的にランダムに切断されます。 Time Series Insights が読み取る一意のコンシューマー グループを指定します。

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>原因 D: 環境がプロビジョニングされた直後

Time Series Insights エクスプローラーにデータが表示されるのは、環境とそのデータが最初に作成されてから数分後です。

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>問題: 表示されるデータと表示されないデータがある

データが一部しか表示されず、データにタイムラグが生じているように思われる場合は、いくつかの可能性を考慮する必要があります。

### <a name="cause-a-your-environment-is-being-throttled"></a>原因 A: 環境が調整されている

[調整](time-series-insights-environment-mitigate-latency.md)は、データを持つイベント ソースを作成した後で、環境をプロビジョニングするときの一般的な問題です。 Azure IoT Hub と Azure Event Hubs は、データを最長 7 日間格納します。 Time Series Insights は常に、イベント ソース内の最も古いイベントから開始します (先入れ先出し (*FIFO*))。

たとえば、単一ユニットの Time Series Insights 環境である S1 に接続するときに、イベント ソースに 500 万イベントがある場合、Time Series Insights は 1 日あたり約 100 万イベントを読み取ります。 Time Series Insights に 5 日の待ち時間が発生しているように見えます。 ただし、起こっているのは、環境が調整されているということです。

イベント ソースに古いイベントがある場合、次の 2 つのうちいずれかの方法で調整にアプローチできます。

- イベント ソースのリテンション期間を変更して、Time Series Insights に表示したくない古いイベントが除去されやすくなるようにします。
- プロビジョニングする環境サイズを大きくして (ユニット数)、古いイベントのスループットを増やします。 前の例を使用して、同じ S1 環境を 1 日あたり 5 ユニットに増やした場合、環境は 1 日のうちに追いつくはずです。 安定状態でのイベント生成が 1 日あたり 100 万イベント以下である場合、追いついた後でイベントの容量を 1 ユニットまで減らすことができます。

環境の SKU のタイプと容量に基づいて調整制限が適用されます。 環境内のすべてのイベント ソースで、この容量が共有されます。 IoT Hub またはイベント ハブのイベント ソースが適用される制限を超えるデータをプッシュする場合は、調整が行われ、タイム ラグが発生します。

次の図は、SKU が S1 で容量が 3 の Time Series Insights 環境を示しています。 この環境は、1 日あたり 300 万イベントを受信できます。

[![環境の SKU の現在の容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

たとえば、ある環境でイベント ハブからメッセージを取り込むとします。 1 日の受信レートは、最大 67,000 メッセージです。 このレートは毎分およそ 46 メッセージに相当します。 

* 各イベント ハブ メッセージが 1 つの Time Series Insights イベントにフラット化されている場合、調整は行われません。 
* 各イベント ハブ メッセージが 100 個の Time Series Insights イベントにフラット化されている場合は、毎分 4,600 イベントを受信することになります。 

容量 3 の S1 SKU 環境は、毎分 2,100 イベントしか受信できません (100 万イベント/日 = 700 イベント/分 × 3 ユニット = 2,100 イベント/分)。 

フラット化のロジックのしくみについては、「[サポートされている JSON 構造](./how-to-shape-query-json.md)」を参照してください。

#### <a name="recommended-resolutions-for-excessive-throttling"></a>調整が過剰な場合の推奨される解決

タイム ラグを解消するには、環境の SKU 容量を増やします。 詳細については、[Time Series Insights 環境のスケーリング](time-series-insights-how-to-scale-your-environment.md)に関するページを参照してください。

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>原因 B: 履歴データの初回取り込みで受信が遅くなる

既存のイベント ソースを接続している場合は、IoT Hub またはイベント ハブに既にデータが含まれている可能性があります。 環境はイベント ソース メッセージのリテンション期間の始めからデータをプルし始めます。 この既定の処理は、上書きできません。 調整に関与できます。 調整は履歴データを取り込むので、追いつくまでしばらく時間がかかる可能性があります。

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>大規模な初期の取り込みの推奨される解決方法

ラグを解消するには:

1. SKU 容量を最大許容値 (この場合は 10) に増やします。 容量を増やすと、受信プロセスが開始しより短い時間で追いつくようになります。 なお、容量の増加分には料金が発生します。 どれくらいの時間で追いつくかを視覚化するために、[Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)の可用性グラフを表示できます。

2. ラグが解消したら、SKU 容量は通常の受信レートまで低下します。

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題: イベント ソースのタイムスタンプ プロパティ名設定が機能しない

タイムスタンプ プロパティの名前と値が次の規則に準拠していることを確認してください。

* タイムスタンプ プロパティ名では大文字と小文字が区別されます。
* イベント ソースから JSON 文字列として取得されるタイムスタンプ プロパティ値は、_yyyy-MM-ddTHH:mm:ss.FFFFFFFK_ の形式である必要があります。 たとえば、**2008-04-12T12:53Z** となります。

タイムスタンプ プロパティ名がキャプチャされて正しく動作していることを確認する最も簡単な方法は、Azure Time Series Insights エクスプローラーを使用することです。 Time Series Insights エクスプローラー内でグラフを使用して、タイムスタンプのプロパティ名を入力した後で期間を選択します。 選択内容を右クリックして、 **[イベントの探索]** オプションを選択します。

最初の列見出しは、タイムスタンプ プロパティ名になるはずです。 **タイムスタンプ**の語の横に **($ts)** と表示されるはずです。

次の値は表示されません。

- *(abc)* :Time Series Insights が文字列としてデータ値を読み取っていることを示します。
- *カレンダー アイコン*:Time Series Insights が*日時*としてデータ値を読み取っていることを示します。
- *#* :Time Series Insights が整数としてデータ値を読み取っていることを示します。

## <a name="next-steps"></a>次の手順

- [Azure Time Series Insights 内での待機時間を軽減する方法](time-series-insights-environment-mitigate-latency.md)を確認します。

- [Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)を確認します。