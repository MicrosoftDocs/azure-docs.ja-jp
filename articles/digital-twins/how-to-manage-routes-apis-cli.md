---
title: エンドポイントとルートを管理する (API と CLI)
titleSuffix: Azure Digital Twins
description: Azure Digital Twins データのエンドポイントとイベント ルートを設定し、管理する方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d25a429873ccf8b546c0919456c97e64445f184c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071700"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Azure Digital Twins のエンドポイントとルートを管理する (API と CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins では、ダウンストリームのサービスや接続されているコンピューティング リソースに[イベント通知](how-to-interpret-event-data.md)をルーティングすることができます。 これを行うには、まず、イベントを受信できる **エンドポイント** を設定します。 そのうえで、Azure Digital Twins によって生成されるどのイベントをどのエンドポイントに配信するかを指定する [**イベント ルート**](concepts-route-events.md)を作成します。

この記事では、[REST API](/rest/api/azure-digitaltwins/)、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)、および [Azure Digital Twins CLI](how-to-use-cli.md) を使用して、エンドポイントとルートを作成する手順について説明します。

別の方法として、エンドポイントとルートは、[Azure portal](https://portal.azure.com) で管理することもできます。 ポータルを使用したバージョンの記事については、[*エンドポイントとルートをポータルで管理する方法*](how-to-manage-routes-portal.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- **Azure アカウント** が必要となります ([こちら](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)から無料で設定できます)。
- ご利用の Azure サブスクリプションに **Azure Digital Twins インスタンス** が必要となります。 まだインスタンスをお持ちでない場合は、インスタンスを作成してください。その手順については、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-cli.md)に関するページを参照してください。 セットアップ中、次の値をメモしておいてください。後でこの記事の中で使用します。
    - インスタンス名
    - Resource group

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins のエンドポイントの作成

インスタンスに作成できるエンドポイントのタイプは次のとおりです。
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

さまざまなエンドポイントのタイプの詳細については、"[*Azure メッセージング サービスの選択*](../event-grid/compare-messaging-services.md)" に関する記事を参照してください。

このセクションでは、Azure CLI を使用してこれらのエンドポイントを作成する方法について説明します。 [DigitalTwinsEndpoint コントロール プレーン API](/rest/api/digital-twins/controlplane/endpoints) を使用してエンドポイントを管理することもできます。

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>エンドポイントを作成する

エンドポイント リソースを作成したら、Azure Digital Twins エンドポイントにそれらを使用できます。 次の例では、[Azure Digital Twins CLI](how-to-use-cli.md) の [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) コマンドを使用してエンドポイントを作成する方法を示します。 コマンドのプレースホルダーは、独自のリソースの詳細に置き換えます。

Event Grid エンドポイントを作成するには:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Event Hubs エンドポイントを作成するには (キーベース認証):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Service Bus トピック エンドポイントを作成するには (キーベース認証):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

