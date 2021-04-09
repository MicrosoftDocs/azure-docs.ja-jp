---
title: Azure Digital Twins と Time Series Insights 間のモデルの同期 | Microsoft Docs
description: ADT の資産モデルを Azure TSI の資産モデルに変換するために使用されるベスト プラクティスとツール
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 5e8e5367daa532fa0cc36a18cd28b382e7cd8f78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682904"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Azure Digital Twins と Time Series Insights Gen2 間のモデルの同期

この記事では、Azure Digital Twins (ADT) の資産モデルを Azure Time Series Insights (TSI) の資産モデルに変換するために使用されるベスト プラクティスとツールについて説明します。  この記事は、Azure Digital Twins と Azure Time Series Insights との統合について説明する 2 部構成のチュートリアル シリーズのパート 2 です。 Azure Digital Twins と Time Series Insights の統合により、Digital Twins のテレメトリと計算されたプロパティの履歴をアーカイブおよび追跡できるようになります。 このチュートリアル シリーズは、Time Series Insights と Azure Digital Twins との統合に取り組んでいる開発者を対象としています。 パート 1 では、[Azure Digital Twins から Time Series Insights に実際のタイム シリーズ データを取り込むデータ パイプラインの確立](../digital-twins/how-to-integrate-time-series-insights.md)について説明し、このチュートリアル シリーズのパート 2 では、Azure Digital Twins と Time Series Insights 間の資産モデルの同期について説明します。 このチュートリアルでは、タイム シリーズ ID (TS ID) の名前付け規則の選択と確立、およびタイム シリーズ モデル (TSM) での階層の手動による確立のベスト プラクティスについて説明します。

## <a name="choosing-a-time-series-id"></a>タイム シリーズ ID の選択

タイム シリーズ ID は、Time Series Insights で資産を特定するために使用される一意識別子です。 タイム シリーズ データ (時間と値のペアである、フィールドからのテレメトリ) は、TSID の下に一覧表示された変数を使用して表されます。 Azure Digital Twins では、ツインのプロパティとテレメトリを使用して、ツインの状態と、ツインによって生成される測定値をそれぞれ表します。 現在の TSM の設計の時点では、TSID は一意である必要があります。 Azure Digital Twins でツインのツイン ID を使用するか、プロパティまたはテレメトリの名前と組み合わせて使用すると、TSM で常に一意の TS ID が作成されます。 一般的なケースでは、**_`<Twin ID>`_** は TSID になり、プロパティおよびテレメトリの名前は TSM の変数になります。 しかし、**_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** などの複合キー形式を使用して、Time Series Insights の資産階層をフラット化することが望ましいユース ケースがあります。 後のケースについて説明する例を見てみましょう。 ある建物にツインとしてモデリングされ、ツイン ID が Room22 の部屋があるとします。 その温度設定プロパティは **_TSID Room22_TempSetting_** として変換され、温度の測定値は TSM で **_Room22_TempMea_** に変換されます。

