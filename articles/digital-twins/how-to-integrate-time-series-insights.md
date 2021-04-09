---
title: Azure Time Series Insights と統合する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins から Azure Time Series Insights へのイベント ルートを設定する方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6aeb7489b455840eeca0a8e1967c7e6e2ed50b7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199902"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure Digital Twins と Azure Time Series Insights を統合する

この記事では、Azure Digital Twins と [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) を統合する方法について説明します。

この記事で説明されているソリューションを使用すると、IoT ソリューションに関する履歴データを収集して分析することができます。 Azure Digital Twins は、Time Series Insights に送信する前に複数のデータ ストリームを関連付け、情報を標準化できるため、Time Series Insights にデータを供給するのに最適です。 

## <a name="prerequisites"></a>前提条件

Time Series Insights との関係を設定できるためには、事前に **Azure Digital Twins のインスタンス** が必要です。 Time Series Insights でデータが追跡されていることを確認するためにツイン情報を何回か更新する必要があるため、データに基づいてデジタル ツイン情報を更新できるようにこのインスタンスを設定する必要があります。 

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

ツインの状態が更新されるたびに、Azure Digital Twins インスタンスで[ツイン更新イベント](how-to-interpret-event-data.md)を生成できます。 このセクションでは、これらの更新イベントを後続の処理のために Azure [Event Hubs](../event-hubs/event-hubs-about.md) に転送する Azure Digital Twins の [**イベント ルート**](concepts-route-events.md)を作成します。

Azure Digital Twins の "[*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)" に関するページでは、温度計を使用して部屋を表すデジタル ツインの温度属性を更新するシナリオの手順が説明されています。 このパターンは、IoT デバイスからのテレメトリの転送ではなく、ツインの更新に依存しています。これにより、Time Series Insights のロジックを更新する必要なしに、基になるデータ ソースを柔軟に変更できます。

