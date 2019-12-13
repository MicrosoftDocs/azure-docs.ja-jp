---
title: イベントを環境に送信する - Azure Time Series Insights | Microsoft Docs
description: イベント ハブを構成し、サンプル アプリケーションを実行して、Azure Time Series Insights 環境にイベントを送信する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1dfd9a8d2723136ef68d983eb99bf2391fb87879
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894892"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>イベント ハブを使用して Time Series Insights 環境にイベントを送信する

この記事では、Azure Event Hubs でイベント ハブを作成して構成する方法について説明します。 また、サンプル アプリケーションを実行し、Event Hubs から Azure Time Series Insights にイベントをプッシュする方法も説明します。 JSON 形式のイベントを含む既存のイベント ハブがある場合は、このチュートリアルをスキップし、[Azure Time Series Insights](./time-series-insights-update-create-environment.md) で環境を表示してください。

## <a name="configure-an-event-hub"></a>イベント ハブを構成する

1. イベント ハブを作成する方法については、[Event Hubs のドキュメント](https://docs.microsoft.com/azure/event-hubs/)をご覧ください。
1. 検索ボックスで、**Event Hubs** を検索します。 返された一覧で、 **[Event Hubs]** を選択します。
1. 自分のイベント ハブを選択します。
1. イベント ハブを作成すると、イベント ハブの名前空間が作成されます。 名前空間内にまだイベント ハブを作成していない場合は、メニューの **[エンティティ]** でイベント ハブを作成します。  

    [![イベント ハブの一覧](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. イベント ハブを作成した後、イベント ハブの一覧でそれを選択します。
1. メニューで、 **[エンティティ]** の **[イベント ハブ]** を選択します。
1. イベント ハブの名前を選択して構成します。
1. **[概要]** で **[コンシューマー グループ]** を選択し、 **[コンシューマー グループ]** を選択します。

    [![コンシューマー グループを作成する](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Time Series Insights のイベント ソースで排他的に使用されるコンシューマー グループを作成していることを確認します。

    > [!IMPORTANT]
    > このコンシューマー グループがその他のサービス (Azure Stream Analytics ジョブや別の Time Series Insights 環境など) で使用されていないことをご確認ください。 コンシューマー グループが他のサービスで使用されている場合、この環境と他のサービスの両方で読み取り操作が悪影響を受けます。 コンシューマー グループとして **$Default** を使用した場合、他の閲覧者がそのコンシューマー グループを再利用する可能性があります。

1. メニューで、 **[設定]** の **[共有アクセス ポリシー]** を選択してから、 **[追加]** を選択します。

    [![[共有アクセス ポリシー] を選んでから、[追加] ボタンを選択する](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. **[新しい共有アクセス ポリシーの追加]** ウィンドウで、**MySendPolicy** という名前の共有アクセス ポリシーを作成します。 後で示す C# の例では、この共有アクセス ポリシーを使用してイベントを送信します。

    [![[ポリシー名] ボックスに「MySendPolicy」と入力する](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. **[要求]** で、 **[送信]** チェック ボックスをオンにします。

## <a name="add-a-time-series-insights-instance"></a>Time Series Insights のインスタンスを追加する

Time Series Insights の更新では、インスタンスを使用して、受信したテレメトリ データにコンテキスト データが追加されます。 データはクエリ時に**タイム シリーズ ID** を使用して結合されます。 この記事の後半で使用するサンプルの風力発電プロジェクトの**タイム シリーズ ID** は、`id` です。 Time Series Insight のインスタンスと**タイム シリーズ ID** について詳しくは、「[タイム シリーズ モデル](./time-series-insights-update-tsm.md)」をご覧ください。

### <a name="create-a-time-series-insights-event-source"></a>Time Series Insights のイベント ソースを作成する

1. イベント ソースを作成していない場合は、[イベント ソースを作成する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)手順を実行します。

1. `timeSeriesId` の値を設定します。 **タイム シリーズ ID** について詳しくは、「[タイム シリーズ モデル](./time-series-insights-update-tsm.md)」をご覧ください。

### <a name="push-events-to-windmills-sample"></a>風力発電のサンプルにイベントをプッシュする

1. 検索バーで「**Event Hubs**」を検索します。 返された一覧で、 **[Event Hubs]** を選択します。

1. 自分のイベント ハブ インスタンスを選択します。

1. **[共有アクセス ポリシー]**  >  **[MySendPolicy]** にアクセスします。 **[接続文字列 - 主キー]** の値をコピーします。

    [![主キーの接続文字列の値をコピーする](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. https://tsiclientsample.azurewebsites.net/windFarmGen.html にアクセスします。 その URL では、シミュレートされた風力発電デバイスが実行されます。
1. Web ページの **[イベント ハブ接続文字列]** ボックスに、[風力発電の入力フィールド](#push-events-to-windmills-sample)でコピーした接続文字列を貼り付けます。
  
    [![[イベント ハブ接続文字列] ボックスに主キーの接続文字列を貼り付ける](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. **[Click to start]\(クリックして開始\)** を選択します。 シミュレーターで、直接使用できるインスタンスの JSON が生成されます。

1. Azure portal でイベント ハブに戻ります。 **[概要]** ページに、イベント ハブによって受信された新しいイベントが表示されます。

    [![イベント ハブのメトリックが表示されているイベント ハブの [概要] ページ](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>サポートされている JSON 構造

### <a name="example-one"></a>例 1

* **入力**:単純な JSON オブジェクト。

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **出力**:1 つのイベント。

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>例 2

* **入力**:2 つの JSON オブジェクトを含む JSON 配列。 各 JSON オブジェクトがイベントに変換されます。

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **出力**:2 つのイベント。

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>例 3

* **入力**:2 つの JSON オブジェクトを含む入れ子になった JSON 配列を含む JSON オブジェクト。

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **出力**:2 つのイベント。 **location** プロパティが各イベントにコピーされます。

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>例 4

* **入力**:2 つの JSON オブジェクトを含む入れ子になった JSON 配列を含む JSON オブジェクト。 この入力は、グローバル プロパティが複雑な JSON オブジェクトによって表現できることを示しています。

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **出力**:2 つのイベント。

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>次の手順

- Time Series Insights エクスプローラーで[自分の環境を表示](https://insights.timeseries.azure.com)します。

- [IoT Hub デバイス メッセージ](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)の詳細を参照してください。
