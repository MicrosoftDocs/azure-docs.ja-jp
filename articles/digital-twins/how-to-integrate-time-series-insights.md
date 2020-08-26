---
title: Azure Time Series Insights と統合する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins から Azure Time Series Insights へのイベント ルートを設定する方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bda07d0e14ddc630bde4fdc9c869704154c1e6cc
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236354"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure Digital Twins と Azure Time Series Insights を統合する

この記事では、Azure Digital Twins と [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) を統合する方法について説明します。

この記事で説明されているソリューションを使用すると、IoT ソリューションに関する履歴データを収集して分析することができます。 Azure Digital Twins は、Time Series Insights に送信する前に複数のデータ ストリームを関連付け、情報を標準化できるため、Time Series Insights にデータを供給するのに最適です。 

## <a name="prerequisites"></a>前提条件

Time Series Insights との関係を設定できるためには、事前に **Azure Digital Twins のインスタンス**が必要です。 Time Series Insights でデータが追跡されていることを確認するためにツイン情報を何回か更新する必要があるため、データに基づいてデジタル ツイン情報を更新できるようにこのインスタンスを設定する必要があります。 

これをまだ設定していない場合、作成手順については Azure Digital Twins の "[*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)" に関するページを参照してください。 そのチュートリアルでは、仮想 IoT デバイスで動作してデジタル ツインの更新をトリガーする Azure Digital Twins インスタンスを設定する手順が示されています。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

次のパスを使用して、Time Series Insights を Azure Digital Twins に接続します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Time Series Insights が強調して示されている、エンドツーエンドのシナリオでの Azure サービスのビュー" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>ルートを作成してツイン更新通知にフィルターを適用する

ツインの状態が更新されるたびに、Azure Digital Twins インスタンスで[ツイン更新イベント](how-to-interpret-event-data.md)を生成できます。 このセクションでは、これらの更新イベントを後続の処理のために Azure [Event Hubs](../event-hubs/event-hubs-about.md) に転送する Azure Digital Twins の[**イベント ルート**](concepts-route-events.md)を作成します。

Azure Digital Twins の "[*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)" に関するページでは、温度計を使用して部屋を表すデジタル ツインの温度属性を更新するシナリオの手順が説明されています。 このパターンは、IoT デバイスからのテレメトリの転送ではなく、ツインの更新に依存しています。これにより、Time Series Insights のロジックを更新する必要なしに、基になるデータ ソースを柔軟に変更できます。

