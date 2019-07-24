---
title: Azure Time Series Insights 環境へのイベントの送信 | Microsoft Docs
description: イベント ハブを構成し、サンプル アプリケーションを実行して、Azure Time Series Insights で表示できるイベントをプッシュする方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 424476b91537c60a6d7f0f9a854453353bf98633
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557021"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>イベント ハブを使用して Time Series Insights 環境にイベントを送信する

この記事では、Azure Event Hubs でイベント ハブを作成および構成し、サンプル アプリケーションを実行してイベントをプッシュする方法について説明します。 JSON 形式のイベントを含む既存のイベント ハブがある場合は、このチュートリアルをスキップし、[Azure Time Series Insights](./time-series-insights-update-create-environment.md) で環境を表示してください。

## <a name="configure-an-event-hub"></a>イベント ハブを構成する

1. イベント ハブを作成する方法については、[Event Hubs のドキュメント](https://docs.microsoft.com/azure/event-hubs/)をご覧ください。
1. 検索ボックスで、**Event Hubs** を検索します。 返された一覧で、**[Event Hubs]** を選択します。
1. 自分のイベント ハブを選択します。
1. イベント ハブを作成すると、実際にはイベント ハブの名前空間が作成されます。 名前空間内にまだイベント ハブを作成していない場合は、メニューの **[エンティティ]** でイベント ハブを作成します。  

    ![イベント ハブの一覧][1]

1. イベント ハブを作成した後、イベント ハブの一覧でそれを選択します。
1. メニューで、**[エンティティ]** の **[Event Hubs]** を選択します。
1. イベント ハブの名前を選択して構成します。
1. **[エンティティ]** で **[コンシューマー グループ]** を選択し、**[コンシューマー グループ]** を選択します。

    ![コンシューマー グループの作成][2]

1. Time Series Insights のイベント ソースで排他的に使用されるコンシューマー グループを作成していることを確認します。

    > [!IMPORTANT]
    > このコンシューマー グループがその他のサービス (Azure Stream Analytics ジョブや別の Time Series Insights 環境など) で使用されていないことを確認してください。 コンシューマー グループが他のサービスで使用されている場合、この環境と他のサービスの両方で読み取り操作が悪影響を受けます。 コンシューマー グループとして **$Default** を使用した場合、他の閲覧者がそのコンシューマー グループを再利用する可能性があります。

1. メニューの **[設定]** で **[共有アクセス ポリシー]** を選択してから、**[追加]** を選択します。

    ![[共有アクセス ポリシー] を選択してから、[追加] ボタンを選択する][3]

1. **[新しい共有アクセス ポリシーの追加]** ウィンドウで、**MySendPolicy** という名前の共有アクセス ポリシーを作成します。 後で示す C# の例では、この共有アクセス ポリシーを使用してイベントを送信します。

    ![[ポリシー名] ボックスに「MySendPolicy」と入力する][4]

1. **[要求]** で、**[送信]** チェック ボックスをオンにします。

## <a name="add-a-time-series-insights-instance"></a>Time Series Insights のインスタンスを追加する

Time Series Insights の更新では、インスタンスを使用して、受信したテレメトリ データにコンテキスト データが追加されます。 データはクエリ時に**タイム シリーズ ID** を使用して結合されます。 記事の後半で使用するサンプルの風力発電プロジェクトの**タイム シリーズ ID** は、**Id** です。Time Series Insight のインスタンスと**タイム シリーズ ID** について詳しくは、「[タイム シリーズ モデル](./time-series-insights-update-tsm.md)」をご覧ください。

### <a name="create-a-time-series-insights-event-source"></a>Time Series Insights のイベント ソースを作成する

1. イベント ソースを作成していない場合は、[イベント ソースを作成する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)手順を実行します。

1. `timeSeriesId` の値を設定します。 **タイム シリーズ ID** について詳しくは、「[タイム シリーズ モデル](./time-series-insights-update-tsm.md)」をご覧ください。

### <a name="push-events"></a>イベントをプッシュする (風力発電サンプル)

1. 検索バーで「**Event Hubs**」を検索します。 返された一覧で、**[Event Hubs]** を選択します。

1. 自分のイベント ハブを選択します。

1. **[共有アクセス ポリシー]** > **RootManageSharedAccessKey** に移動します。 **[接続文字列 - 主キー]** の値をコピーします。

    ![主キーの接続文字列の値をコピーする][5]

1. https://tsiclientsample.azurewebsites.net/windFarmGen.html にアクセスします。 その URL では、シミュレートされた風力発電デバイスが実行されます。
1. Web ページの **[Event Hub Connection String]\(イベント ハブ接続文字列\)** ボックスに、「[イベントをプッシュする](#push-events)」でコピーした接続文字列を貼り付けます。
  
    ![[Event Hub Connection String]\(イベント ハブ接続文字列\) ボックスに主キーの接続文字列を貼り付ける][6]

1. **[Click to start]\(クリックして開始\)** を選択します。 シミュレーターで、直接使用できるインスタンスの JSON が生成されます。

1. Azure portal でイベント ハブに戻ります。 **[概要]** ページに、イベント ハブによって受信された新しいイベントが表示されます。

    ![イベント ハブのメトリックが表示されているイベント ハブの [概要] ページ][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>サポートされている JSON 構造

### <a name="sample-1"></a>サンプル 1

#### <a name="input"></a>入力

簡単な JSON オブジェクト:

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>出力:1 つのイベント

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>サンプル 2

#### <a name="input"></a>入力

2 つの JSON オブジェクトを含む JSON 配列。 各 JSON オブジェクトがイベントに変換されます。

```json
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

#### <a name="output-two-events"></a>出力:2 つのイベント

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>サンプル 3

#### <a name="input"></a>入力

2 つの JSON オブジェクトを含む入れ子になった JSON 配列を含む JSON オブジェクト。

```json
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

#### <a name="output-two-events"></a>出力:2 つのイベント

**location** プロパティが各イベントにコピーされます。

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>サンプル 4

#### <a name="input"></a>入力

2 つの JSON オブジェクトを含む入れ子になった JSON 配列を含む JSON オブジェクト。 この入力は、グローバル プロパティが複雑な JSON オブジェクトによって表現できることを示しています。

```json
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

#### <a name="output-two-events"></a>出力:2 つのイベント

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Time Series Insights エクスプローラーで自分の環境を表示する](https://insights.timeseries.azure.com)

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