これらのコマンドが正常に実行されると、イベント グリッド、イベント ハブ、または Service Bus トピックが、Azure Digital Twins 内のエンドポイントとして、`--endpoint-name` 引数で指定した名前で使用できるようになります。 通常、この名前を、**イベント ルート** ([この記事の中で後で](#create-an-event-route)作成します) のターゲットとして使用します。

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>ID ベースの認証を使用してエンドポイントを作成する

また、ID ベースの認証を使用するエンドポイントを作成し、そのエンドポイントを[マネージド ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview) で使用することもできます。 このオプションは、種類が Event Hubs と Service Bus のエンドポイントでのみ使用できます (Event Grid ではサポートされていません)。

この種類のエンドポイントを作成する CLI コマンドは次のようになります。 コマンドのプレースホルダーには次の値を設定する必要があります。
* Azure Digital Twins インスタンスの Azure リソース ID
* エンドポイント名
* エンドポイントの種類
* エンドポイント リソースの名前空間
* イベント ハブまたは Service Bus トピックの名前
* Azure Digital Twins インスタンスの場所

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>配信不能処理を行うエンドポイントを作成する

エンドポイントでは、一定期間内にイベントを配信できない場合や、イベントの配信を一定回数試行した後も配信できない場合、未配信イベントをストレージ アカウントに送信できます。 このプロセスは **配信不能処理** と呼ばれます。

配信不能処理が有効なエンドポイントは、Azure Digital Twins [CLI](how-to-use-cli.md) または[コントロール プレーン API](how-to-use-apis-sdks.md#overview-control-plane-apis) を使用して設定できます。

配信不能処理の詳細については、[*イベント ルートの概念*](concepts-route-events.md#dead-letter-events)に関するページを参照してください。 配信不能処理を構成してエンドポイントを設定する方法については、このセクションの残りの部分を読み進めます。

#### <a name="set-up-storage-resources"></a>ストレージ リソースを設定する

配信不能の場所を設定するには、ご使用の Azure アカウントに[コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)が設定された[ストレージ アカウント](../storage/common/storage-account-create.md?tabs=azure-portal)が必要です。 

後でエンドポイントを作成するときに、このコンテナーの URI を指定します。 配信不能の場所は、[SAS トークン](../storage/common/storage-sas-overview.md)を含むコンテナー URI としてエンドポイントに提供されます。 このトークンには、ストレージ アカウント内の宛先コンテナーに対する `write` アクセス許可が必要です。 完全な形式の **配信不能 SAS URI** は、`https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` という形式になります。

以下の手順に従って、Azure アカウントでこれらのストレージ リソースを設定し、次のセクションでエンドポイント接続の設定を準備します。

1. 「[*ストレージ アカウントを作成する*](../storage/common/storage-account-create.md?tabs=azure-portal)」の手順に従って、Azure サブスクリプションに **ストレージ アカウント** を作成します。 後で使用するために、そのストレージ アカウント名を書き留めておきます。
2. 「[*コンテナーを作成する*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)」の手順に従って、新しいストレージ アカウント内に **コンテナー** を作成します。 後で使用するために、そのコンテナー名を書き留めておきます。
3. 次に、ストレージ アカウント用に **SAS トークン** を作成して、エンドポイントからアクセスするときにこれを使用できるようにします。 まず、[Azure portal](https://ms.portal.azure.com/#home) でご自分のストレージ アカウントに移動します (ポータルの検索バーを使って名前で見つけることができます)。
4. ストレージ アカウントのページで、左側のナビゲーション バーの _[Shared Access Signature]_ リンクを選択し、SAS トークンの設定を開始します。

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Azure portal の [ストレージ アカウント] ページ" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. *Shared Access Signature のページ* の *[使用できるサービス]* と *[使用できるリソースの種類]* で、目的の設定を選択します。 カテゴリごとに少なくとも 1 つのボックスを選択する必要があります。 *[与えられているアクセス許可]* で **[書き込み]** を選択します (必要に応じて他のアクセス許可を選択することもできます)。
1. 残りの設定には、任意の値を設定します。
1. 完了後、 _[SAS と接続文字列を生成する]_ ボタンを選択して SAS トークンを生成します。 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Azure portal の [ストレージ アカウント] ページで SAS トークンを生成するためのすべての設定の選択が示されている。[SAS と接続文字列を生成する] ボタンが強調表示されている" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. これにより、同じページの下部の選択した設定の下に、いくつかの SAS と接続文字列の値が生成されます。 下にスクロールして値を表示し、 *[クリップボードにコピー]* アイコンを使用して **SAS トークン** 値をコピーします。 後で使用するために保存します。

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="配信不能メッセージのシークレットで使用する SAS トークンをコピーします。" lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>配信不能エンドポイントを作成する

配信不能処理が有効なエンドポイントを作成するには、次の配信不能パラメーターを、[Azure Digital Twins CLI](how-to-use-cli.md) の [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) コマンドに追加します。

パラメーターの値は、[前のセクション](#set-up-storage-resources)で収集したストレージ アカウント名、コンテナー名、SAS トークンで構成された **配信不能 SAS URI** です。 このパラメーターにより、キーベースの認証を使用するエンドポイントが作成されます。

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

配信不能処理が有効になっている目的の種類のエンドポイントを作成するには、前の「[*エンドポイントを作成する*](#create-the-endpoint)」セクションのエンドポイント作成コマンドの末尾に、このパラメーターを追加します。

または、CLI の代わりに [Azure Digital Twins コントロール プレーン API](how-to-use-apis-sdks.md#overview-control-plane-apis) を使用して、配信不能エンドポイントを作成することもできます。 これを行うには、[DigitalTwinsEndpoint のドキュメント](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)を参照し、要求を構成して配信不能パラメーターを追加する方法を確認してください。

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>ID ベースの認証を使用して配信不能エンドポイントを作成する

また、ID ベースの認証を使用する配信不能エンドポイントを作成し、そのエンドポイントを[マネージド ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview) で使用することもできます。 このオプションは、種類が Event Hubs と Service Bus のエンドポイントでのみ使用できます (Event Grid ではサポートされていません)。

この種類のエンドポイントを作成するには、前と同じ CLI コマンドを使用して [ID ベースの認証を使用するエンドポイントを作成する](#create-an-endpoint-with-identity-based-authentication)ときに、JSON ペイロードで `deadLetterUri` の追加フィールドを指定します。

コマンドのプレースホルダーには次の値を設定する必要があります。
* Azure Digital Twins インスタンスの Azure リソース ID
* エンドポイント名
* エンドポイントの種類
* エンドポイント リソースの名前空間
* イベント ハブまたは Service Bus トピックの名前
* **配信不能 SAS URI** の詳細: ストレージ アカウント名、コンテナー名
* Azure Digital Twins インスタンスの場所

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>メッセージ ストレージ スキーマ

配信不能処理を行うエンドポイントが設定されると、配信不能メッセージが次の形式でストレージ アカウントに格納されます。

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

配信不能メッセージは、元のエンドポイントに配信されることを意図していた元のイベントのスキーマと一致します。

次に、[ツイン作成通知](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications)の配信不能メッセージの例を示します。

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>イベント ルートを作成する

**前提条件**:ルートの作成に進む前に、この記事の前出の説明に従ってエンドポイントを作成する必要があります。 エンドポイントの設定が完了したら、イベント ルートの作成に進むことができます。

> [!NOTE]
> エンドポイントを最近デプロイした場合は、新しいイベント ルートでそれらの使用を試みる **前に**、それらのデプロイが完了していることを確認します。 エンドポイントの準備ができていないためにルートのデプロイが失敗する場合は、数分待ってからやり直してください。
>
> このフローをスクリプト化する場合は、ルートの設定に進む前に、エンドポイント サービスのデプロイが完了するまでの 2 ～ 3 分の待機時間を組み込んで、このことを考慮に入れておくことを推奨します。

Azure Digital Twins からエンドポイントに実際にデータを送信するには、**イベント ルート** を定義する必要があります。 システム全体で、および下流のサービスに対して、イベント フローを接続するためにイベント ルートが使用されます。

ルート定義にはこれらの要素を含めることができます。
* 使用するルート名
* 使用するエンドポイントの名前
* エンドポイントに送信されるイベントを定義するフィルター 

ルート名がない場合、メッセージは Azure Digital Twins の外部にルーティングされません。 ルート名があり、フィルターが `true` の場合は、すべてのメッセージがエンドポイントにルーティングされます。 ルート名があり、別のフィルターを追加した場合は、そのフィルターに基づいてメッセージがフィルター処理されます。

1 つのルートで、複数の通知とイベントの種類を選択できるようにする必要があります。 

イベント ルートを作成するには、Azure Digital Twins の [**EventRoutes** データ プレーン API](/rest/api/digital-twins/dataplane/eventroutes) または [**az dt route** CLI コマンド](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true)を使用できます。 このセクションの残りの部分では、作成プロセスについて説明します。

### <a name="create-routes-with-the-apis-and-c-sdk"></a>API と C# SDK を使用してルートを作成する

イベント ルートを定義する方法の 1 つは、[データ プレーン API](how-to-use-apis-sdks.md#overview-data-plane-apis) を使用することです。 このセクションのサンプルでは、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) を使用しています。

`CreateOrReplaceEventRouteAsync` は、イベント ルートを追加するために使用される SDK 呼び出しです。 その使用例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

#### <a name="event-route-sample-sdk-code"></a>イベント ルートのサンプル SDK コード

次のサンプル メソッドでは、C# SDK を使用してイベント ルートを作成、一覧表示、削除する方法を示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>CLI を使用してルートを作成する

Azure Digital Twins CLI の [az dt route](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true) コマンドを使用して、ルートを管理することもできます。 

CLI の使用および利用できるコマンドについて詳しくは、"[*Azure Digital Twins CLI を使用する方法*](how-to-use-cli.md)" に関するページを参照してください。

## <a name="filter-events"></a>イベントのフィルター処理

フィルター処理を行わない場合、エンドポイントは Azure Digital Twins からさまざまなイベントを受信します。
* Azure Digital Twins サービス API を使用して[デジタル ツイン](concepts-twins-graph.md)で発生するテレメトリ
* Azure Digital Twins インスタンスでツインのプロパティが変更されたときに発生する、ツインのプロパティ変更通知
* ツインまたはリレーションシップが作成または削除されたときに発生するライフサイクル イベント

イベント ルートにエンドポイントの **フィルター** を追加することで、送信されるイベントを制限できます。

フィルターを追加するために、次の本文を含む PUT 要求を *https://{Your-azure-digital-twins-hostname}/eventRoutes/{event-route-name}?api-version=2020-10-31* に対して使用できます。

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

サポートされているルート フィルターを次に示します。 「*フィルター テキスト スキーマ*」列の情報を使用して、上記の要求本文の `<filter-text>` プレースホルダーを置き換えてください。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>次のステップ

受信できるさまざまな種類のイベント メッセージについてご確認ください。
* [*方法: イベント データを解釈する*](how-to-interpret-event-data.md)
