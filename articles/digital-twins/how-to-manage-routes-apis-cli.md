---
title: エンドポイントとルートを管理する (API と CLI)
titleSuffix: Azure Digital Twins
description: Azure Digital Twins データのエンドポイントとイベント ルートを設定し、管理する方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 27b745353521a44733c46170a5f5952c194c2343
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293508"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Azure Digital Twins のエンドポイントとルートを管理する (API と CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins では、ダウンストリームのサービスや接続されているコンピューティング リソースに[イベント通知](how-to-interpret-event-data.md)をルーティングすることができます。 これを行うには、まず、イベントを受信できる**エンドポイント**を設定します。 そのうえで、Azure Digital Twins によって生成されるどのイベントをどのエンドポイントに配信するかを指定する[**イベント ルート**](concepts-route-events.md)を作成します。

エンドポイントとルートは、[EventRoutes API](how-to-use-apis-sdks.md)、[.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用して管理できます。 この記事では、これらのメカニズムを通じてエンドポイントとルートを作成する手順を説明します。

また、[Azure portal](https://portal.azure.com) を使用して管理することもできます。 ポータルを使用したバージョンの記事については、[*エンドポイントとルートをポータルで管理する方法*](how-to-manage-routes-portal.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure アカウント**が必要となります ([こちら](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)から無料で設定できます)。
* ご利用の Azure サブスクリプションに **Azure Digital Twins インスタンス**が必要となります。 まだインスタンスをお持ちでない場合は、インスタンスを作成してください。その手順については、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-scripted.md)に関するページを参照してください。 セットアップ中、次の値をメモしておいてください。後でこの記事の中で使用します。
    - インスタンス名
    - Resource group
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins のエンドポイントの作成

インスタンスに作成できるエンドポイントのタイプは次のとおりです。
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

さまざまなエンドポイントのタイプの詳細については、"[*Azure メッセージング サービスの選択*](../event-grid/compare-messaging-services.md)" に関する記事を参照してください。

エンドポイントを Azure Digital Twins にリンクするには、エンドポイントに使用する Event Grid トピック、イベント ハブ、または Service Bus が既に存在している必要があります。 

### <a name="create-an-event-grid-endpoint"></a>Event Grid のエンドポイントを作成する

次の例は、Azure CLI を使用して Event Grid タイプのエンドポイントを作成する方法を示しています。 [Azure Cloud Shell](https://shell.azure.com) を使用するか、[CLI をローカルにインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)してください。

まず、Event Grid トピックを作成します。 次のコマンドを使用することができます。または、Event Grid の "*カスタム イベント*" に関するクイックスタートの[「*カスタム トピックの作成*」セクション](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)にアクセスして、詳しい手順を参照してください。

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Azure CLI のコマンドに渡すことのできる Azure リージョン名のリストを出力するには、次のコマンドを実行します。
> ```azurecli
> az account list-locations -o table
> ```

トピックを作成したら、次のコマンドを使用して Azure Digital Twins にリンクできます。

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

これにより、`--endpoint-name` 引数に指定した名前で、Event Grid トピックを Azure Digital Twins 内のエンドポイントとして使用できるようになりました。 通常は、**イベント ルート**のターゲットとしてこの名前を使用します。これは、[この記事の中で後から](#event-routes-with-apis-and-the-c-sdk) Azure Digital Twins サービス API を使用して作成します。

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Event Hubs または Service Bus のエンドポイントを作成する

Event Hubs または Service Bus のエンドポイントを作成する手順は、前述した Event Grid のエンドポイントを作成する手順と似ています。

まず、エンドポイントとして使用するリソースを作成します。 必要なものは以下のとおりです。
* Service Bus:"_Service Bus 名前空間_"、"_Service Bus トピック_"、"_承認規則_"
* Event Hubs:_Event Hubs 名前空間_、_イベント ハブ_、_承認規則_

さらに、次のコマンドを使用して、Azure Digital Twins にエンドポイントを作成します。 

* Service Bus トピック エンドポイントを追加する (事前に作成された Service Bus リソースが必要)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Event Hubs エンドポイントを追加する (事前に作成された Event Hubs リソースが必要)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="event-routes-with-apis-and-the-c-sdk"></a>イベント ルート (API と C# SDK を使用)

Azure Digital Twins からエンドポイントに実際にデータを送信するには、**イベント ルート**を定義する必要があります。 Azure Digital Twins の **EventRoutes API** を使用すると、開発者はシステム全体およびダウンストリーム サービスへのイベント フローを結び付けることができます。 イベント ルートの詳細については、[*Azure Digital Twins イベントのルーティングの概念*](concepts-route-events.md)に関する記事を参照してください。

このセクションのサンプルでは C# SDK を使用しています。

**前提条件**:ルートの作成に進む前に、この記事の前出の説明に従ってエンドポイントを作成する必要があります。 エンドポイントの設定が完了したら、イベント ルートの作成に進むことができます。

>[!NOTE]
>エンドポイントを最近デプロイした場合は、新しいイベント ルートでそれらの使用を試みる**前に**、それらのデプロイが完了していることを確認します。 エンドポイントの準備ができていないためにルートのデプロイが失敗する場合は、数分待ってからやり直してください。
>
> このフローをスクリプト化する場合は、ルートの設定に進む前に、エンドポイント サービスのデプロイが完了するまでの 2 ～ 3 分の待機時間を組み込んで、このことを考慮に入れておくことを推奨します。

### <a name="create-an-event-route"></a>イベント ルートを作成する

イベント ルートは、データ プレーン API を使用して定義します。 

ルート定義にはこれらの要素を含めることができます。
* 使用するルート名
* 使用するエンドポイントの名前
* エンドポイントに送信されるイベントを定義するフィルター 

ルート名がない場合、メッセージは Azure Digital Twins の外部にルーティングされません。 ルート名があり、フィルターが `true` の場合は、すべてのメッセージがエンドポイントにルーティングされます。 ルート名があり、別のフィルターを追加した場合は、そのフィルターに基づいてメッセージがフィルター処理されます。

1 つのルートで、複数の通知とイベントの種類を選択できるようにする必要があります。 

`CreateEventRoute` は、イベント ルートを追加するために使用される SDK 呼び出しです。 その使用例を次に示します。

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

### <a name="event-route-sample-code"></a>イベント ルートのサンプル コード

次のコード サンプルは、イベント ルートを作成、一覧表示、削除する方法を示しています。
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>イベントのフィルター処理

フィルター処理を行わない場合、エンドポイントは Azure Digital Twins からさまざまなイベントを受信します。
* Azure Digital Twins サービス API を使用して[デジタル ツイン](concepts-twins-graph.md)で発生するテレメトリ
* Azure Digital Twins インスタンスでツインのプロパティが変更されたときに発生する、ツインのプロパティ変更通知
* ツインまたはリレーションシップが作成または削除されたときに発生するライフサイクル イベント
* Azure Digital Twins インスタンスで構成された[モデル](concepts-models.md)が追加または削除されたときに発生するモデル変更イベント

イベント ルートにエンドポイントの**フィルター**を追加することで、送信されるイベントを制限できます。

フィルターを追加するには、*https://{YourHost}/EventRoutes/myNewRoute?api-version=2020-05-31-preview* に対して、次の本文を含む PUT 要求を使用します。

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

サポートされているルート フィルターを次に示します。 「*フィルター テキスト スキーマ*」列の情報を使用して、上記の要求本文の `<filter-text>` プレースホルダーを置き換えてください。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>CLI を使用したエンドポイントとルートの管理

エンドポイントとルートは、Azure Digital Twins CLI を使用して管理することもできます。 CLI の使用および利用できるコマンドについて詳しくは、"[*Azure Digital Twins CLI を使用する方法*](how-to-use-cli.md)" に関するページを参照してください。

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>次のステップ

受信できるさまざまな種類のイベント メッセージについてご確認ください。
* [*方法: イベント データを解釈する*](how-to-interpret-event-data.md)
