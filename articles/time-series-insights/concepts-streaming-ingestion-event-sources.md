---
title: ストリーミング インジェスト イベント ソース - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 へのデータ ストリーミングについて説明します。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: a16a83408587a1b99485140174c45e457ac820f8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855099"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2 のイベント ソース

 Azure Time Series Insights Gen2 環境には、最大 2 つのストリーミング イベント ソースを含めることができます。 次の 2 種類の Azure リソースが入力としてサポートされています。

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

イベントは、UTF-8 でエンコードされた JSON として送信する必要があります。

## <a name="create-or-edit-event-sources"></a>イベント ソースの作成または編集

イベント ソース リソースは、Azure Time Series Insights Gen2 環境と同じ Azure サブスクリプションに存在する場合と、別のサブスクリプションに存在する場合があります。[Azure portal](time-series-insights-update-create-environment.md#create-a-preview-payg-environment)、[Azure CLI](https://github.com/Azure/azure-cli-extensions/tree/master/src/timeseriesinsights)、[ARM テンプレート](time-series-insights-manage-resources-using-azure-resource-manager-template.md)、および [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) を使用して、環境のイベント ソースを作成、編集、または削除できます。

イベント ソースに接続すると、Azure Time Series Insights Gen2 環境は、IoT Hub またはイベント ハブに現在格納されているすべてのイベントを、最も古いイベントから読み取ります。

> [!IMPORTANT]
>
> - Azure Time Series Insights Gen2 環境にイベント ソースをアタッチすると、初期の待機時間が長くなることがあります。
> - イベント ソースの待機時間は、現在 IoT Hub またはイベント ハブにあるイベントの数によって変わります。
> - 最初にイベント ソース データが取り込まれた後は、待機時間が短くなります。 長い待機時間が継続する場合は、Azure portal からサポート チケットを送信してください。

## <a name="streaming-ingestion-best-practices"></a>ストリーミング インジェストのベスト プラクティス

- 常に Azure Time Series Insights Gen2 環境に固有のコンシューマー グループを常に作成して、イベント ソースからデータを消費します。 コンシューマー グループを再利用すると、ランダムに切断が発生し、データが失われる可能性があります。

- 同じ Azure リージョンで、Azure Time Series Insights Gen2 環境と、IoT ハブ、イベントハブ、またはその両方を構成します。 別のリージョンにイベント ソースを構成することもできますが、このシナリオはサポートされておらず、高可用性を保証できません。

- 環境の[スループット速度制限](./concepts-streaming-ingress-throughput-limits.md)またはパーティションごとの制限を超えないようにします。

- 環境でデータ処理の問題が発生した場合に通知されるように、遅延の[警告](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency#monitor-latency-and-throttling-with-alerts)を構成します。

- ストリーミング インジェストは、準リアルタイム データおよび最近のデータにのみ使用します。履歴データのストリーミングはサポートされていません。

- プロパティをエスケープする方法と JSON [データをフラット化して格納する](./concepts-json-flattening-escaping-rules.md)方法を理解します。

- イベント ソース接続文字列を指定する場合は、最小限の特権の原則に従います。 Event Hubs の場合は、*send* 要求のみの共有アクセス ポリシーを構成し、IoT Hub の場合は、*service connect* アクセス許可のみを使用します。

### <a name="historical-data-ingestion"></a>履歴データのインジェスト

Azure Time Series Insights Gen2 では、ストリーミング パイプラインを使用した履歴データのインポートは現在サポートされていません。 過去のデータをご使用の環境にインポートする必要がある場合は、次のガイドラインに従ってください。

- ライブ データと履歴データを並行してストリーミングしないでください。 順不同のデータを取り込むと、クエリのパフォーマンスが低下します。
- 最適なパフォーマンスを得るために、履歴データは時系列で取り込みます。
- インジェストのスループット率制限以下を維持します。
- データがウォーム ストアの保持期間よりも古い場合、ウォーム ストアを無効にします。

## <a name="event-source-timestamp"></a>イベント ソースのタイムスタンプ

イベント ソースを構成するときに、タイムスタンプ ID プロパティを入力するように求められます。 タイムスタンプ プロパティは、時間の経過と共にイベントを追跡するために使用されます。これは、[クエリ API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute) で $event.$ts として使用される時刻であり、Azure Time Series Insights TSI Explorer で時系列をプロットするために使用されます。 作成時にプロパティが指定されない場合、またはイベントのタイムスタンプ プロパティがない場合は、イベントが IoT Hub または Events Hubs にエンキューした時刻が既定値として使用されます。 タイムスタンプ プロパティ値は UTC に格納されます。

通常、ユーザーは、既定のハブ エンキュー時刻を使用する代わりに、タイムスタンプ プロパティをカスタマイズして、センサーまたはタグが読み取りを生成した時刻を使用することを選択します。 これが特に必要になるのは、デバイスの接続が間欠的に失われ、遅延メッセージがまとめて Azure Time Series Insights Gen2 に転送されるときです。

カスタム タイムスタンプが、入れ子になった JSON オブジェクトまたは配列内にある場合は、[フラット化およびエスケープの名前付け規則](concepts-json-flattening-escaping-rules.md)に従って、正しいプロパティ名を指定する必要があります。 たとえば、[ここで](concepts-json-flattening-escaping-rules.md#example-a)示されている JSON ペイロードの イベント ソース タイムスタンプは、`"values.time"` と入力する必要があります。

### <a name="time-zone-offsets"></a>タイムゾーンのオフセット

タイムスタンプは ISO 8601 形式で送信される必要があります。UTC で格納されます。 タイムゾーンのオフセットが指定されている場合は、オフセットが適用され、その時刻が UTC 形式で格納されたり返されたりします。 オフセットの形式が正しくない場合は無視されます。 元のオフセットのコンテキストがソリューションに含まれない可能性がある場合は、追加の個別のイベント プロパティでオフセット データを送信して、それを保存し、アプリケーションがクエリ応答で参照できるようにします。

タイムゾーンのオフセットは、次のいずれかの形式にする必要があります。

±HHMMZ</br>
±HH:MM</br>
±HH:MMZ</br>

## <a name="next-steps"></a>次のステップ

- [JSON のフラット化とエスケープのルール](./concepts-json-flattening-escaping-rules.md)を読み、イベントの格納方法を理解する。

- 使用環境の[スループットに関する制限事項](./concepts-streaming-ingress-throughput-limits.md)を理解する。