1. 最初に、Azure Digital Twins インスタンスからイベントを受信するイベント ハブ名前空間を作成します。 以下の Azure CLI の手順を使用するか、Azure portal を使用することができます: 「[*クイック スタート:Azure portal を使用したイベント ハブの作成*](../event-hubs/event-hubs-create.md)」。

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. 名前空間内にイベント ハブを作成します。

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. 送信および受信のアクセス許可を持つ[承認規則](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create)を作成します。

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. イベント グリッド トピックを Azure Digital Twins インスタンスにリンクする Azure Digital Twins [エンドポイント](concepts-route-events.md#create-an-endpoint)を作成します。

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. ツイン更新イベントをエンドポイントに送信するための[ルート](concepts-route-events.md#create-an-event-route)を Azure Digital Twins で作成します。 このルートのフィルターでは、エンドポイントに渡されるツイン更新メッセージのみが許可されます。

    ```azurecli
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

この記事の後半で別のイベント ハブを作成するためにもう一度使用するので、先に進む前に、"*Event Hubs 名前空間*" と "*リソース グループ*" を記録しておきます。

## <a name="create-an-azure-function"></a>Azure 関数の作成 

次に、関数アプリ内に Event Hubs によってトリガーされる関数を作成します。 エンドツーエンドのチュートリアルで作成した関数アプリを使用することも ("[*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)")、自分で作成することもできます。 

この関数を使用して、それらのツイン更新イベントを、JSON Patch ドキュメントとしての元の形式から、ツインから更新および追加される値だけが含まれる JSON オブジェクトに変換します。

Azure Functions で Event Hubs を使用する方法の詳細については、"[*Azure Functions に対する Azure Event Hubs トリガー*](../azure-functions/functions-bindings-event-hubs-trigger.md)" に関するページを参照してください。

発行された関数アプリ内で、関数のコードを次のコードに置き換えます。

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-event-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("tsi-event-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

ここからは、関数で作成した JSON オブジェクトを、Time Series Insights に接続されている 2 番目のイベント ハブに送信します。

後ほど、この関数で独自のイベント ハブに接続するために使用するいくつかの環境変数も設定します。

## <a name="send-telemetry-to-an-event-hub"></a>テレメトリをイベント ハブに送信する

次に、2 つ目のイベント ハブを作成し、出力をそのイベント ハブにストリーミングするように関数を構成します。 その後、このイベント ハブを Time Series Insights に接続します。

### <a name="create-an-event-hub"></a>イベント ハブの作成

2 番目のイベント ハブを作成するには、以下の Azure CLI の手順を使用するか、Azure portal を使用することができます: 「[*クイック スタート:Azure portal を使用したイベント ハブの作成*](../event-hubs/event-hubs-create.md)」。

1. この記事の前半で説明したように、"*イベント ハブ名前空間*" と "*リソース グループ*" 名を準備します

2. 新しいイベント ハブを作成します
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. 送信および受信のアクセス許可を持つ[承認規則](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create)を作成します
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>関数を構成する

次に、前に作成したイベント ハブに対する接続文字列が含まれる環境変数を、前の関数アプリで設定する必要があります。

### <a name="set-the-twins-event-hub-connection-string"></a>Twins イベント ハブ接続文字列を設定する

1. Twins ハブに対して上で作成した承認規則を使用して、Twins [イベント ハブ接続文字列](../event-hubs/event-hubs-get-connection-string.md)を取得します。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. 取得した接続文字列を使用して、接続文字列が含まれるアプリ設定を関数アプリに作成します。

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Time Series Insights イベント ハブの接続文字列を設定する

1. Time Series Insights ハブに対して上で作成した承認規則を使用して、TSI [イベント ハブ接続文字列](../event-hubs/event-hubs-get-connection-string.md)を取得します。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. 関数アプリで、接続文字列が含まれるアプリ設定を作成します。

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Azure Time Series Insights インスタンスを作成して接続する

次に、2 番目のイベント ハブからデータを受信するように Time Series Insights インスタンスを設定します。 以下の手順のようにします。このプロセスの詳細については、"[*チュートリアル: Azure Time Series Insights Gen2 PAYG 環境の設定*](../time-series-insights/tutorials-set-up-tsi-environment.md)" に関するページを参照してください。

1. Azure portal で、Time Series Insights リソースの作成を始めます。 
    1. **[PAYG (プレビュー)]** 価格レベルを選択します。
    2. この環境に対する**時系列 ID** を選択する必要があります。 時系列 ID は、Time Series Insights でデータを検索するために使用し、最大 3 つの値を使用できます。 このチュートリアルでは、 **$dtId** を使用できます ID 値の選択について詳しくは、「[*時系列 ID の選択のベスト プラクティス*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)」を参照してください。
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Time Series Insights 環境用の作成ポータル UX。PAYG (プレビュー) 価格レベルが選択されていて、時系列 ID プロパティの名前は $dtId である":::

2. **[Next:イベント ソース]** を選択し、上記の Event Hubs 情報を選択します。 新しい Event Hubs コンシューマー グループを作成する必要もあります。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Time Series Insights 環境イベント ソースの作成ポータル UX。上記のイベント ハブ情報を使用してイベント ソースを作成している。また、新しいコンシュー マーグループも作成している。":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Azure Digital Twins への IoT データの送信を始める

Time Series Insights へのデータの送信を始めるには、Azure Digital Twins のデジタル ツイン プロパティの変化するデータ値での更新を始める必要があります。 [az dt twin update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) コマンドを使用します。

エンドツーエンドのチュートリアル (「[*チュートリアル: エンドツーエンドのソリューションの接続*](tutorial-end-to-end.md)」) を使用して環境をセットアップしている場合は、サンプルから *DeviceSimulator* プロジェクトを実行することにより、シミュレートされた IoT データの送信を開始できます。 手順については、チュートリアルの「[*シミュレーションを構成して実行する*](tutorial-end-to-end.md#configure-and-run-the-simulation)」セクションを参照してください。

## <a name="visualize-your-data-in-time-series-insights"></a>Time Series Insights でデータを視覚化する

現在は、データが Time Series Insights インスタンスに流れているはずであり、分析する準備ができました。 次の手順のようにして、受信したデータを調べます。

1. [Azure portal](https://portal.azure.com) で、Time Series Insights インスタンスを開きます (ポータルの検索バーでインスタンスの名前を検索できます)。 インスタンスの概要で示されている *Time Series Insights Explorer URL* にアクセスします。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Time Series Insights 環境の概要タブで Time Series Insights Explorer の URL を選択する":::

2. エクスプローラーの左側に、Azure Digital Twins からの 3 つのツインが表示されます。 _**thermostat67**_ を選択し、**temperature** を選択して、 **[追加]** をクリックします。

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="**thermostat67**、**temperature**、**[追加]** の順に選択する":::

3. 次に示すように、サーモスタットからの初期温度の測定値が表示されます。 同じ温度の測定値が *room21* と *floor1* に対して更新され、それらのデータ ストリームを同時に見ることができます。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="初期温度データが TSI Explorer でグラフ化される。68 から 85 までのランダムな値である":::

4. シミュレーションを長時間実行させておくと、表示は次のようになります。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="各ツインの気温データが、異なる色の 3 本の平行線でグラフ化される。":::

## <a name="next-steps"></a>次のステップ

デジタル ツインは、Time Series Insights にフラット階層として既定で格納されますが、モデル情報と組織の複数レベルの階層を使用して強化することができます。 このプロセスの詳細については、以下を参照してください。 

* [*チュートリアル:モデルを定義して適用する*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Azure Digital Twins に既に格納されているモデルとグラフ データを使用して、この情報を自動的に提供するカスタム ロジックを作成できます。 ツイン グラフの情報の管理、アップグレード、および取得について詳しくは、以下の参照情報をご覧ください。

* [*方法: デジタル ツインを管理する*](./how-to-manage-twin.md)
* [*方法: ツイン グラフにクエリを実行する*](./how-to-query-graph.md)