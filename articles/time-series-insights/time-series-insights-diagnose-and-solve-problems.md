---
title: 問題の診断、トラブルシューティング、解決 - Azure Time Series Insights
description: この記事では、Azure Time Series Insights 環境内の一般的な問題を診断、トラブルシューティング、解決する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 15f2ff5aaa1d731c13125d0a3ab4ac32acb9276c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023277"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Azure Time Series Insights Gen1 環境の問題を診断して解決する

> [!CAUTION]
> これは Gen1 の記事です。

この記事では、Azure Time Series Insights 環境で発生する可能性がある問題について説明します。 この記事では、考えられる原因と解決のためのソリューションを示します。

## <a name="video"></a>ビデオ

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Azure Time Series Insights の一般的な課題と軽減策の説明</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>問題: データが表示されない

[Azure Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)にデータが表示されない場合は、次の一般的な原因を検討してください。

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>原因 A:イベント ソース データが JSON 形式でない

Azure Time Series Insights は JSON データのみをサポートしています。 JSON のサンプルについては、「[サポートされている JSON 構造](./concepts-json-flattening-escaping-rules.md)」を参照してください。

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>原因 B:イベント ソース キーに必要なアクセス許可がない

* Azure IoT Hub の IoT ハブの場合は、サービス接続アクセス許可があるキーを指定する必要があります。 **iothubowner** または **service** ポリシーのいずれかを選択します。 どちらにもサービス接続のアクセス許可があります。

   [![IoT Hub サービス接続のアクセス許可](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Azure Event Hubs のイベント ハブの場合は、リッスン アクセス許可があるキーを指定する必要があります。 **読み取り** ポリシーと **管理** ポリシーは両方ともリッスン アクセス許可があるため、どちらも機能します。

   [![イベント ハブのリッスン アクセス許可](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>原因 C:指定したコンシューマー グループが Azure Time Series Insights 専用ではない

IoT Hub またはイベント ハブを登録するときに、データの読み取りに使用するコンシューマー グループを設定する必要があります。 このコンシューマー グループは *共有できません*。 コンシューマー グループを共有すると、基盤となる IoT Hub またはイベント ハブによってリーダーのいずれかが自動的にランダムに切断されます。 Azure Time Series Insights が読み取る一意のコンシューマー グループを指定します。

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>原因 D:環境がプロビジョニングされた直後である

Azure Time Series Insights Explorer にデータが表示されるのは、環境とそのデータが最初に作成されてから数分後です。

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>問題: 表示されるデータと表示されないデータがある

データが部分的にのみ表示され、データが遅れているように見える場合は、次のような問題が考えられます。

### <a name="cause-a-your-environment-is-being-throttled"></a>原因 A:環境が調整されている

[調整](time-series-insights-environment-mitigate-latency.md)は、データを持つイベント ソースを作成した後で、環境をプロビジョニングするときの一般的な問題です。 Azure IoT Hub と Azure Event Hubs は、データを最長 7 日間格納します。 Azure Time Series Insights は常に、イベント ソース内の最も古いイベントから開始します (先入れ先出し (*FIFO*))。

たとえば、単一ユニットの Azure Time Series Insights 環境である S1 に接続するときに、イベント ソースに 500 万イベントがある場合、Azure Time Series Insights は 1 日あたり約 100 万イベントを読み取ります。 Azure Time Series Insights に 5 日の待ち時間が発生しているように見えます。 しかし、実際に起きているのは、環境の調整です。

イベント ソースに古いイベントがある場合、次の 2 つのうちいずれかの方法で調整にアプローチできます。

* イベント ソースのリテンション期間を変更して、Azure Time Series Insights に表示したくない古いイベントが除去されるようにします。
* プロビジョニングする環境サイズを大きくして (ユニット数)、古いイベントのスループットを増やします。 前の例では、同じ S1 環境を 1 日あたり 5 ユニットに増やすと、その環境で 1 日以内に追いつくはずです。 安定状態でのイベント生成が 1 日あたり 100 万イベント以下である場合は、Azure Time Series Insights で追いついた後、イベント容量を 1 ユニットに減らすことができます。

適用される調整制限は、環境の SKU のタイプと容量に基づきます。 環境内のすべてのイベント ソースで、この容量が共有されます。 IoT Hub またはイベント ハブのイベント ソースが適用される制限を超えるデータをプッシュする場合は、調整が行われ、タイム ラグが発生します。

次の図は、SKU が S1 で容量が 3 の Azure Time Series Insights 環境を示しています。 この環境は、1 日あたり 300 万イベントを受信できます。

[![環境の容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

ある環境でイベント ハブからメッセージを取り込むとしましょう。 1 日に約 67,000 メッセージのイングレス レートがあります。 このレートは毎分およそ 46 メッセージに相当します。

* 各イベント ハブ メッセージが 1 つの Azure Time Series Insights イベントにフラット化されている場合、調整は行われません。
* 各イベント ハブ メッセージが 100 個の Azure Time Series Insights イベントにフラット化されている場合は、毎分 4,600 イベントを受信することになります。

容量 3 の S1 SKU 環境は、毎分 2,100 イベントしか受信できません (100 万イベント/日 = 700 イベント/分 × 3 ユニット = 2,100 イベント/分)。

フラット化ロジックのしくみについては、「[クエリのパフォーマンスを最大化するための JSON の調整](./concepts-json-flattening-escaping-rules.md)」を参照してください。

#### <a name="recommended-resolutions-for-excessive-throttling"></a>調整が過剰な場合の推奨される解決

タイム ラグを解消するには、環境の SKU 容量を増やします。 詳細については、[Azure Time Series Insights 環境のスケーリング](time-series-insights-how-to-scale-your-environment.md)に関するページを参照してください。

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>原因 B:履歴データの初回取り込みで受信が遅くなる

既存のイベント ソースを接続している場合は、IoT Hub またはイベント ハブに既にデータが含まれている可能性があります。 環境はイベント ソース メッセージのリテンション期間の始めからデータをプルし始めます。 この既定の処理は、上書きできません。 調整に関与できます。 調整は履歴データを取り込むので、追いつくまでしばらく時間がかかる可能性があります。

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>大規模な初期の取り込みの推奨される解決方法

ラグを解消するには:

1. SKU 容量を最大許容値 (この場合は 10) に増やします。 容量を増やすと、受信プロセスが開始しより短い時間で追いつくようになります。 なお、容量の増加分には料金が発生します。 どれくらいの時間で追いつくかを視覚化するために、[Azure Time Series Insights Explorer](https://insights.timeseries.azure.com) の可用性グラフを表示できます。

2. ラグが解消したら、SKU 容量は通常の受信レートまで低下します。

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>問題: 以前に表示されていたデータが、表示されなくなった

Azure Time Series Insights でデータが取り込まれなくなったのに、イベントが引き続き Iot Hub またはイベント ハブにストリーミングされている場合は、次のような問題が考えられます。

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>原因 A:ハブ アクセス キーが再生成されたため、環境を更新する必要がある

この問題は、イベント ソースの作成時に提供されたキーが有効ではなくなったときに発生します。 テレメトリはハブに表示されますが、Azure Time Series Insights にイングレス受信メッセージが表示されません。 キーが再生成されたかどうかが不明な場合は、イベント ハブのアクティビティ ログで、"名前空間の承認規則の作成または更新" を検索します。 IoT ハブの場合は、"IotHub リソースの作成または更新" を検索します。

新しいキーで Azure Time Series Insights 環境を更新するには、Azure portal でお使いのハブ リソースを開き、新しいキーをコピーします。 Azure Time Series Insights リソースにアクセスし、 **[イベント ソース]** を選択します。

   [![[イベント ソース] の選択](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

インジェストが停止した 1 つまたは複数のイベント ソースを選択し、新しいキーを貼り付けて、 **[保存]** を選択します。

   [![新しいキーを貼り付ける](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題: イベント ソースのタイムスタンプ プロパティ名の設定が機能しない

イベント ソースから JSON 文字列として取得されるタイムスタンプ プロパティ値が確実に _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_ の形式であるようにします。 次に例を示します。**2008-04-12T12: 53Z**。

タイムスタンプ プロパティ名では大文字と小文字が区別されることにご注意ください。

タイムスタンプ プロパティ名がキャプチャされて正しく動作していることを確認する最も簡単な方法は、Azure Time Series Insights Explorer を使用することです。 Azure Time Series Insights Explorer 内でグラフを使用して、タイムスタンプのプロパティ名を入力した後で期間を選択します。 選択内容を右クリックして、 **[イベントの探索]** を選択します。

最初の列見出しは、タイムスタンプ プロパティ名になるはずです。 "**Timestamp**" という語の横に **($ts)** が表示されます。

次の値は表示されません。

* *(abc)* :Azure Time Series Insights がデータ値を文字列として読み取っていることを示します。
* *カレンダー アイコン*:Azure Time Series Insights がデータ値を日時値として読み取っていることを示します。
* *#* :Azure Time Series Insights がデータ値を整数として読み取っていることを示します。

## <a name="next-steps"></a>次のステップ

* [Azure Time Series Insights で待機時間を緩和する方法](time-series-insights-environment-mitigate-latency.md)を確認します。

* [Azure Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)を確認します。