1. 最初に、Azure Digital Twins インスタンスからイベントを受信するイベント ハブ名前空間を作成します。 以下の Azure CLI の手順を使用するか、Azure portal を使用することができます: 「[*クイック スタート:Azure portal を使用したイベント ハブの作成*](../event-hubs/event-hubs-create.md)」。 Event Hubs がサポートされているリージョンを確認するには、"[*リージョン別の利用可能な Azure 製品*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)" に関するページを参照してください。

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. ツインの変更イベントを受け取るためのイベント ハブを名前空間内に作成します。 イベント ハブの名前を指定します。

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. 送信および受信のアクセス許可を持つ[承認規則](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create)を作成します。 規則の名前を指定します。

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. イベント ハブを Azure Digital Twins インスタンスにリンクする Azure Digital Twins [エンドポイント](concepts-route-events.md#create-an-endpoint)を作成します。

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. ツイン更新イベントをエンドポイントに送信するための[ルート](concepts-route-events.md#create-an-event-route)を Azure Digital Twins で作成します。 このルートのフィルターでは、エンドポイントに渡されるツイン更新メッセージのみが許可されます。

    >[!NOTE]
    >Cloud Shell には現在、`az dt route`、`az dt model`、`az dt twin` の各コマンド グループに影響する **既知の問題** があります。
    >
    >解決するには、コマンドを実行する前に Cloud Shell で `az login` を実行するか、Cloud Shell ではなく[ローカル CLI](/cli/azure/install-azure-cli) を使用します。 この件の詳細については、「[*トラブルシューティング: Azure Digital Twins の既知の問題*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)」を参照してください。

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

この記事の後半で別のイベント ハブを作成するためにもう一度使用するので、先に進む前に、"*Event Hubs 名前空間*" と "*リソース グループ*" を記録しておきます。

## <a name="create-a-function-in-azure"></a>Azure で関数を作成する

次に、Azure Functions を使用して、関数アプリ内に **Event Hubs によってトリガーされる関数** を作成します。 エンドツーエンドのチュートリアルで作成した関数アプリを使用することも ("[*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)")、自分で作成することもできます。 

この関数を使用して、それらのツイン更新イベントを、JSON Patch ドキュメントとしての元の形式から、ツインから更新および追加される値だけが含まれる JSON オブジェクトに変換します。

Azure Functions で Event Hubs を使用する方法の詳細については、"[*Azure Functions に対する Azure Event Hubs トリガー*](../azure-functions/functions-bindings-event-hubs-trigger.md)" に関するページを参照してください。

発行した関数アプリ内で **ProcessDTUpdatetoTSI** という名前の新しい関数を追加します。コードは次のとおりです。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>`dotnet add package` コマンドまたは Visual Studio NuGet パッケージ マネージャーを使用して、プロジェクトにパッケージを追加することが必要になる場合があります。

次に、新しい Azure 関数を **発行** します。 この方法の手順については、「[*方法: データを処理するための Azure 関数の設定*](how-to-create-azure-function.md#publish-the-function-app-to-azure)に関するページを参照してください。

今後、この関数で作成した JSON オブジェクトが 2 番目のイベント ハブに送信され、それが Time Series Insights に接続されます。 そのイベント ハブは、次のセクションで作成します。

後ほど、この関数で独自のイベント ハブに接続するために使用するいくつかの環境変数も設定します。

## <a name="send-telemetry-to-an-event-hub"></a>テレメトリをイベント ハブに送信する

次に、2 つ目のイベント ハブを作成し、出力をそのイベント ハブにストリーミングするように関数を構成します。 その後、このイベント ハブを Time Series Insights に接続します。

### <a name="create-an-event-hub"></a>イベント ハブの作成

2 番目のイベント ハブを作成するには、以下の Azure CLI の手順を使用するか、Azure portal を使用することができます: 「[*クイック スタート:Azure portal を使用したイベント ハブの作成*](../event-hubs/event-hubs-create.md)」。

1. この記事の前半で説明したように、"*イベント ハブ名前空間*" と "*リソース グループ*" 名を準備します

2. 新しいイベント ハブを作成します。 イベント ハブの名前を指定します。

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. 送信および受信のアクセス許可を持つ[承認規則](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create)を作成します。 規則の名前を指定します。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>関数を構成する

次に、前に作成したイベント ハブに対する接続文字列が含まれる環境変数を、前の関数アプリで設定する必要があります。

### <a name="set-the-twins-event-hub-connection-string"></a>Twins イベント ハブ接続文字列を設定する

1. Twins ハブに対して上で作成した承認規則を使用して、Twins [イベント ハブ接続文字列](../event-hubs/event-hubs-get-connection-string.md)を取得します。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. その結果の *primaryConnectionString* 値を使用して、接続文字列が含まれるアプリ設定を関数アプリに作成します:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Time Series Insights イベント ハブの接続文字列を設定する

1. Time Series Insights ハブに対して上で作成した承認規則を使用して、TSI [イベント ハブ接続文字列](../event-hubs/event-hubs-get-connection-string.md)を取得します。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. その結果の *primaryConnectionString* 値を使用して、接続文字列が含まれるアプリ設定を関数アプリに作成します:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Azure Time Series Insights インスタンスを作成して接続する

次に、2 番目の (TSI) イベント ハブからデータを受信するように Time Series Insights インスタンスを設定します。 以下の手順のようにします。このプロセスの詳細については、"[*チュートリアル: Azure Time Series Insights Gen2 PAYG 環境の設定*](../time-series-insights/tutorials-set-up-tsi-environment.md)" に関するページを参照してください。

1. Azure portal で、Time Series Insights 環境の作成を始めます。 
    1. **Gen2 (L1)** 価格レベルを選択します。
    2. この環境に対する **時系列 ID** を選択する必要があります。 時系列 ID は、Time Series Insights でデータを検索するために使用し、最大 3 つの値を使用できます。 このチュートリアルでは、 **$dtId** を使用できます ID 値の選択について詳しくは、「[*時系列 ID の選択のベスト プラクティス*](../time-series-insights/how-to-select-tsid.md)」を参照してください。
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Time Series Insights 環境用の作成ポータル UX。サブスクリプション、リソース グループ、および場所をそれぞれのドロップダウンから選択し、環境の名前を選択します。" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Time Series Insights 環境用の作成ポータル UX。Gen2(L1) 価格レベルが選択されていて、時系列 ID プロパティの名前は $dtId である" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. **Next:イベント ソース** を選択し、前述の TSI イベント ハブ情報を選択します。 新しい Event Hubs コンシューマー グループを作成する必要もあります。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Time Series Insights 環境イベント ソースの作成ポータル UX。上記のイベント ハブ情報を使用してイベント ソースを作成している。また、新しいコンシュー マーグループも作成している。" lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Azure Digital Twins への IoT データの送信を始める

Time Series Insights へのデータの送信を始めるには、Azure Digital Twins のデジタル ツイン プロパティの変化するデータ値での更新を始める必要があります。 [az dt twin update](/cli/azure/ext/azure-iot/dt/twin#ext-azure-iot-az-dt-twin-update) コマンドを使用します。

エンドツーエンドのチュートリアル (「[*チュートリアル: エンドツーエンドのソリューションの接続*](tutorial-end-to-end.md)」) を使用して環境をセットアップしている場合は、サンプルから *DeviceSimulator* プロジェクトを実行することにより、シミュレートされた IoT データの送信を開始できます。 手順については、チュートリアルの「[*シミュレーションを構成して実行する*](tutorial-end-to-end.md#configure-and-run-the-simulation)」セクションを参照してください。

## <a name="visualize-your-data-in-time-series-insights"></a>Time Series Insights でデータを視覚化する

現在は、データが Time Series Insights インスタンスに流れているはずであり、分析する準備ができました。 次の手順のようにして、受信したデータを調べます。

1. [Azure portal](https://portal.azure.com) で、Time Series Insights 環境を開きます (ポータルの検索バーで環境の名前を検索できます)。 インスタンスの概要で示されている *Time Series Insights Explorer URL* にアクセスします。
    
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