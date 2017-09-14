---
title: "問題の診断と解決 | Microsoft Docs"
description: "このチュートリアルでは、Time Series Insights 環境の問題を診断して解決する方法について説明します。"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4b8d5fdab1744b2db658917f91d6dac05db30d2f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/12/2017

---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Time Series Insights 環境の問題を診断して解決する

## <a name="i-dont-see-my-data"></a>データが表示されない
[Azure Time Series Insights ポータル](https://insights.timeseries.azure.com)で環境内のデータが表示されない理由はいくつかあります。

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>イベント ソースに JSON 形式のデータがない
Azure Time Series Insights は現時点で JSON データのみをサポートしています。 JSON のサンプルについては、「[サポートされている JSON 構造](time-series-insights-send-events.md#supported-json-shapes)」を参照してください。

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>イベント ソースを登録したときに、必要なアクセス許可を持つキーを指定しなかった
* IoT Hub の場合は、**サービス接続**アクセス許可を持つキーを指定する必要があります。

   ![IoT Hub のサービス接続アクセス許可](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   上の図に示すように、ポリシー **iothubowner** と **service** は両方とも**サービス接続**アクセス許可が設定されているため、どちらも使用できます。
* イベント ハブの場合は、**リッスン** アクセス許可を持つキーを指定する必要があります。

   ![イベント ハブのリッスン アクセス許可](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   上の図に示すように、ポリシー **read** と **manage** は両方とも**リッスン** アクセス許可が設定されているため、どちらも使用できます。

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>指定したコンシューマー グループが Time Series Insights 専用ではない
IoT Hub またはイベント ハブでは、登録時に、データを読み取るために使用するコンシューマー グループを指定する必要があります。 このコンシューマー グループは共有できません。 共有すると、基盤となるイベント ハブによってリーダーのいずれかがランダムに選択されて切断されます。

## <a name="i-see-my-data-but-theres-a-lag"></a>データは表示されるが、タイム ラグがある
[Time Series Insights ポータル](https://insights.timeseries.azure.com)で環境内のデータが一部しか表示されない理由はいくつかあります。

### <a name="your-environment-is-getting-throttled"></a>環境が調整されている
環境の SKU のタイプと容量に基づいて調整制限が適用されます。 環境内のすべてのイベント ソースで、この容量が共有されます。 IoT Hub またはイベント ハブのイベント ソースが適用される制限を超えるデータをプッシュしている場合は、調整が行われ、タイム ラグが発生します。

次の図は、SKU が S1 で容量が 3 の Time Series Insights 環境を示しています。 この環境は、1 日あたり 300 万イベントを受信できます。

![環境の SKU の現在の容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

この環境が、次の図に示す受信レートでイベント ハブからメッセージを受信していたとします。

![イベント ハブの受信レートの例](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

図に示すように、1 日の受信レートは約 67,000 メッセージです。 このレートは毎分およそ 46 メッセージに相当します。 各イベント ハブ メッセージが 1 つの Time Series Insights イベントにフラット化されている場合、この環境では調整は行われません。 各イベント ハブ メッセージが 100 個の Time Series Insights イベントにフラット化されている場合は、毎分 4,600 イベントを受信することになります。 容量 3 の S1 SKU 環境は、毎分 2,100 イベントしか受信できません (100 万イベント/日 = 700 イベント/分 × 3 ユニット = 2,100 イベント/分)。 そのため、調整によるタイム ラグが発生します。 

フラット化のロジックのしくみについては、「[サポートされている JSON 構造](time-series-insights-send-events.md#supported-json-shapes)」を参照してください。

#### <a name="recommended-steps"></a>推奨される手順
タイム ラグを解消するには、環境の SKU 容量を増やします。 詳細については、「[Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)」を参照してください。

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>履歴データをプッシュすると受信が遅くなる
既存のイベント ソースを接続している場合は、IoT Hub またはイベント ハブに既にデータがある可能性があります。 その場合、環境はイベント ソース メッセージのリテンション期間の始めからデータをプルし始めます。 

この動作は既定の動作であり、無効にすることはできません。 調整を適用できますが、履歴データの受信の遅れを取り戻すのに時間がかかることがあります。

#### <a name="recommended-steps"></a>推奨される手順
タイム ラグを解消するには、次の手順を実行します。
1. SKU 容量を許可される最大値 (この場合は 10) まで増やします。 容量を増やすと、より短い時間で受信プロセスが追いつくようになります。 どれくらいの時間で追いつくかを、[Time Series Insights ポータル](https://insights.timeseries.azure.com)の可用性グラフで視覚化できます。 なお、容量の増加分には料金が発生します。
2. タイム ラグが解消したら、SKU 容量を通常の受信レートに戻します。

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>イベント ソースの*タイムスタンプ プロパティ名*設定が機能しない
名前と値が次の規則に準拠していることを確認してください。
* タイムスタンプ プロパティ名は_大文字と小文字が区別されます_。
* イベント ソースから JSON 文字列として取得されるタイムスタンプ プロパティ値は、_yyyy-MM-ddTHH:mm:ss.FFFFFFFK_ の形式である必要があります。 例: 2008-04-12T12:53Z