[![タイム シリーズ ID の選択](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>タイム シリーズのコンテキスト化

Time Series Insights でのデータ (ほとんどの場合、本質的に空間) のコンテキスト化は、資産階層を介して実現され、Time Series Insights Explorer のツリー ビューを介してデータを簡単にナビゲートするためにも使用されます。 タイム シリーズの種類、および階層は、Time Series Insights のタイム シリーズ モデル (TSM) を使用して定義されます。 TSM の種類は変数を定義するのに役立ちますが、階層レベルおよびインスタンス フィールドの値は、Time Series Insights Explorer でツリー ビューを構築するために使用されます。 TSM の詳細については、[オンラインの Time Series Insights ドキュメント](./concepts-model-overview.md)を参照してください。

Azure Digital Twins では、資産間の接続はツイン リレーションシップを使用して表されます。 ツイン リレーションシップは、接続された資産の単なるグラフです。 しかし、Time Series Insight では、資産間のリレーションシップは本質的に階層となります。 つまり、資産によって親子の種類のリレーションシップが共有され、ツリー構造を使用して表されます。 Azure Digital Twins のリレーションシップ情報を Time Series Insights 階層に変換するには、Azure Digital Twins から関連する階層リレーションシップを選択する必要があります。 Azure Digital Twins では、デジタル ツイン定義言語 (DTDL) と呼ばれるオープン スタンダードのモデリング言語を使用します。 DTDL モデルでは、JSON-LD と呼ばれる JSON の一種を使用して記述されます。 仕様の詳細については、[DTDL のドキュメント](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)を参照してください。

[![資産間の接続](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Time Series Insights のツリー構造への Azure Digital Twins のグラフ表現の変換

チュートリアルの以下のセクションでは、Time Series Insights のツリー構造に Azure Digital Twins のグラフ構造を手動で変換するいくつかの主要なシナリオについて説明します。

サンプル システム: このチュートリアルでは次の例を使用して、後述の概念について説明します。 これは、フロアが 1 つで部屋が 2 つあるシンプルな架空の建物管理システムです。 サーモスタットは 3 つあります。各部屋に 1 つずつと、もう 1 つはフロアに共通のものです。 さらに、部屋はパイプでつながっており、Room21 から Room22 への水流を測定する水流メーターもあります。 ツイン間の空間リレーションシップを見ると、両方の種類のリレーションシップがあります。

1. 最も一般的な、階層リレーションシップ。 たとえば、Building40 -> Floor01 -> FloorTS* -> Temperature です
1. それほど一般的ではない、循環リレーションシップ。 たとえば、Building40 から開始すると、FlowMtr は 2 つの異なるパスを介してトレースできます。

   1. Building40 -> Floor01 -> Room21 -> FlowMtr* -> Flow
   1. Building40 -> Floor01 -> Room22 -> FlowMtr* -> Flow  
      ここで、"Flow" は Room21 と Room22 の間の水流を測定する実際のテレメトリです

> [!Note]
>
> `*` FloorTS は FloorThermoStat の略で、FlowMtr は Flow Meter の略であり、通常は TSID として選択されます。 Temperature と Flow は、Time Series Insights で変数と呼ばれる未加工のテレメトリです。

Time Series Insights の現在の制限により、1 つの資産を複数の分岐で表せないと仮定し、以下のセクションでは Time Series Insights での階層および循環リレーションシップのモデリングについて説明します。

## <a name="case-1-hierarchical-parent-child-relationship"></a>ケース 1: 階層 (親子) リレーションシップ

これは、ツイン間の最も一般的な種類のリレーションシップです。 純粋な親子リレーションシップのモデリングについては、次の図で説明します。 インスタンス フィールドと TSID は、以下に示すようにツイン ID から派生します。 Time Series Insights Explorer を使用してインスタンス フィールドを手動で更新することはできますが、以下の「API を使用したインスタンス フィールドの更新」というセクションでは、Azure Digital Twins でのモデル変更のリッスンと、Azure Functions を使用した Time Series Insights でのインスタンス フィールドの更新について説明しています。

[![ツイン ID のマッピング](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![ツイン ID のマッピング 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>ケース 2: 循環リレーションシップ

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Time Series Insights の単一階層リレーションシップとの Azure Digital Twins の循環リレーションシップ

TSID が一意である必要があり、1 つの階層でのみ表すことができると仮定した場合、このケースでは、ツインの _‘Room21’_ のすぐ下の _‘Flow’_ という名前のテレメトリで _‘FlowMtr’_ が示されます。 今後、Time Series Insights で TSM のタイム シリーズの複数表現がサポートできるようになったときに、テレメトリ _‘Flow’_ は _‘Room 21’_ と _‘Room 22’_ の下に示されるようになります

次のスクリーンショットは、TSM のインスタンス フィールドへの Azure Digital Twins のツイン ID のマッピングと、Time Series Insights の結果の階層を示しています。

[![Azure Digital Twins でのツイン ID のマッピング](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Time Series Insights の複数の階層との Azure Digital Twins の循環リレーションシップ (重複を使用)

チュートリアルのパート 1 では、クライアント プログラム (Azure 関数) が、IoT Hub またはその他のイベント ソースから Azure Digital Twins へのテレメトリ データの転送にどのように役立つかについて説明します。 このアプローチでは、同じクライアント プログラムを使用して、親ツインの関連プロパティを更新することをお勧めします。 示されている例では、IoT Hub から FlowMtr テレメトリを読み取り、FlowMtr ツインでプロパティ "Flow" を更新している間に、ソースの考えられるすべての親ツインの対応するプロパティをプログラムで更新することもできます。 この例では、Room21 の "outflowmea" (‘outflow’ リレーションシップを使用して特定される) プロパティと Room22 の "inflowmea" プロパティです。  以下のスクリーンショットは、Time Series Insights Explorer の最終的なユーザー エクスペリエンスを示しています。 このアプローチを取ると、データが重複することに注意する必要があります。

[![Time Series Insights Explorer](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

以下のコード スニペットは、Azure Digital Twins API を使用して、クライアント アプリケーションでツイン リレーションシップをどのようにナビゲートできたかを示しています。

> [!Note]
>
> このコード スニペットの例では、読者がこのチュートリアルの[パート 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) をよく理解しており、このコード変更が "ProcessHubToDTEvents" 関数内で行われたことを前提とします。

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>API を使用したインスタンス フィールドの更新

チュートリアルのこのセクションでは、ツインの作成、削除あるいはツイン間のリレーションシップの変更などの Azure Digital Twins でのモデル変更のリッスンと、Time Series Insights モデル API を使用したインスタンス フィールドおよび階層のプログラムによる更新について説明します。 この Time Series Insights モデルを更新する方法は、通常、Azure Functions を通じて実現されます。 Azure Digital Twins では、ツインの追加や削除などのイベント通知を Event Hubs などのダウンストリーム サービスにルーティングでき、その後、Azure Functions にフィードできます。 イベントのルーティングとフィルター処理の詳細については、[こちら](../digital-twins/how-to-manage-routes-portal.md)を参照してください。  このセクションの残りの部分では、Azure Functions で Time Series Insights モデル API を使用し、Azure Digital Twins でのツインの追加 (モデル変更の一種) に応じて Time Series Insights モデルを更新する方法について説明します。

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>ツインの追加イベント通知の受信と特定

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Time Series Insights クライアントの作成とインスタンスの詳細の追加

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>インスタンスへの階層情報の適用

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>次の手順

このシリーズの 3 番目のチュートリアルでは、Time Series Insights API を使用して Azure Digital Twins からの履歴データに対してクエリを実行する方法を示します。 これは進行中の作業であり、セクションは準備ができたときに更新されます。 それまでの間、読者には、[Time Series Insights データ クエリ API のドキュメント](./concepts-query-overview.md)を参照することをお勧めします。
